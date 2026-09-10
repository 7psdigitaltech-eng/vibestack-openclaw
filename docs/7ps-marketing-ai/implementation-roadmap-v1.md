# Roadmap de Implementação — 7Ps Marketing AI V1

## Objetivo

Implementar a agência de marketing com IA da 7Ps de forma incremental, mantendo a stack funcional a cada fase e evitando adicionar integrações de alto risco antes de validar a base.

A ordem abaixo prioriza: estabilidade > observabilidade > comunicação > organização multiagente > qualidade de marketing > automação externa.

---

## Fase 0 — Congelar arquitetura e preparar o fork

### Entregas

- documentação V1 concluída;
- registrar decisões arquiteturais;
- preservar referência ao upstream original;
- evitar alterações funcionais antes da documentação.

### Critério de aceite

- visão, arquitetura, agentes e roadmap versionados no GitHub;
- nenhuma implementação crítica iniciada sem decisão registrada.

---

## Fase 1 — Estrutura local do projeto

### Objetivo

Preparar o projeto como stack independente no Docker Desktop.

### Entregas

- diretório físico em `D:\Docker\7ps-marketing-ai\`;
- clone do fork em pasta própria;
- persistência planejada para Hermes, Ollama, knowledge e outputs;
- projeto Docker com nome próprio;
- documentação dos comandos de operação local.

### Critérios de aceite

- projeto sobe sem depender do container `debian-vps`;
- não usa Docker-in-Docker;
- dados persistentes ficam fora do filesystem efêmero;
- restart do container não perde configuração.

---

## Fase 2 — Hermes-only

### Objetivo

Remover o OpenClaw do caminho operacional da V1.

### Entregas

- `INSTALL_OPENCLAW=false`;
- `INSTALL_HERMES=true`;
- Hermes como processo principal;
- dashboard funcional;
- health check/documentação de diagnóstico;
- logs persistentes ou facilmente acessíveis.

### Critérios de aceite

- stack inicia com Hermes sem depender do OpenClaw;
- Hermes responde em sessão local/dashboard;
- restart automático funciona;
- falha do Hermes é diagnosticável pelos logs.

---

## Fase 3 — Ollama e benchmark de modelo local

### Objetivo

Validar operação com custo zero/mínimo.

### Entregas

- Ollama operacional;
- modelos candidatos baixados;
- contexto configurado adequadamente;
- benchmark prático de marketing;
- registro de RAM, CPU, tempo de resposta e qualidade.

### Testes sugeridos

1. planejamento de campanha;
2. definição de ICP;
3. copy de anúncio;
4. análise de uma tabela simples;
5. uso de tool calling;
6. resposta longa com contexto;
7. delegação para subagent.

### Critérios de aceite

- pelo menos um modelo local consegue executar as tarefas básicas da V1;
- uso de memória é sustentável no computador atual;
- tool calling não falha sistematicamente;
- modelo escolhido é registrado na documentação.

### Observação

Caso nenhum modelo local ofereça qualidade mínima, será aberta decisão específica para provider em nuvem de baixo custo, sem abandonar a arquitetura local-first.

---

## Fase 4 — Telegram + Diretor

### Objetivo

Criar a porta de entrada operacional da agência.

### Entregas

- bot Telegram;
- token armazenado fora do Git;
- allowlist do usuário;
- gateway Hermes operacional;
- profile `diretor`;
- SOUL/instruções do Diretor;
- comunicação texto ida/volta;
- teste com arquivos/imagens quando suportado.

### Critérios de aceite

- mensagem enviada ao bot chega ao Diretor;
- Diretor responde corretamente;
- usuário não autorizado é bloqueado;
- segredos não aparecem em logs/documentação;
- reinício da stack restaura o canal.

---

## Fase 5 — Knowledge Base 7Ps

### Objetivo

Separar fatos oficiais da empresa da memória dos agents.

### Entregas

- estrutura `knowledge/7ps/`;
- documentos iniciais de empresa, posicionamento, serviços, ICP e marca;
- regra de precedência da knowledge base;
- mecanismo de leitura pelos profiles;
- processo para atualizar conhecimento oficial.

### Critérios de aceite

- agents consultam informação oficial antes de afirmar dados importantes;
- mudança em documento oficial não exige editar todos os prompts;
- nenhum segredo é salvo na knowledge base versionada.

---

## Fase 6 — Skills 7Ps V1

### Objetivo

Transformar a metodologia da 7Ps em procedimentos reutilizáveis.

### Skills iniciais prioritárias

1. diagnóstico de marketing;
2. definição de ICP;
3. posicionamento;
4. proposta de valor;
5. pesquisa de concorrência;
6. planejamento de campanha;
7. copy de anúncios;
8. calendário editorial;
9. briefing criativo;
10. análise de performance;
11. planejamento Meta Ads.

### Critérios de aceite

- skills são carregadas sob demanda;
- cada skill possui quando usar, procedimento, pitfalls e verificação;
- skills não duplicam fatos da knowledge base;
- outputs são consistentes entre execuções.

---

## Fase 7 — Profiles especialistas

### Objetivo

Criar os sete especialistas persistentes.

### Profiles

- `diretor`;
- `estrategista`;
- `analista`;
- `copywriter`;
- `criativo`;
- `conteudo`;
- `gestor-trafego`.

### Entregas

Para cada profile:

- descrição do papel;
- SOUL;
- skills relevantes;
- toolsets permitidos;
- limites de autonomia;
- contrato de entrada/saída;
- workspace/cwd quando necessário.

### Critérios de aceite

- cada profile responde de acordo com sua função;
- não há sobreposição crítica de responsabilidade;
- Analista não executa Ads;
- Criativo não decide estratégia sozinho;
- Gestor não publica enquanto Meta estiver desabilitado.

---

## Fase 8 — Kanban multiagente

### Objetivo

Transformar a agência em fluxo de trabalho durável.

### Entregas

- board principal da 7Ps;
- regras de estados;
- dependências;
- comentários/handoffs;
- bloqueio aguardando usuário;
- Diretor capaz de criar e acompanhar tarefas;
- profiles capazes de consumir tarefas atribuídas.

### Cenário de teste

Campanha completa com tarefas:

1. Estratégia;
2. Copy;
3. Conceito visual;
4. Plano de conteúdo;
5. Plano de mídia.

### Critérios de aceite

- dependências respeitadas;
- tarefa bloqueada sobrevive a restart;
- histórico pode ser revisado;
- Diretor consegue consolidar status pelo Telegram.

---

## Fase 9 — Subagents controlados

### Objetivo

Adicionar paralelismo temporário sem saturar a máquina.

### Configuração inicial

- concorrência: 1–2;
- mesmo modelo local do parent inicialmente;
- uso restrito a pesquisa/análise bem delimitada.

### Critérios de aceite

- parent recebe resultado sem mistura de contexto;
- máquina permanece estável;
- tarefas de pesquisa melhoram em qualidade/tempo;
- falha de subagent não derruba o workflow principal.

---

## Fase 10 — Ferramentas de criação

### Objetivo

Dar ao Criativo capacidade prática além do LLM textual.

### Integrações previstas

- Media Editor;
- Higgsfield;
- AtlasCloud;
- outras ferramentas visuais aprovadas.

### Estratégia

Cada integração pode existir em um dos estados:

- instalada;
- configurada;
- autorizada.

### Critérios de aceite

- Criativo consegue identificar ferramenta correta;
- outputs são salvos em caminho persistente;
- arquivos podem ser enviados ao Diretor/Telegram;
- custo de cada ferramenta é conhecido antes de uso recorrente;
- fallback existe quando serviço externo não estiver disponível.

---

## Fase 11 — Gestor de Tráfego preparado

### Objetivo

Validar o Gestor sem publicar campanhas.

### Entregas

- skill de planejamento Meta;
- estrutura campanha/adset/ad;
- orçamento e hipóteses;
- integração lógica com Copy/Criativo;
- política de autorização documentada;
- tools Meta presentes, mas sem credenciais.

### Critérios de aceite

- Gestor produz plano executável;
- tentativa de write sem configuração falha de forma segura;
- nenhum gasto real pode ocorrer.

---

## Fase 12 — Meta Ads read-only

### Objetivo

Conectar Meta após validação da agência, começando com menor privilégio.

### Entregas

- credenciais Meta;
- leitura de contas/campanhas;
- acesso do Analista;
- acesso de leitura do Gestor;
- auditoria de chamadas.

### Critérios de aceite

- leitura real funciona;
- escrita continua bloqueada;
- dados retornam ao Analista e alimentam estratégia.

---

## Fase 13 — Meta Ads write com aprovação

### Objetivo

Liberar execução real somente após política de risco.

### Antes de habilitar

Definir:

- alçada de budget;
- ações que exigem aprovação;
- limites diários;
- rollback;
- logs;
- idempotência;
- confirmação antes de exclusões;
- ambiente/teste inicial.

### Critérios de aceite

- somente Gestor possui write;
- ações acima da alçada bloqueiam;
- usuário consegue aprovar/rejeitar;
- toda mutação é registrada;
- existe confirmação pós-ação.

---

## Fase 14 — Validação operacional da V1

### Objetivo

Usar a agência para marketing real da 7Ps.

### Período sugerido

Executar ciclos reais por semanas, não apenas demos isoladas.

### Métricas

- tempo poupado;
- quantidade de retrabalho;
- qualidade percebida;
- taxa de entregas aceitas sem refação;
- latência;
- estabilidade;
- consumo de RAM/CPU;
- custo externo;
- quantidade de intervenções humanas;
- qualidade de handoff;
- erros de contexto.

### Saída

Relatório de validação que decide:

- manter/ajustar profiles;
- consolidar/remover skills;
- trocar modelo;
- aumentar autonomia;
- iniciar V2 multi-clínica.

---

# Fora do escopo inicial

Não implementar antes de necessidade comprovada:

- multi-clínica;
- multi-tenant SaaS;
- múltiplos bots Telegram;
- WhatsApp/Evolution;
- alta disponibilidade;
- cluster Docker/Kubernetes;
- deploy em VPS pública permanente;
- cobrança/assinaturas;
- painel específico para clientes;
- integração profunda com CliniSmart.

---

# Política de mudanças

Toda mudança relevante em:

- runtime principal;
- modelo;
- isolamento;
- segurança;
- autonomia;
- tools externas;
- estrutura de agents;
- multi-cliente;

...deve ser registrada em `decisions.md` antes ou junto da implementação.
