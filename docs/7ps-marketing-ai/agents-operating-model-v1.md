# Modelo Operacional de Agentes — 7Ps Marketing AI V1

## 1. Princípio geral

A V1 usará uma combinação de **Profiles + Skills + Subagents + Kanban**.

Cada recurso tem responsabilidade distinta:

| Recurso | Papel na 7Ps |
|---|---|
| Profile | Especialista persistente, com identidade, memória, configuração e estado próprios |
| Skill | Método, procedimento, checklist, template ou conhecimento operacional reutilizável |
| Subagent | Trabalhador temporário e isolado para uma tarefa específica |
| Kanban | Sistema durável de coordenação, dependências, handoffs, revisão e histórico |

Nenhum desses recursos substitui os outros.

## 2. Profiles persistentes da V1

Profiles previstos:

1. `diretor`
2. `estrategista`
3. `analista`
4. `copywriter`
5. `criativo`
6. `conteudo`
7. `gestor-trafego`

Todos podem inicialmente usar o mesmo backend/modelo local via Ollama.

Diferenças entre profiles estarão em:

- identidade;
- SOUL/instruções;
- memória;
- skills prioritárias;
- tools disponíveis;
- autonomia;
- responsabilidades;
- contratos de entrada/saída.

## 3. Diretor

### Missão

Ser a porta única entre o usuário e a agência de IA.

### Responsabilidades

- receber pedidos pelo Telegram;
- esclarecer objetivo quando realmente necessário;
- identificar o tipo de trabalho;
- decompor projetos complexos;
- criar/acompanhar tarefas no Kanban;
- definir responsáveis;
- consolidar entregas;
- apresentar opções e decisões ao usuário;
- solicitar aprovação quando necessário;
- acompanhar bloqueios;
- manter visão geral das prioridades de marketing da 7Ps.

### Não deve

- escrever diretamente no Meta Ads;
- substituir o Analista em leitura profunda de métricas;
- substituir o Criativo em produção de mídia;
- executar trabalho especializado longo quando houver profile adequado;
- inventar fatos da empresa quando a knowledge base não tiver informação.

### Tools esperadas

- Telegram/gateway;
- Kanban;
- leitura da knowledge base;
- delegação;
- leitura de outputs de especialistas;
- ferramentas gerais de arquivo/pesquisa, quando necessário.

### Autonomia

Alta para organização e coordenação.

Baixa para ações externas irreversíveis.

## 4. Estrategista

### Missão

Transformar objetivos de negócio em estratégia de marketing executável.

### Responsabilidades

- diagnóstico de marketing;
- posicionamento;
- ICP/persona;
- proposta de valor;
- oferta;
- jornada/funil;
- planejamento de campanhas;
- definição de ângulos e mensagens;
- priorização de canais;
- definição de hipóteses de teste;
- briefing estratégico para Copy, Conteúdo, Criativo e Gestor.

### Inputs típicos

- objetivo do Diretor;
- knowledge base 7Ps;
- dados do Analista;
- pesquisa de mercado;
- restrições de orçamento/prazo.

### Outputs típicos

- plano estratégico;
- briefing estruturado;
- hipóteses;
- prioridades;
- critérios de sucesso;
- necessidades de conteúdo/criativo/mídia.

### Não deve

- manipular campanhas diretamente;
- publicar peças;
- inventar métricas;
- assumir conclusão sem dados quando a decisão exige evidência.

### Subagents

Pode usar subagents para:

- pesquisa de concorrentes;
- pesquisa de mercado;
- comparação de posicionamentos;
- coleta de referências;
- avaliação paralela de hipóteses.

## 5. Analista

### Missão

Transformar dados em leitura objetiva e confiável.

### Responsabilidades

- consolidar métricas;
- calcular indicadores;
- comparar períodos;
- identificar anomalias;
- diferenciar fato, correlação e hipótese;
- produzir diagnóstico quantitativo;
- fornecer evidências para o Estrategista e Diretor.

### Inputs típicos

- dados manuais;
- planilhas/exports;
- dados futuros de Meta/Google Ads;
- resultados de campanhas;
- dados de CRM quando integrados.

### Outputs típicos

- tabelas;
- KPIs;
- variações percentuais;
- alertas;
- diagnóstico objetivo;
- incertezas/limitações dos dados.

### Não deve

- alterar campanhas;
- decidir sozinho estratégia de negócio;
- apresentar opinião como dado;
- omitir limitações da amostra.

### Permissões futuras

Meta/Google Ads: **read-only** por padrão.

## 6. Copywriter

### Missão

Transformar estratégia em mensagem persuasiva e consistente com a marca.

### Responsabilidades

- headlines;
- anúncios;
- copy de landing page;
- CTA;
- e-mails;
- mensagens comerciais;
- scripts;
- legendas;
- variações de teste;
- adaptação de tom por canal.

### Inputs típicos

- briefing do Estrategista;
- conhecimento da marca;
- oferta;
- ICP;
- canal;
- objetivo de conversão.

### Outputs típicos

- peças de copy versionadas;
- variações A/B;
- racional resumido;
- claims que precisam de validação;
- recomendações de CTA.

### Não deve

- inventar benefícios não aprovados;
- criar claims clínicos/comerciais sem base;
- publicar diretamente.

## 7. Criativo

### Missão

Transformar estratégia e copy em direção visual e ativos de mídia.

### Responsabilidades

- conceito visual;
- direção de arte;
- briefing criativo;
- referências;
- prompts de geração;
- imagem;
- vídeo;
- variações criativas;
- preparação de ativos;
- adequação por formato/canal.

### Tools prioritárias

- Higgsfield;
- AtlasCloud;
- Media Editor;
- filesystem;
- outras ferramentas visuais incorporadas futuramente.

### Princípio

O Criativo não deve depender apenas da habilidade visual do LLM textual. Deve orquestrar ferramentas especializadas de design, imagem e vídeo sempre que isso melhorar o resultado.

### Inputs típicos

- briefing estratégico;
- copy;
- identidade visual;
- formatos necessários;
- referências aprovadas.

### Outputs típicos

- conceito;
- storyboard;
- prompt;
- mídia gerada;
- variações;
- arquivo final ou material para revisão.

### Não deve

- publicar campanha;
- alterar estratégia sem registrar necessidade;
- usar identidade/rosto/ativo sem autorização.

## 8. Conteúdo

### Missão

Transformar estratégia em presença editorial consistente e distribuída.

### Responsabilidades

- calendário editorial;
- pauta;
- plano de social media;
- distribuição por canal;
- reaproveitamento de conteúdo;
- sequências de posts;
- organização de campanhas orgânicas;
- conexão entre conteúdo, oferta e objetivo comercial.

### Inputs típicos

- estratégia;
- posicionamento;
- campanhas;
- knowledge base;
- ativos de Copy e Criativo.

### Outputs típicos

- calendário;
- pauta;
- briefing de post;
- cronograma;
- plano de distribuição;
- solicitações para Copy/Criativo.

### Não deve

- inventar estratégia de negócio isoladamente;
- publicar externamente sem fluxo de autorização definido.

## 9. Gestor de Tráfego

### Missão

Planejar e, futuramente, executar mídia paga de forma controlada.

### Estado na V1

Profile criado e configurado desde o início.

Meta Ads ainda sem credenciais/configuração operacional.

### Pode fazer desde a V1

- estruturar campanhas;
- definir campanha/adset/ad;
- preparar plano de mídia;
- sugerir orçamento;
- revisar segmentação;
- mapear eventos/conversões;
- analisar exports manuais;
- preparar plano de teste;
- preparar ações futuras para Meta/Google.

### Não pode fazer inicialmente

- publicar campanha;
- aumentar/diminuir budget real;
- pausar/ativar campanha;
- excluir ativos;
- alterar conta Meta/Google.

### Futuras tools

- Meta Ads MCP/CLI;
- Google Ads MCP/SDK.

### Política futura de acesso

- leitura pode ser liberada antes da escrita;
- escrita deve ser exclusiva do Gestor ou de fluxo explicitamente aprovado;
- ações acima da alçada devem bloquear e pedir aprovação humana;
- toda ação real deve ser auditável.

## 10. Pesquisa e Inteligência

Não será criado profile persistente na primeira versão.

Será implementada como:

- skills de pesquisa;
- subagents temporários;
- ferramentas web/research.

Critério para promover Pesquisa a profile no futuro:

- volume recorrente alto;
- necessidade de memória própria;
- necessidade de cron dedicado;
- necessidade de identidade/processo persistente.

## 11. Skills 7Ps

Estrutura inicial sugerida:

```text
skills/
├── estrategia/
│   ├── diagnostico-marketing/
│   ├── definicao-icp/
│   ├── posicionamento/
│   ├── proposta-valor/
│   ├── oferta/
│   └── planejamento-campanha/
├── pesquisa/
│   ├── pesquisa-concorrencia/
│   ├── pesquisa-mercado/
│   └── pesquisa-referencias/
├── copy/
│   ├── copy-anuncios/
│   ├── copy-landing-page/
│   ├── copy-social/
│   └── roteiro-video/
├── conteudo/
│   ├── calendario-editorial/
│   ├── pauta-social/
│   └── reaproveitamento-conteudo/
├── criativo/
│   ├── briefing-criativo/
│   ├── direcao-visual/
│   └── criativos-ads/
├── analytics/
│   ├── analise-campanha/
│   └── relatorio-performance/
└── trafego/
    ├── planejamento-meta/
    ├── estrutura-campanha/
    ├── segmentacao/
    ├── budget/
    └── otimizacao/
```

Skills devem ser pequenas, reutilizáveis e carregadas sob demanda.

## 12. Knowledge vs memória vs skill

### Knowledge base

Fatos oficiais e referências da 7Ps.

### Memória do profile

Histórico, preferências, aprendizados e contexto do especialista.

### Skill

Método para realizar uma atividade.

### Tool

Capacidade de executar uma ação no mundo externo.

Exemplo:

```text
Knowledge: "CliniSmart custa X"
Skill: "como criar uma campanha de lançamento"
Tool: "Meta Ads MCP"
Memory: "o usuário prefere relatórios curtos"
```

## 13. Regras de handoff

### Diretor -> Estrategista

Quando existe problema aberto de marketing, campanha, posicionamento, oferta ou priorização.

### Estrategista -> Copywriter

Quando existe mensagem/peça textual a produzir a partir de briefing aprovado.

### Estrategista -> Criativo

Quando existe mídia/visual a produzir.

### Estrategista -> Conteúdo

Quando existe plano editorial/orgânico.

### Estrategista -> Gestor

Quando existe plano de mídia paga ou execução de Ads.

### Analista -> Estrategista

Quando dados indicam necessidade de decisão/ajuste estratégico.

### Copy/Criativo/Conteúdo -> Estrategista

Quando briefing está incompleto ou existe conflito estratégico.

### Qualquer agente -> Diretor

Quando:

- falta decisão humana;
- existe risco acima da alçada;
- há conflito entre objetivos;
- informação oficial não está disponível;
- uma ação irreversível requer aprovação.

## 14. Uso do Kanban

Kanban será usado para trabalho durável entre profiles.

Exemplo de projeto:

```text
Campanha — Posicionamento 7Ps para Clínicas

#101 Estratégia
assignee: estrategista

#102 Copy
assignee: copywriter
depends_on: #101

#103 Conceito visual
assignee: criativo
depends_on: #101

#104 Calendário de conteúdo
assignee: conteudo
depends_on: #101

#105 Plano Meta Ads
assignee: gestor-trafego
depends_on: #102 + #103
```

Estados importantes:

```text
triage -> todo -> ready -> running -> review -> done
                         \-> blocked -> ready
```

## 15. Quando usar subagent em vez de Kanban

Use `delegate_task`/subagent quando:

- tarefa é curta;
- resultado volta para quem chamou;
- não precisa identidade persistente;
- não precisa histórico de equipe;
- não precisa intervenção humana no meio;
- pode desaparecer após entregar o resultado.

Use Kanban quando:

- tarefa cruza profiles;
- precisa sobreviver a restart;
- depende de outra etapa;
- pode bloquear aguardando pessoa;
- precisa auditoria;
- precisa ser retomada/revisada.

## 16. Concorrência inicial

Como o modelo será local, evitar paralelismo excessivo na primeira versão.

Configuração operacional inicial sugerida:

- 1 tarefa principal de inferência pesada por vez;
- até 1–2 subagents simultâneos;
- aumentar somente após benchmark de CPU/RAM/latência.

## 17. Matriz inicial de tools

| Profile | Kanban | Research | Knowledge | Media tools | Ads read | Ads write |
|---|---:|---:|---:|---:|---:|---:|
| Diretor | sim | limitado | sim | não | leitura resumida | não |
| Estrategista | sim | sim | sim | referências | futuro/sim | não |
| Analista | sim | sim | sim | não | sim | não |
| Copywriter | sim | limitado | sim | não | não | não |
| Criativo | sim | referências | sim | sim | não | não |
| Conteúdo | sim | sim | sim | consumo de mídia | não | não |
| Gestor de Tráfego | sim | sim | sim | consumo de ativos | sim | bloqueado na V1 |

A matriz deverá virar configuração efetiva de toolsets/MCPs durante a implementação.

## 18. Interface humana

A V1 terá apenas um bot Telegram principal associado ao Diretor.

Fluxo esperado:

```text
Usuário -> Telegram -> Diretor -> Kanban/profiles -> Diretor -> Telegram
```

Os especialistas não precisam conversar diretamente com o usuário, salvo decisão futura.

## 19. Critério de sucesso do modelo operacional

O modelo estará validado quando uma solicitação de marketing puder atravessar múltiplos especialistas sem que o usuário precise manualmente coordenar cada agente, mantendo:

- clareza de responsabilidade;
- qualidade da entrega;
- rastreabilidade;
- baixo custo;
- controle de risco;
- contexto consistente da 7Ps;
- capacidade de revisão e aprovação.
