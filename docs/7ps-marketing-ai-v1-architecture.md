# 7Ps Marketing AI — Arquitetura V1

## 1. Objetivo

Este documento registra as decisões de arquitetura para a adaptação do projeto `vibestack-openclaw` à realidade da 7Ps Digital Tech.

O fork deixa de ser tratado apenas como uma stack de "agência de tráfego com IA" e passa a ser a base da **7Ps Marketing AI**, uma agência de marketing com IA operada inicialmente para a própria 7Ps.

A V1 tem dois objetivos principais:

1. validar internamente a metodologia de marketing, os agentes, os fluxos e a capacidade operacional;
2. criar uma base técnica evolutiva para, em uma V2, integrar essa metodologia ao ecossistema voltado para clínicas.

A V1 **não será multi-clínica**. O escopo inicial será exclusivamente a operação de marketing da 7Ps.

---

## 2. Princípios da V1

A arquitetura deve seguir os princípios abaixo:

- custo operacional mínimo;
- prioridade para modelos locais;
- especialização por agentes, skills e ferramentas;
- separação clara entre conhecimento, raciocínio, execução e integrações;
- persistência de memória e tarefas;
- capacidade de auditoria e retomada;
- evolução incremental;
- manter integrações futuras preparadas, sem exigir configuração imediata;
- evitar complexidade de infraestrutura sem ganho prático.

---

## 3. Infraestrutura escolhida

### 3.1 Host

O **Docker Desktop no Windows** será o host principal das aplicações.

Não será usado Docker-in-Docker para hospedar a 7Ps Marketing AI.

A arquitetura desejada é:

```text
Windows 10
└── Docker Desktop / WSL2
    ├── 7ps-marketing-ai
    ├── debian-vps (laboratório genérico, opcional)
    ├── outros projetos futuros
    └── outros serviços futuros
```

Cada projeto será tratado como uma stack Docker independente, com:

- rede própria;
- volumes/bind mounts próprios;
- arquivo `.env` próprio;
- containers próprios;
- portas externas controladas;
- dados persistentes separados.

### 3.2 Organização em disco

Diretório-base recomendado:

```text
D:\Docker\
├── 7ps-marketing\
│   ├── repo\
│   └── data\
├── clinismart\
│   ├── repo\
│   └── data\
├── outros-projetos\
└── backups\
```

A separação física e lógica evita mistura de dados entre aplicações.

### 3.3 Papel do `debian-vps`

O container `debian-vps` criado anteriormente não será o host interno da 7Ps Marketing AI.

Seu papel passa a ser opcional, como ambiente para:

- testes Linux;
- scripts;
- experimentação;
- diagnóstico;
- aprendizado;
- execução manual de ferramentas.

A stack principal será executada diretamente pelo Docker Desktop.

---

## 4. Runtime principal

### Decisão

**Hermes Agent será o runtime principal da 7Ps Marketing AI.**

Configuração-base esperada:

```env
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
```

O OpenClaw permanece como referência do projeto original, mas não fará parte da execução da V1.

### Motivos

- melhor aderência ao modelo de profiles persistentes;
- suporte a skills;
- suporte a delegação por subagentes;
- suporte a Kanban multiagente;
- suporte a gateway de mensageria;
- boa base para uma organização de agentes permanentes.

---

## 5. Modelo de linguagem

### Decisão inicial

Usar modelo local via **Ollama**.

Configuração-base:

```env
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
```

### Estratégia

A V1 deve priorizar custo zero ou muito próximo de zero.

Modelos em nuvem continuam sendo uma possibilidade futura, mas não serão a dependência principal da primeira versão.

### Primeiro benchmark sugerido

Começar com modelos leves e medir:

- qualidade de raciocínio;
- qualidade de tool calling;
- uso de memória;
- latência;
- estabilidade;
- comportamento em delegações.

Candidatos iniciais:

- `qwen3:4b`;
- `llama3.2:3b`.

A escolha final deverá ser baseada em benchmark real no computador que hospedará a stack.

---

## 6. Canal de interação

### Decisão

**Telegram será o canal principal de interação com a agência.**

WhatsApp/Evolution Go não fará parte da V1.

Configuração-base:

```env
INSTALL_EVOLUTION=false
COMPOSE_PROFILES=
```

### Motivos

- menor complexidade operacional;
- menor risco de bloqueio;
- integração nativa com Hermes;
- adequado para uso interno;
- permite começar sem criar infraestrutura extra de WhatsApp.

### Modelo de uso

Na V1, somente o **Diretor** terá contato direto com o usuário via Telegram.

Os demais agentes trabalharão internamente.

```text
Usuário
  ↓
Telegram
  ↓
Diretor
  ↓
Agência interna
```

---

## 7. Arquitetura conceitual

A 7Ps Marketing AI será dividida em quatro camadas principais:

```text
PROFILE  = funcionário/departamento persistente
SKILL    = metodologia/procedimento/conhecimento
SUBAGENT = força de trabalho temporária
KANBAN   = sistema durável de coordenação do trabalho
```

Além dessas quatro camadas, teremos:

```text
TOOLS      = capacidade prática de execução
KNOWLEDGE  = fonte oficial de verdade da 7Ps
```

---

## 8. Profiles permanentes

A V1 terá inicialmente sete profiles persistentes:

1. `diretor`
2. `estrategista`
3. `analista`
4. `copywriter`
5. `criativo`
6. `conteudo`
7. `gestor-trafego`

Todos podem apontar inicialmente para o mesmo modelo local no Ollama.

Exemplo:

```text
diretor ─────────┐
estrategista ────┤
analista ────────┤
copywriter ──────┼──> Ollama -> modelo local
criativo ────────┤
conteudo ────────┤
gestor-trafego ──┘
```

O que muda entre eles é:

- identidade;
- SOUL;
- memória;
- responsabilidades;
- skills;
- ferramentas;
- autonomia;
- regras de handoff.

---

## 9. Skills — metodologia 7Ps

Skills representam **como a agência trabalha**.

Não devem ser tratadas apenas como nomes de cargos.

Estrutura inicial sugerida:

```text
skills/
├── diagnostico-marketing
├── pesquisa-concorrencia
├── definicao-icp
├── posicionamento
├── proposta-valor
├── planejamento-campanha
├── calendario-editorial
├── copy-anuncios
├── copy-landing-page
├── roteiro-reels
├── briefing-criativo
├── analise-campanha
├── meta-ads
└── google-ads
```

A intenção é transformar progressivamente a metodologia da 7Ps em um ativo versionado e reutilizável.

A biblioteca de skills deve conter, conforme necessário:

- procedimentos;
- checklists;
- regras;
- frameworks;
- templates;
- exemplos;
- critérios de aceite;
- referências;
- scripts auxiliares.

---

## 10. Subagentes

Subagentes serão usados como força de trabalho temporária.

São adequados para tarefas que:

- precisam de contexto isolado;
- podem ser executadas em paralelo;
- envolvem pesquisa;
- envolvem comparação;
- gerariam muito ruído no contexto do agente principal;
- não precisam manter identidade ou memória persistente.

Exemplo:

```text
Estrategista
├── subagent A -> concorrentes
├── subagent B -> dores do ICP
└── subagent C -> análise de ofertas

A + B + C
   ↓
Estrategista sintetiza
```

### Limite inicial

Por restrição de recursos locais, a concorrência inicial deverá ser conservadora:

```text
1 a 2 subagentes simultâneos
```

Esse limite será revisado após testes de CPU, memória e latência.

---

## 11. Kanban multiagente

O Kanban do Hermes será o mecanismo central para trabalhos que atravessam departamentos.

A diferença conceitual é:

```text
delegate_task = chamada temporária de trabalho
Kanban        = fila durável de trabalho entre agentes
```

O Kanban será usado quando a tarefa:

- precisa sobreviver a reinicializações;
- possui dependências;
- passa por vários agentes;
- precisa de revisão;
- pode ser bloqueada por decisão humana;
- precisa manter histórico e auditoria;
- precisa ser retomada posteriormente.

Estados típicos:

```text
triage
  ↓
todo
  ↓
ready
  ↓
running
  ↓
review
  ↓
done
```

Ou:

```text
running
  ↓
blocked
  ↓
aguarda decisão humana
  ↓
ready
```

---

## 12. Ferramentas especializadas

A arquitetura deve preservar a ideia central do projeto original: a LLM coordena e raciocina; ferramentas especializadas executam atividades em que um modelo genérico tende a performar pior.

Exemplos:

```text
Hermes
├── pesquisa -> ferramentas web
├── design/mídia -> Higgsfield / AtlasCloud / Media Editor
├── Meta Ads -> Meta Ads MCP/CLI
├── Google Ads -> SDK/MCP
├── arquivos -> filesystem
└── código -> ferramentas de desenvolvimento
```

### Regra arquitetural

Separar claramente:

```text
INSTALADO
!=
CONFIGURADO
!=
AUTORIZADO
```

Uma ferramenta pode existir na imagem sem estar pronta ou autorizada para executar ações reais.

Exemplo Meta Ads na primeira fase:

```text
INSTALADO     = SIM
CONFIGURADO   = NÃO
AUTORIZADO    = NÃO
```

---

## 13. Meta Ads

A integração Meta Ads será mantida no projeto.

Não será configurada imediatamente.

O agente `gestor-trafego` deve existir desde a V1 e estar preparado para:

- planejar campanhas;
- revisar estruturas;
- sugerir públicos;
- sugerir budget;
- preparar anúncios;
- ler e interpretar estrutura de campanhas;
- executar campanhas quando a integração for habilitada.

Antes da configuração Meta:

```text
PLANEJAR      = SIM
ANALISAR      = SIM
PREPARAR      = SIM
RECOMENDAR    = SIM
PUBLICAR      = NÃO
ALTERAR META  = NÃO
```

Depois da configuração:

```text
Gestor de Tráfego
  ↓
Meta Ads MCP
  ↓
execução real
```

---

## 14. Google Ads

A integração Google Ads também será preservada como capacidade futura.

Na V1 poderá permanecer instalada, porém não necessariamente configurada.

O objetivo é evitar retrabalho arquitetural quando a operação de mídia paga for expandida.

---

## 15. Ferramentas de criação e mídia

Higgsfield, AtlasCloud, Media Editor e integrações similares não devem ser removidos apenas porque o modelo local consegue gerar texto.

Essas ferramentas fazem parte da estratégia de especialização da stack.

Princípio:

```text
LLM = cérebro/orquestrador
Skill = forma de trabalhar
Tool = capacidade prática de executar
```

Exemplo do agente Criativo:

```text
PROFILE
Criativo
   ↓
SKILLS
├── briefing-criativo
├── identidade-visual
├── criativo-social
├── criativo-ads
└── roteiro-video
   ↓
TOOLS
├── Higgsfield
├── AtlasCloud
├── Media Editor
└── filesystem
```

---

## 16. Base de conhecimento

A verdade institucional da 7Ps não deverá ficar apenas na memória dos agentes.

Será criada uma camada compartilhada de conhecimento.

Estrutura conceitual:

```text
knowledge/
└── 7ps/
    ├── empresa
    ├── posicionamento
    ├── marca
    ├── produtos
    ├── servicos
    ├── publico
    ├── clinicas
    ├── ofertas
    ├── concorrentes
    ├── campanhas
    └── resultados
```

Princípio:

```text
Memória = experiência/contexto do agente
Knowledge = fonte oficial de verdade
```

Isso evita divergência entre agentes.

---

## 17. Visão da arquitetura operacional

```text
                    TELEGRAM
                        │
                        ▼
                ┌──────────────┐
                │   DIRETOR    │
                │   Profile    │
                └──────┬───────┘
                       │
                       ▼
                ┌──────────────┐
                │ KANBAN 7PS   │
                └──────┬───────┘
                       │
      ┌────────────────┼────────────────┐
      │                │                │
      ▼                ▼                ▼
 ESTRATEGISTA       ANALISTA         CONTEÚDO
   Profile           Profile          Profile
      │
 ┌────┴─────┐
 ▼          ▼
COPY      CRIATIVO
Profile   Profile
             │
             ▼
        GESTOR TRÁFEGO
           Profile

Todos usam conforme necessidade:
- Skills 7Ps
- Knowledge 7Ps
- Tools/MCP
- Subagentes temporários
```

---

## 18. V1 versus V2

### V1 — uso interno da 7Ps

Objetivos:

- validar organização dos agentes;
- validar metodologia;
- validar skills;
- validar uso de ferramentas;
- validar qualidade do modelo local;
- validar Telegram;
- validar Kanban;
- validar autonomia e aprovações;
- medir ganho operacional.

Não haverá multi-cliente ou multi-clínica.

### V2 — método para clínicas

Depois de validar a V1, a arquitetura poderá evoluir para:

- múltiplas clínicas;
- segregação de contexto;
- boards por cliente/projeto;
- tenants;
- workspaces separados;
- knowledge base por clínica;
- integrações com CliniSmart;
- credenciais por cliente;
- políticas de autonomia por cliente.

A V2 não deve ser antecipada dentro da V1 além do necessário para evitar decisões incompatíveis.

---

## 19. Decisões consolidadas

| Tema | Decisão V1 |
|---|---|
| Runtime | Hermes |
| OpenClaw | desabilitado |
| LLM | local via Ollama |
| LM Studio | desabilitado inicialmente |
| Canal | Telegram |
| WhatsApp/Evolution | fora da V1 |
| Meta Ads | ferramenta preservada; configuração posterior |
| Google Ads | ferramenta preservada; configuração posterior |
| Criativo | usar ferramentas especializadas quando disponível |
| Multi-clínica | adiada para V2 |
| Docker-in-Docker | não usar |
| Host | Docker Desktop |
| Profiles | 7 agentes persistentes |
| Skills | metodologia 7Ps |
| Subagents | tarefas temporárias/isoladas |
| Kanban | coordenação durável multiagente |
| Knowledge | fonte oficial da 7Ps |

---

## 20. Próxima etapa

Antes de iniciar implementação, definir detalhadamente o **modelo operacional dos sete agentes**:

- missão;
- responsabilidade;
- entradas;
- saídas;
- autonomia;
- ferramentas;
- skills;
- regras de handoff;
- uso de Kanban;
- quando delegar para subagentes;
- quando solicitar aprovação humana.

Esse desenho está registrado no documento complementar:

`docs/7ps-marketing-ai-v1-agents.md`
