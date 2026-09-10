# 7Ps Marketing AI — Roadmap de Implementação V1

## Objetivo

Este roadmap transforma as decisões arquiteturais da 7Ps Marketing AI em uma sequência de implementação controlada, priorizando validação, baixo custo e reversibilidade.

A implementação deve acontecer por checkpoints. Não avançar para um checkpoint dependente enquanto o anterior não estiver validado.

---

## Fase 0 — Base e segurança

### 0.1 Repositório

- usar `7psdigitaltech-eng/vibestack-openclaw` como repositório oficial da adaptação;
- manter histórico do upstream como referência;
- documentar todas as decisões relevantes antes de mudanças estruturais;
- nunca versionar credenciais reais.

### 0.2 Infraestrutura

- Docker Desktop como host;
- WSL2 apenas como infraestrutura do Docker Desktop;
- não usar Docker-in-Docker;
- manter stacks independentes por projeto;
- persistência no disco `D:` sempre que aplicável.

### 0.3 Estrutura local alvo

```text
D:\Docker\7ps-marketing\
├── repo\
└── data\
    ├── hermes\
    ├── ollama\
    ├── knowledge\
    ├── artifacts\
    └── backups\
```

A estrutura final poderá ser ajustada durante a implementação, desde que preserve separação entre código e dados persistentes.

### Critério de aceite

- Docker saudável;
- diretórios criados;
- repositório clonado na localização definitiva;
- nenhuma credencial versionada.

---

## Fase 1 — Simplificação da stack para Hermes

### Objetivo

Subir uma stack mínima com Hermes como runtime principal.

### Mudanças esperadas

- `INSTALL_OPENCLAW=false`;
- `INSTALL_HERMES=true`;
- `INSTALL_EVOLUTION=false`;
- `COMPOSE_PROFILES=` vazio;
- manter integrações especializadas disponíveis no código;
- não configurar Meta Ads ainda.

### Revisões necessárias

- Dockerfile;
- docker-compose.yml;
- entrypoint.sh;
- .env.example;
- install.sh;
- documentação da raiz.

### Critério de aceite

- build concluído;
- container principal permanece saudável;
- gateway Hermes sobe corretamente;
- dashboard Hermes acessível localmente;
- restart do container preserva configuração.

---

## Fase 2 — Ollama e benchmark local

### Objetivo

Validar a viabilidade da operação com LLM local.

### Primeiro conjunto de testes

Modelos candidatos:

- `qwen3:4b`;
- `llama3.2:3b`.

### Métricas

Registrar para cada modelo:

- RAM usada;
- CPU;
- tempo de primeira resposta;
- tempo de resposta médio;
- qualidade de português;
- capacidade de seguir instruções;
- tool calling;
- capacidade de resumir;
- capacidade de planejar;
- desempenho em delegação;
- estabilidade com contexto maior.

### Critério de aceite

Escolher pelo menos um modelo que consiga operar o Diretor e realizar chamadas de ferramentas de forma utilizável.

Se nenhum modelo local atender, documentar resultado antes de avaliar opção híbrida/nuvem.

---

## Fase 3 — Telegram

### Objetivo

Criar o canal principal de interação com o Diretor.

### Regras

- apenas o Diretor recebe Telegram na V1;
- usar allowlist de usuário;
- não enviar segredos por chat;
- preferir configuração de segurança mínima necessária;
- testar texto e anexos conforme suporte disponível.

### Critério de aceite

- mensagem Telegram chega ao Diretor;
- resposta retorna ao usuário;
- usuário não autorizado é bloqueado;
- reinicialização não exige reconfiguração manual do bot.

---

## Fase 4 — Profile Diretor

### Objetivo

Criar a primeira identidade operacional permanente.

### Entregáveis

- `SOUL.md`;
- descrição do profile;
- skills iniciais;
- regras de autonomia;
- regras de approval;
- acesso a Kanban;
- acesso à knowledge base;
- comportamento via Telegram.

### Testes

1. pedido simples;
2. pedido ambíguo;
3. solicitação que exige pesquisa;
4. solicitação que exige criação de tarefa;
5. solicitação que deve pedir aprovação.

### Critério de aceite

Diretor consegue funcionar como porta única sem tentar executar indevidamente tarefas especializadas.

---

## Fase 5 — Profiles especialistas

Criar, nesta ordem sugerida:

1. `estrategista`;
2. `analista`;
3. `copywriter`;
4. `conteudo`;
5. `criativo`;
6. `gestor-trafego`.

Para cada profile registrar:

- missão;
- descrição;
- SOUL;
- inputs;
- outputs;
- skills;
- tools;
- autonomia;
- handoffs permitidos;
- handoffs proibidos;
- gates de aprovação.

### Critério de aceite

Cada profile deve produzir saída compatível com seu papel e evitar assumir funções exclusivas de outro profile.

---

## Fase 6 — Knowledge Base 7Ps

### Objetivo

Criar uma fonte oficial de verdade compartilhada.

### Primeiros domínios

- empresa;
- posicionamento;
- marca;
- público;
- ofertas;
- produtos/serviços;
- concorrentes;
- campanhas;
- conteúdo;
- resultados.

### Regras

- fatos oficiais não devem depender apenas da memória de agents;
- documento oficial prevalece sobre lembrança do modelo;
- toda informação sensível deve ficar fora do Git quando necessário;
- dados obsoletos devem ser marcados ou removidos.

### Critério de aceite

Dois profiles diferentes respondem de forma consistente sobre fatos fundamentais da 7Ps consultando a mesma fonte.

---

## Fase 7 — Skills da metodologia 7Ps

### Objetivo

Converter os processos de marketing em conhecimento operacional reutilizável.

### Primeiro lote

- diagnóstico-marketing;
- definicao-icp;
- pesquisa-concorrencia;
- posicionamento;
- proposta-valor;
- planejamento-campanha;
- calendario-editorial;
- copy-anuncios;
- copy-landing-page;
- briefing-criativo;
- analise-campanha.

### Regra

Skill não deve ser um prompt genérico. Deve definir um procedimento verificável.

Cada skill deve ter, quando aplicável:

- quando usar;
- inputs necessários;
- procedimento;
- formato de saída;
- critérios de qualidade;
- erros comuns;
- verificação.

### Critério de aceite

O mesmo tipo de tarefa executado em sessões diferentes mantém estrutura e qualidade semelhantes.

---

## Fase 8 — Kanban multiagente

### Objetivo

Validar coordenação persistente entre profiles.

### Primeiro workflow recomendado

Projeto: campanha institucional da própria 7Ps.

```text
Estratégia
  ↓
Copy + Criativo + Conteúdo
  ↓
Estrutura de mídia
  ↓
Plano de métricas
  ↓
Revisão do Diretor
```

### O que testar

- dependências;
- estados;
- bloqueio;
- comentário;
- handoff;
- reinício do container durante o fluxo;
- retomada;
- revisão;
- conclusão.

### Critério de aceite

Uma campanha atravessa pelo menos quatro profiles sem depender de repasse manual de contexto pelo usuário.

---

## Fase 9 — Subagentes

### Objetivo

Usar delegação temporária sem sobrecarregar o host.

### Configuração inicial

- concorrência máxima: 1 ou 2;
- mesmo modelo do agente pai inicialmente;
- aumentar somente após benchmark.

### Casos de teste

- pesquisa concorrencial;
- comparação de ofertas;
- revisão paralela de copies;
- levantamento de ideias.

### Critério de aceite

Delegações terminam de forma previsível sem saturar CPU/RAM e retornam sínteses úteis ao profile pai.

---

## Fase 10 — Ferramentas criativas

### Objetivo

Validar o princípio de que a LLM orquestra ferramentas especializadas para elevar a qualidade da execução.

### Prioridade

1. Media Editor;
2. Higgsfield;
3. AtlasCloud;
4. outras ferramentas se justificadas.

### Avaliar

- custo real;
- necessidade de API key;
- limites gratuitos;
- qualidade;
- capacidade de automação;
- persistência de autenticação;
- direitos e restrições de uso.

### Critério de aceite

O profile Criativo consegue transformar briefing em pelo menos um ativo utilizável usando ferramenta especializada.

---

## Fase 11 — Gestor de Tráfego em modo preparação

### Objetivo

Deixar o profile operacional antes de configurar Meta Ads.

### Deve conseguir

- criar estrutura de campanha;
- recomendar objetivo;
- propor públicos;
- definir nomenclatura;
- organizar copy + criativos;
- montar checklist de publicação;
- propor budget;
- preparar plano de mensuração.

### Não pode ainda

- publicar;
- alterar campanha real;
- movimentar orçamento;
- excluir recursos.

### Critério de aceite

Receber uma estratégia pronta e devolver um plano de mídia executável manualmente.

---

## Fase 12 — Meta Ads posterior

Esta fase fica explicitamente fora do primeiro bring-up.

Quando iniciada:

- configurar credenciais;
- limitar escopo;
- validar leitura primeiro;
- validar sandbox/conta segura quando possível;
- somente depois habilitar escrita;
- implementar approval gate;
- registrar auditoria das ações.

A progressão recomendada é:

```text
sem credencial
  ↓
credencial + leitura
  ↓
leitura validada
  ↓
escrita limitada
  ↓
aprovação humana
  ↓
autonomia progressiva
```

---

## Fase 13 — Observabilidade e backup

Antes de considerar a V1 madura:

- documentar logs importantes;
- definir backup do HERMES_HOME;
- backup do Kanban;
- backup da knowledge base;
- backup das configurações;
- documentar restore;
- testar restart;
- testar rebuild;
- testar perda do container sem perda de estado persistente.

### Critério de aceite

É possível recriar a stack e recuperar o estado operacional a partir dos dados persistentes e do Git.

---

# Critérios gerais de V1 concluída

A V1 pode ser considerada validada quando:

1. Hermes opera como runtime único;
2. modelo local atende qualidade mínima;
3. Telegram é canal funcional do Diretor;
4. sete profiles estão configurados;
5. knowledge base é fonte oficial de verdade;
6. skills principais existem e são reutilizáveis;
7. Kanban coordena workflow multiagente;
8. subagentes funcionam sem saturar host;
9. Criativo usa ferramenta especializada;
10. Gestor está pronto em modo planejamento;
11. Meta continua desabilitado operacionalmente;
12. estado crítico sobrevive a restart/rebuild;
13. documentação acompanha a implementação real.

---

# Fora do escopo da V1

- multi-clínica;
- multi-tenant completo;
- integração CliniSmart;
- automação de publicação Meta sem aprovação;
- operação para clientes externos;
- cobrança/SaaS;
- painel próprio da 7Ps;
- substituição total do dashboard Hermes.

Esses pontos serão avaliados na V2 após validação interna.
