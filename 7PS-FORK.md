# 7Ps Marketing AI — visão do fork

Este fork do `vibestack-openclaw` será evoluído para a realidade da **7Ps Digital Tech**, usando a base técnica do projeto original para construir uma **agência de marketing com IA**.

O objetivo da V1 não é oferecer um produto multiempresa nem automatizar imediatamente a publicação de campanhas. A primeira versão será usada internamente pela 7Ps para validar metodologia, papéis dos agentes, fluxos, autonomia, ferramentas, qualidade das entregas e custo operacional.

Após essa validação, a V2 poderá transformar a estrutura em parte da metodologia de marketing oferecida às clínicas atendidas pela 7Ps, com isolamento de contexto, dados e execução por cliente.

## Documentação oficial do fork 7Ps

A documentação de planejamento está em:

- [`docs/7ps-marketing-ai/README.md`](docs/7ps-marketing-ai/README.md) — visão, escopo e princípios da V1.
- [`docs/7ps-marketing-ai/architecture-v1.md`](docs/7ps-marketing-ai/architecture-v1.md) — arquitetura técnica e de infraestrutura.
- [`docs/7ps-marketing-ai/agents-operating-model-v1.md`](docs/7ps-marketing-ai/agents-operating-model-v1.md) — organograma, profiles, skills, subagentes, Kanban, autonomia e handoffs.
- [`docs/7ps-marketing-ai/implementation-roadmap-v1.md`](docs/7ps-marketing-ai/implementation-roadmap-v1.md) — fases de implementação e critérios de aceite.
- [`docs/7ps-marketing-ai/decisions.md`](docs/7ps-marketing-ai/decisions.md) — decisões arquiteturais registradas.

## Direção resumida

- Runtime principal: **Hermes Agent**.
- LLM inicial: **modelo local via Ollama**, buscando custo zero ou mínimo.
- Canal humano principal: **Telegram**.
- OpenClaw: não será o runtime principal da V1.
- WhatsApp/Evolution: fora do escopo inicial.
- Meta Ads: integração preservada, porém a configuração de credenciais e publicação real será feita depois.
- Gestor de Tráfego: já fará parte do organograma da V1, mas sem permissão operacional de publicação enquanto Meta Ads não estiver configurado.
- Ferramentas especializadas de criação, mídia e Ads serão preservadas, pois complementam as limitações do modelo local.
- Docker Desktop será o host das aplicações. Não será usado Docker-in-Docker como arquitetura padrão.
- A V1 atende apenas a 7Ps. Multi-cliente/multi-clínica fica para V2.

> Este arquivo serve como ponto de entrada para as decisões específicas do fork. O README original continua sendo referência para a stack e para o funcionamento herdado do projeto upstream.
