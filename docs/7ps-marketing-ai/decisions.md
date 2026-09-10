# Registro de Decisões — 7Ps Marketing AI

Este arquivo registra as principais decisões arquiteturais e de produto da V1.

Formato resumido:

- **Decisão** — o que foi escolhido.
- **Motivo** — por que foi escolhido.
- **Consequência** — impacto esperado.
- **Status** — estado atual.

---

## ADR-001 — Transformar o fork em agência de marketing com IA da 7Ps

**Decisão:** o fork deixa de ser tratado apenas como reprodução de uma agência de tráfego e passa a ser a base da **7Ps Marketing AI**, uma agência de marketing mais ampla.

**Motivo:** a estratégia da 7Ps exige capacidades além de mídia paga: pesquisa, estratégia, conteúdo, copy, criativo, análise e futura integração com CRM/comercial.

**Consequência:** o diretório `agency/` e os fluxos originais deverão ser adaptados para o organograma e metodologia 7Ps.

**Status:** aprovado.

---

## ADR-002 — V1 será usada apenas pela própria 7Ps

**Decisão:** não implementar multi-clínica na primeira versão.

**Motivo:** validar metodologia e operação antes de adicionar complexidade de isolamento, credenciais e multi-tenant.

**Consequência:** um único contexto organizacional e uma única knowledge base principal na V1.

**Status:** aprovado.

---

## ADR-003 — Multi-clínica será V2

**Decisão:** isolamento entre clínicas será projetado depois da validação interna.

**Motivo:** evitar engenharia prematura.

**Consequência:** V2 deverá avaliar boards, tenants, workspaces, profiles e credenciais separados, além de integração com CliniSmart.

**Status:** adiado para V2.

---

## ADR-004 — Hermes Agent será o runtime principal

**Decisão:** usar Hermes Agent em vez de OpenClaw como núcleo operacional da V1.

**Motivo:** profiles persistentes, skills, delegação, Kanban multiagente, gateway de mensageria, dashboard e melhor aderência ao modelo operacional desejado.

**Consequência:** configuração alvo `INSTALL_OPENCLAW=false` e `INSTALL_HERMES=true`.

**Status:** aprovado.

---

## ADR-005 — Ollama e modelo local primeiro

**Decisão:** começar com modelos locais via Ollama.

**Motivo:** reduzir custo recorrente ao mínimo e evitar limitações de planos gratuitos em nuvem.

**Consequência:** haverá benchmark de modelos locais e limitação inicial de paralelismo.

**Status:** aprovado.

---

## ADR-006 — Escolha final do modelo por benchmark

**Decisão:** não fixar um modelo apenas por reputação ou tamanho.

**Motivo:** marketing agentic exige equilíbrio entre RAM, contexto, tool calling, português, velocidade e qualidade.

**Consequência:** modelos candidatos serão testados em tarefas reais antes de padronização.

**Status:** aprovado.

---

## ADR-007 — Telegram será o canal principal

**Decisão:** substituir WhatsApp/Evolution por Telegram na V1.

**Motivo:** menor complexidade, menor risco de bloqueio, menos serviços auxiliares e integração nativa com Hermes.

**Consequência:** Evolution Go e seu Postgres não subirão inicialmente.

**Status:** aprovado.

---

## ADR-008 — Apenas o Diretor será exposto ao Telegram inicialmente

**Decisão:** o usuário conversa com um único profile público.

**Motivo:** manter uma porta de entrada simples e evitar obrigar o usuário a coordenar especialistas manualmente.

**Consequência:** especialistas trabalham internamente via Kanban/delegação.

**Status:** aprovado.

---

## ADR-009 — Docker Desktop será o host das aplicações

**Decisão:** cada projeto será uma stack irmã no Docker Desktop.

**Motivo:** isolamento simples de redes, volumes, Compose, portas e configuração.

**Consequência:** projetos futuros como CliniSmart, n8n e outros não precisam rodar dentro do container da agência.

**Status:** aprovado.

---

## ADR-010 — Não usar Docker-in-Docker como arquitetura padrão

**Decisão:** não instalar um Docker Engine dentro do `debian-vps` para hospedar a 7Ps Marketing AI.

**Motivo:** complexidade e risco desnecessários.

**Consequência:** o `debian-vps` pode permanecer apenas como laboratório Linux genérico.

**Status:** aprovado.

---

## ADR-011 — Dados principais serão direcionados ao disco D:

**Decisão:** usar armazenamento persistente em `D:\Docker\...` sempre que adequado.

**Motivo:** maior capacidade de disco e melhor organização dos projetos.

**Consequência:** bind mounts/volumes serão planejados para Hermes, Ollama, knowledge, outputs, logs e backups.

**Status:** aprovado; caminhos finais serão validados na implementação.

---

## ADR-012 — Sete profiles persistentes na V1

**Decisão:** criar os profiles:

- Diretor;
- Estrategista;
- Analista;
- Copywriter;
- Criativo;
- Conteúdo;
- Gestor de Tráfego.

**Motivo:** cada papel precisa de identidade, memória, estado e responsabilidade persistentes.

**Consequência:** todos podem usar inicialmente o mesmo modelo Ollama, mas com SOUL, skills e toolsets diferentes.

**Status:** aprovado.

---

## ADR-013 — Pesquisa começa como skill + subagent

**Decisão:** não criar inicialmente um profile Pesquisador.

**Motivo:** pesquisa é uma capacidade transversal e pode ser executada sob demanda.

**Consequência:** poderá virar profile no futuro caso precise memória, cron e processo persistentes.

**Status:** aprovado.

---

## ADR-014 — Skills representam metodologia, não pessoas

**Decisão:** skills deverão representar procedimentos e conhecimento operacional reutilizável.

**Motivo:** separar papel organizacional de método de execução.

**Consequência:** uma mesma skill poderá ser usada por diferentes profiles quando apropriado.

**Status:** aprovado.

---

## ADR-015 — Kanban será o mecanismo de coordenação durável

**Decisão:** handoffs importantes entre profiles deverão usar o Kanban Hermes.

**Motivo:** tarefas precisam sobreviver a restart, ter dependências, revisão, bloqueio e histórico.

**Consequência:** campanhas e projetos serão decompostos em cards atribuídos a especialistas.

**Status:** aprovado.

---

## ADR-016 — Subagents serão trabalhadores temporários

**Decisão:** usar subagents para pesquisas e análises isoladas e temporárias.

**Motivo:** permitir paralelismo e reduzir poluição de contexto sem criar dezenas de profiles permanentes.

**Consequência:** concorrência inicial de 1–2 até benchmark de hardware.

**Status:** aprovado.

---

## ADR-017 — Knowledge base será fonte oficial de verdade

**Decisão:** fatos oficiais da 7Ps não devem depender da memória individual dos agents.

**Motivo:** memórias podem divergir, envelhecer ou refletir contexto conversacional.

**Consequência:** haverá `knowledge/7ps/` compartilhado e versionado quando apropriado.

**Status:** aprovado.

---

## ADR-018 — Meta Ads será preservado, mas configuração será posterior

**Decisão:** manter a integração e o desenho do Gestor de Tráfego, porém adiar credenciais e publicação real.

**Motivo:** evitar custo/complexidade imediata sem precisar redesenhar a arquitetura posteriormente.

**Consequência:** Gestor poderá planejar antes de ter capacidade de write.

**Status:** aprovado.

---

## ADR-019 — Gestor de Tráfego existe desde a V1

**Decisão:** não adiar a criação do profile Gestor.

**Motivo:** mídia paga é parte estrutural do organograma e das campanhas, mesmo antes da execução automática.

**Consequência:** ele operará inicialmente em modo planejamento/preparação.

**Status:** aprovado.

---

## ADR-020 — Integrado não significa autorizado

**Decisão:** classificar ferramentas em três estados independentes: instalado, configurado e autorizado.

**Motivo:** uma ferramenta pode ser útil no runtime sem possuir credenciais, e uma ferramenta configurada pode precisar restrição por agente.

**Consequência:** permissões deverão ser explícitas e auditáveis.

**Status:** aprovado.

---

## ADR-021 — Preservar ferramentas especializadas de mídia e Ads

**Decisão:** Higgsfield, AtlasCloud, Media Editor, Meta Ads e Google Ads permanecem na visão arquitetural.

**Motivo:** ferramentas especializadas compensam limitações do LLM local e melhoram a qualidade prática das entregas.

**Consequência:** não serão removidas apenas por estarem sem credenciais na primeira instalação.

**Status:** aprovado.

---

## ADR-022 — Criativo deve usar ferramentas visuais especializadas

**Decisão:** o agente Criativo não deve depender apenas do modelo textual para design.

**Motivo:** direção de arte e geração visual performam melhor quando o LLM consegue acionar ferramentas próprias de imagem/vídeo/edição.

**Consequência:** toolset visual será prioridade na evolução do Criativo.

**Status:** aprovado.

---

## ADR-023 — Analista não escreve em plataformas de mídia

**Decisão:** Analista será read-only em integrações de Ads.

**Motivo:** separar medição de execução reduz risco e conflito de responsabilidade.

**Consequência:** alterações de campanha passam pelo Gestor.

**Status:** aprovado.

---

## ADR-024 — Gestor será o principal profile com permissão futura de write em Ads

**Decisão:** mutações de campanha serão centralizadas no Gestor de Tráfego.

**Motivo:** reduzir superfície de risco e manter auditoria clara.

**Consequência:** Diretor e Estrategista decidem/aprovam, mas não executam diretamente write no Meta/Google.

**Status:** aprovado para desenho; política fina de alçada será definida antes de habilitar write.

---

## ADR-025 — Autonomia será progressiva

**Decisão:** começar automatizando ações reversíveis/baixo risco e só depois liberar ações externas de impacto.

**Motivo:** confiança precisa ser conquistada através de validação operacional.

**Consequência:** publicação, budget e mutações importantes terão gates de aprovação inicialmente.

**Status:** aprovado.

---

## ADR-026 — Não refatorar todo o upstream antes da primeira execução

**Decisão:** preservar a base funcional do fork e adaptar incrementalmente.

**Motivo:** uma refatoração grande antes de validar Hermes/Ollama/Telegram aumentaria risco sem evidência.

**Consequência:** mudanças serão feitas por fases, mantendo capacidade de comparar com o upstream.

**Status:** aprovado.

---

## ADR-027 — V1 deve ser validada em trabalho real da 7Ps

**Decisão:** sucesso não será medido apenas por smoke tests técnicos.

**Motivo:** o objetivo é uma agência funcional, não somente uma stack que sobe.

**Consequência:** haverá ciclos reais de campanhas, conteúdo e planejamento antes de iniciar V2.

**Status:** aprovado.
