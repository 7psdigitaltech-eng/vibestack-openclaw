# 7Ps Marketing AI — Planejamento V1

**Status:** arquitetura conceitual aprovada para planejamento e implementação inicial.

## 1. Objetivo

Transformar o fork `7psdigitaltech-eng/vibestack-openclaw` em uma **agência de marketing com IA para uso interno da 7Ps**, aproveitando a base técnica existente do Vibestack, mas adaptando papéis, ferramentas, canal de comunicação e fluxo operacional para a realidade da 7Ps.

A V1 será um laboratório operacional real: a própria 7Ps será o primeiro cliente da agência de IA. O sistema deverá ajudar a planejar, produzir, organizar, analisar e futuramente executar marketing com uma estrutura de especialistas coordenados.

A prioridade inicial não é criar um SaaS multiempresa. A prioridade é validar:

1. metodologia de marketing;
2. qualidade de decisão dos agentes;
3. divisão correta de responsabilidades;
4. eficiência dos handoffs;
5. uso de ferramentas especializadas;
6. uso de modelo local com custo mínimo;
7. interação prática via Telegram;
8. governança e segurança antes de ações irreversíveis;
9. capacidade de registrar e aprender com o trabalho realizado.

## 2. Visão de evolução

### V1 — Agência interna 7Ps

Escopo único: **7Ps**.

A V1 deverá operar uma única marca/contexto principal. Não serão implementados agora isolamento por clínica, multi-tenant, múltiplas credenciais por cliente ou roteamento entre empresas.

### V2 — Método de Marketing para Clínicas

Depois da validação interna, a mesma arquitetura poderá ser adaptada para atender clínicas através da metodologia da 7Ps.

A V2 deverá avaliar e implementar:

- isolamento de contexto por clínica;
- knowledge base por cliente;
- boards/workspaces/tenants separados;
- credenciais por cliente;
- regras de acesso por clínica;
- integrações com CliniSmart;
- relatórios por clínica;
- trilha de auditoria por cliente;
- eventual produto/serviço gerenciado pela 7Ps.

A separação multi-clínica é uma decisão deliberadamente adiada. A V1 deve ser simples o suficiente para ser validada rapidamente, mas estruturada para não bloquear a V2.

## 3. Decisões centrais da V1

### 3.1 Hermes Agent será o runtime principal

A V1 será construída sobre **Hermes Agent**.

O OpenClaw permanecerá como parte da origem do projeto e poderá ser mantido no histórico/upstream, mas não será o runtime operacional escolhido para a agência 7Ps.

Motivos:

- suporte a profiles persistentes;
- skills carregadas sob demanda;
- subagent delegation;
- Kanban multiagente durável;
- gateway para Telegram;
- memória e sessões por profile;
- dashboard e capacidade de operação headless;
- boa base para futura expansão multiagente/multicliente.

### 3.2 Modelo local primeiro

A prioridade é **custo zero ou mínimo**.

A primeira opção será Ollama com modelo local. Providers em nuvem poderão ser usados mais tarde quando houver justificativa de qualidade, desempenho ou necessidade específica.

O modelo local será tratado como o cérebro de raciocínio, mas não como responsável por executar sozinho todas as tarefas. A qualidade final será construída pela combinação:

```text
modelo
+ contexto
+ skills
+ knowledge base
+ ferramentas especializadas
+ workflow
+ validação
```

A escolha final do modelo será feita por benchmark prático. Modelos pequenos serão priorizados inicialmente por causa dos recursos disponíveis no ambiente local.

### 3.3 Telegram será o canal humano principal

A V1 usará **Telegram**, aproveitando o gateway nativo do Hermes.

O WhatsApp/Evolution Go não será usado inicialmente.

Benefícios esperados:

- configuração mais simples;
- menor risco operacional de bloqueio de conta;
- menos infraestrutura auxiliar;
- comunicação bidirecional rápida;
- suporte a texto, arquivos, imagens e voz;
- possibilidade de aprovações e comandos via bot;
- uso local sem necessidade inicial de webhook público, usando long polling quando adequado.

Inicialmente apenas o **Diretor** será exposto ao Telegram. Os demais profiles funcionarão internamente.

### 3.4 Meta Ads será preparado, mas não ativado operacionalmente

A integração Meta Ads será preservada no projeto e o **Gestor de Tráfego** será criado desde a V1.

Entretanto, as credenciais Meta e a capacidade real de criar/alterar campanhas serão configuradas em uma etapa posterior.

Antes disso, o Gestor poderá:

- estruturar campanhas;
- preparar plano de mídia;
- sugerir orçamento;
- definir arquitetura de campanha/adset/ad;
- revisar segmentação;
- analisar dados fornecidos manualmente;
- preparar comandos/ações futuras.

Não poderá:

- publicar campanhas;
- alterar budget real;
- pausar ou ativar anúncios;
- modificar ativos em conta Meta.

### 3.5 Ferramentas especializadas serão preservadas

A V1 não deve confundir "não configurado" com "desnecessário".

Ferramentas como Meta Ads CLI/MCP, Google Ads SDK/MCP, Higgsfield, AtlasCloud e Media Editor são parte importante da visão da agência porque permitem que um modelo local utilize serviços especializados para executar trabalhos que não são sua principal competência.

Princípio:

```text
INSTALADO != CONFIGURADO != AUTORIZADO
```

Uma integração pode estar presente no runtime, mas indisponível até que credenciais sejam fornecidas. E mesmo configurada, pode ter políticas de autorização por agente.

## 4. Organograma da V1

Profiles persistentes planejados:

1. **Diretor** — porta de entrada humana, orquestração e consolidação.
2. **Estrategista** — estratégia, posicionamento, campanha, funil, oferta e priorização.
3. **Analista** — métricas, diagnóstico quantitativo e leitura objetiva de performance.
4. **Copywriter** — textos, anúncios, landing pages, roteiros e mensagens.
5. **Criativo** — conceito visual, briefing e operação de ferramentas de imagem/vídeo.
6. **Conteúdo** — calendário editorial, social media, distribuição e reaproveitamento.
7. **Gestor de Tráfego** — mídia paga, arquitetura de campanhas e futura execução via Ads.

Pesquisa e inteligência de mercado serão inicialmente tratadas como **skills e subagentes**, evitando criar um profile persistente adicional sem necessidade comprovada.

## 5. Quatro primitivas de trabalho do Hermes

A arquitetura utiliza cada recurso para uma finalidade distinta.

### Profiles

Representam especialistas persistentes. Possuem identidade, memória, configuração, sessões e estado próprios.

### Skills

Representam metodologia, conhecimento operacional, procedimento, checklist, templates e boas práticas reutilizáveis.

Exemplos:

- diagnóstico de marketing;
- definição de ICP;
- pesquisa de concorrência;
- posicionamento;
- proposta de valor;
- planejamento de campanha;
- calendário editorial;
- copy de anúncio;
- briefing criativo;
- análise de performance;
- planejamento Meta Ads.

### Subagents

São trabalhadores temporários e isolados criados para uma tarefa específica.

Usos iniciais:

- pesquisar múltiplos concorrentes;
- comparar alternativas;
- analisar documentos em paralelo;
- levantar hipóteses;
- executar pesquisas independentes;
- reduzir poluição do contexto principal.

Na V1 a concorrência deverá começar limitada a **1–2 subagentes simultâneos**, até medirmos CPU, RAM, latência e qualidade do modelo local.

### Kanban

Será a camada durável de coordenação entre profiles.

Uso quando:

- uma tarefa passa de um especialista para outro;
- existe dependência entre etapas;
- é necessário histórico;
- há revisão/aprovação humana;
- a tarefa deve sobreviver a restart;
- existe bloqueio aguardando ação humana;
- o trabalho precisa ser auditável.

## 6. Knowledge base compartilhada

A memória individual dos agents não será considerada fonte oficial de verdade da empresa.

A V1 deverá manter uma base de conhecimento compartilhada da 7Ps, organizada aproximadamente como:

```text
knowledge/7ps/
├── empresa/
├── marca/
├── posicionamento/
├── publico/
├── produtos-servicos/
├── ofertas/
├── concorrentes/
├── campanhas/
├── conteudo/
├── processos/
└── resultados/
```

Princípio:

- **memória** = histórico, preferências, aprendizados e contexto conversacional;
- **knowledge base** = fatos oficiais e documentos de referência;
- **skills** = como executar um trabalho;
- **tools** = como agir em sistemas externos.

## 7. Princípios de governança

1. O Diretor é a porta única com o usuário na V1.
2. Especialistas devem trabalhar dentro de suas competências.
3. Ações externas de risco devem ter alçada explícita.
4. Nenhum agente deve assumir credenciais inexistentes.
5. O Gestor de Tráfego só escreve em Ads depois de integração e política de autorização aprovadas.
6. O Analista deve separar fato/métrica de recomendação estratégica.
7. O Criativo utiliza ferramentas especializadas, não apenas geração textual da LLM.
8. Handoffs entre departamentos importantes devem passar pelo Kanban.
9. Subagents são temporários; não substituem identidade/memória de profiles persistentes.
10. A knowledge base é a fonte oficial para fatos da 7Ps.
11. Toda automação irreversível deverá possuir mecanismo de auditoria e, quando necessário, aprovação humana.

## 8. Resultado esperado da V1

Ao final da V1, deve ser possível conversar com o Diretor pelo Telegram e solicitar uma atividade real de marketing, por exemplo:

> "Quero criar uma campanha para posicionar a 7Ps como parceira de crescimento para clínicas de estética."

O sistema deverá ser capaz de:

1. interpretar o objetivo;
2. decompor o projeto;
3. consultar conhecimento 7Ps;
4. criar tarefas no Kanban;
5. encaminhar estratégia ao Estrategista;
6. gerar copy e conteúdo;
7. produzir briefing e/ou mídia pelo Criativo;
8. preparar plano de mídia com o Gestor;
9. consolidar as entregas;
10. solicitar aprovação quando necessário;
11. registrar o histórico do projeto.

A publicação automática no Meta não é requisito para o primeiro marco funcional.
