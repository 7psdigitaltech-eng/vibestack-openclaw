# Cenários de Validação — 7Ps Marketing AI V1

Este documento define cenários práticos para validar se a arquitetura da agência funciona como sistema de trabalho, e não apenas como demonstração técnica.

## Cenário 1 — Planejamento de posicionamento

### Pedido

"Quero posicionar a 7Ps como parceira de crescimento para clínicas de estética."

### Fluxo esperado

1. Diretor recebe a demanda.
2. Diretor cria projeto/tarefas no Kanban.
3. Estrategista consulta knowledge base.
4. Estrategista pode delegar pesquisa de concorrência/mercado a subagents.
5. Estrategista entrega posicionamento, proposta de valor e ângulos.
6. Diretor consolida e apresenta ao usuário.

### Valida

- Telegram;
- Diretor;
- Kanban;
- Estrategista;
- knowledge base;
- subagents;
- handoff.

## Cenário 2 — Campanha completa sem publicação

### Pedido

"Crie uma campanha para divulgar o CliniSmart para clínicas de estética."

### Fluxo esperado

1. Estratégia da campanha.
2. Copywriter gera mensagens/ads.
3. Criativo produz direção visual e ativos.
4. Conteúdo cria plano orgânico complementar.
5. Gestor de Tráfego estrutura campanha/adset/ad e plano de budget.
6. Gestor identifica que Meta não está configurado e não tenta publicar.
7. Diretor entrega pacote consolidado.

### Valida

- dependências Kanban;
- papéis dos sete profiles;
- tools de mídia;
- guardrail do Meta;
- consolidação final.

## Cenário 3 — Pesquisa concorrencial paralela

### Pedido

"Compare os principais concorrentes da 7Ps no mercado de automação/IA para clínicas."

### Fluxo esperado

- Estrategista cria 1–2 subagents;
- cada subagent recebe contexto completo da tarefa;
- pesquisas são independentes;
- Estrategista sintetiza conclusões;
- Diretor apresenta resultado.

### Valida

- isolamento de subagent;
- limite de concorrência;
- estabilidade de CPU/RAM;
- qualidade de síntese.

## Cenário 4 — Análise de performance

### Pedido

Usuário fornece export/planilha com resultados de campanha.

### Fluxo esperado

1. Diretor encaminha ao Analista.
2. Analista calcula métricas e identifica variações.
3. Analista separa fato de hipótese.
4. Estrategista recebe os achados e propõe ações.
5. Gestor traduz ações em plano operacional.
6. Diretor apresenta recomendação com evidências.

### Valida

- separação Analista/Estrategista/Gestor;
- leitura de arquivos;
- raciocínio quantitativo;
- handoff orientado por evidência.

## Cenário 5 — Produção de conteúdo

### Pedido

"Monte uma semana de conteúdo da 7Ps para Instagram com foco em clínicas."

### Fluxo esperado

1. Estrategista define objetivo e pilares.
2. Conteúdo cria calendário.
3. Copywriter produz textos.
4. Criativo produz conceito/mídia.
5. Diretor revisa consistência.

### Valida

- calendário editorial;
- skills de conteúdo;
- ferramentas visuais;
- consistência da marca.

## Cenário 6 — Bloqueio para decisão humana

### Situação

Durante uma tarefa, falta uma definição oficial de preço/oferta ou existe ação acima da alçada.

### Fluxo esperado

1. profile responsável marca tarefa como `blocked`;
2. informa claramente qual decisão é necessária;
3. Diretor solicita decisão ao usuário pelo Telegram;
4. após resposta, tarefa retorna a `ready`;
5. trabalho continua sem perder histórico.

### Valida

- human-in-the-loop;
- durabilidade do Kanban;
- retomada de contexto;
- ausência de invenção de fatos.

## Cenário 7 — Restart da stack

### Situação

Há tarefas em `todo`, `running` ou `blocked`, e o Docker/Hermes é reiniciado.

### Resultado esperado

- profiles e configuração permanecem;
- knowledge/skills persistem;
- Kanban não perde tarefas;
- Telegram volta a responder;
- modelo local pode ser reinicializado;
- nenhuma ação externa é duplicada silenciosamente.

### Valida

- persistência;
- recuperação;
- segurança operacional.

## Cenário 8 — Limite do modelo local

### Situação

O modelo não consegue executar uma tarefa com qualidade suficiente.

### Comportamento esperado

- sistema não inventa que completou;
- registra limitação;
- tenta ferramenta/skill mais adequada quando existir;
- solicita revisão humana quando necessário;
- dado de benchmark é registrado para futura decisão de modelo/provider.

## Critério global de aprovação da V1

A V1 não será considerada validada apenas porque containers estão `Running`.

Será considerada validada quando conseguir executar ciclos de marketing reais da 7Ps com:

- coordenação automática útil;
- especialização clara;
- consistência factual;
- entregas utilizáveis;
- estabilidade local;
- baixo custo;
- auditoria;
- controle humano em ações de risco.
