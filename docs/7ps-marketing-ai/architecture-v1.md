# Arquitetura V1 — 7Ps Marketing AI

## 1. Objetivo arquitetural

Construir a V1 como uma aplicação independente dentro do Docker Desktop, evitando Docker-in-Docker e evitando que projetos futuros compartilhem estado de forma acidental.

A arquitetura deve permitir que a 7Ps rode vários projetos no mesmo computador sem misturar containers, redes, volumes, configurações ou dados.

## 2. Host de aplicações

O **Docker Desktop** será o host de aplicações local.

Estrutura conceitual:

```text
Windows 10
└── Docker Desktop / WSL2
    ├── stack 7ps-marketing-ai
    ├── stack clinismart
    ├── stack n8n
    ├── stack projeto-x
    └── debian-vps (laboratório Linux opcional)
```

Cada projeto futuro deve preferencialmente possuir:

- diretório próprio;
- `docker-compose.yml` próprio;
- `.env` próprio;
- rede Docker própria;
- volumes/bind mounts próprios;
- nomes de serviço próprios;
- portas de host explicitamente planejadas.

O fato de vários containers usarem a mesma porta **interna** não cria conflito. Conflito só ocorre quando tentam publicar a mesma porta no host.

Exemplo:

```text
app-a: container 3000 -> host 3000
app-b: container 3000 -> host 3001
```

## 3. Decisão: não usar Docker-in-Docker como padrão

Não será usada a topologia:

```text
Docker Desktop
└── debian-vps
    └── Docker Engine
        └── 7ps-marketing-ai
```

Motivos:

- adiciona uma camada operacional desnecessária;
- complica volumes e rede;
- complica observabilidade e debugging;
- aumenta risco ao usar `--privileged` ou Docker socket;
- dificulta manutenção futura;
- não traz ganho real para a V1.

O `debian-vps` criado anteriormente pode permanecer como **laboratório Linux genérico**, mas não será o host pai da agência.

## 4. Base Linux da aplicação

O projeto herdado já constrói sua própria imagem Linux baseada em Bookworm. Portanto, a agência 7Ps terá seu ambiente Linux dentro do próprio container da aplicação.

A intenção da V1 é evoluir esse container para um runtime Hermes-centric.

Topologia alvo:

```text
Docker Desktop
└── 7ps-marketing-ai
    ├── Hermes Agent
    ├── Ollama
    ├── skills 7Ps
    ├── knowledge 7Ps
    ├── MCPs/tools
    ├── gateway Telegram
    └── dados persistentes
```

Serviços auxiliares poderão ser separados em containers irmãos quando houver benefício operacional.

## 5. Organização física no Windows

Diretório sugerido:

```text
D:\Docker\7ps-marketing-ai\
├── repo\
├── data\
│   ├── hermes\
│   ├── ollama\
│   ├── knowledge\
│   └── generated\
├── backups\
└── logs\
```

Objetivos:

- usar o disco D: como armazenamento principal de dados persistentes;
- manter o projeto versionado separado de dados de runtime;
- facilitar backup e inspeção;
- reduzir uso do disco C:;
- evitar perder dados ao recriar containers.

Os caminhos finais deverão ser validados no momento da implementação do Compose.

## 6. Stack V1

### 6.1 Runtime de agentes

```text
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
```

Hermes será o processo principal.

### 6.2 Backend de modelo

```text
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
```

Ollama será a primeira opção.

Critérios de seleção do modelo:

- funcionar dentro da RAM disponível;
- tool calling consistente;
- contexto adequado a workflows agentic;
- boa resposta em português;
- desempenho aceitável em planejamento, copy e análise;
- velocidade prática suficiente para uso via Telegram.

A escolha do modelo será baseada em benchmark, não apenas em tamanho ou popularidade.

### 6.3 Canal

Telegram será o gateway humano da V1.

Fluxo:

```text
Eduardo
  ↓
Telegram Bot
  ↓
Gateway Hermes
  ↓
Profile Diretor
```

Os demais profiles não precisam de bot individual na V1.

### 6.4 WhatsApp / Evolution

Desativados na V1.

```text
INSTALL_EVOLUTION=false
COMPOSE_PROFILES=
```

Os serviços Evolution Go + Postgres não devem subir no fluxo inicial.

### 6.5 Ads

Meta Ads e Google Ads permanecerão como ferramentas/integradores previstos.

Estado inicial:

```text
Meta Ads:
  instalada/preservada: SIM
  credenciais: NÃO
  escrita real: BLOQUEADA

Google Ads:
  instalada/preservada: SIM
  credenciais: NÃO
  escrita real: BLOQUEADA
```

A decisão de preservar as integrações evita reconstruir posteriormente a arquitetura do Gestor de Tráfego.

## 7. Ferramentas especializadas

A arquitetura considera ferramentas externas como extensões de capacidade do modelo.

Camadas:

```text
Hermes / LLM
   ↓
Skills
   ↓
Tools / MCPs / CLIs
   ↓
Serviços externos
```

Ferramentas previstas:

- Meta Ads CLI/MCP;
- Google Ads SDK/MCP;
- Higgsfield;
- AtlasCloud;
- Media Editor;
- terminal/filesystem;
- ferramentas de pesquisa/web disponíveis ao Hermes;
- outras integrações que provarem valor.

Não é requisito que todas tenham credenciais na primeira instalação.

## 8. Estados de integração

Toda integração deve ser classificada em três eixos independentes:

### Instalado

O binário, pacote ou MCP está disponível no runtime.

### Configurado

Credenciais e parâmetros necessários foram fornecidos.

### Autorizado

A política da 7Ps permite que determinado profile faça aquela operação.

Exemplo:

```text
Meta Ads MCP
INSTALADO:   SIM
CONFIGURADO: NÃO
AUTORIZADO:  NÃO PARA WRITE
```

No futuro:

```text
Meta Ads MCP
INSTALADO:   SIM
CONFIGURADO: SIM
AUTORIZADO:
  Analista -> READ
  Estrategista -> READ
  Gestor -> READ/WRITE dentro da política
  Diretor -> sem WRITE direto
```

## 9. Persistência

Dados que precisam sobreviver a rebuild/restart:

- configuração Hermes;
- profiles;
- `SOUL.md`;
- skills customizadas;
- memórias;
- sessões relevantes;
- Kanban;
- cron jobs;
- modelos Ollama;
- knowledge base 7Ps;
- arquivos gerados e aprovados;
- credenciais em storage apropriado;
- logs necessários à auditoria.

Código da imagem e dependências instaladas no build não devem depender do filesystem efêmero do container para persistência.

## 10. Isolamento entre projetos Docker

Para evitar mistura entre projetos futuros:

1. cada stack deve ter `COMPOSE_PROJECT_NAME` ou diretório Compose distinto;
2. evitar `container_name` global quando não houver necessidade;
3. usar volumes nomeados/pastas com prefixos do projeto;
4. não compartilhar `.env` entre projetos;
5. mapear portas explicitamente;
6. não montar `D:\Docker` inteiro dentro de um container;
7. montar apenas caminhos necessários;
8. segredos de um projeto não devem ser expostos a outro;
9. redes externas compartilhadas só devem existir por decisão explícita.

## 11. Segurança operacional

A V1 terá autonomia gradual.

Operações de baixo risco podem ser automáticas, como:

- pesquisa;
- criação de rascunho;
- organização de tarefas;
- análise de conteúdo;
- geração de alternativas;
- leitura de métricas.

Operações de maior risco devem possuir aprovação/regras específicas, como:

- gastar orçamento;
- publicar campanha;
- alterar campanha ativa;
- excluir ativos;
- enviar conteúdo em nome da empresa para terceiros;
- modificar credenciais;
- alterar infraestrutura.

## 12. Observabilidade mínima

A implementação deve permitir diagnosticar:

- container status;
- saúde do Hermes;
- saúde do Ollama;
- modelo carregado;
- profile responsável;
- tarefas Kanban em execução/bloqueadas;
- falhas de tools;
- falhas de gateway Telegram;
- uso de memória/CPU;
- tempos de resposta;
- erros de delegação.

## 13. Caminho para V2

A arquitetura atual não implementará multi-clínica, mas deve evitar escolhas que impeçam isso.

Recursos Hermes que podem ser utilizados na V2:

- boards separados;
- tenants;
- workspaces separados;
- knowledge bases separadas;
- profiles específicos quando necessário;
- credenciais por cliente;
- integração com CliniSmart.

A decisão exata de isolamento da V2 será feita somente depois da validação da V1.
