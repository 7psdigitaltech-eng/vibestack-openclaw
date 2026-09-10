# Documentação — 7Ps Marketing AI

Este diretório concentra a documentação específica do fork `7psdigitaltech-eng/vibestack-openclaw` para transformação do projeto original em uma **agência de marketing com IA da 7Ps**.

A documentação do projeto original continua válida como referência técnica da stack upstream. Os documentos abaixo registram as decisões próprias da 7Ps e passam a orientar a evolução do fork.

## Documentos principais

### 1. Arquitetura V1

[`7ps-marketing-ai-v1-architecture.md`](./7ps-marketing-ai-v1-architecture.md)

Contém:

- objetivo da V1;
- estratégia de infraestrutura;
- decisão por Docker Desktop sem Docker-in-Docker;
- Hermes como runtime principal;
- Ollama e modelo local;
- Telegram como canal;
- profiles;
- skills;
- subagentes;
- Kanban;
- tools/MCP;
- knowledge base;
- Meta Ads, Google Ads e ferramentas criativas;
- limites entre V1 e V2.

### 2. Organograma e operação dos agentes

[`7ps-marketing-ai-v1-agents.md`](./7ps-marketing-ai-v1-agents.md)

Contém:

- sete profiles permanentes;
- responsabilidades de cada agente;
- inputs e outputs;
- autonomia;
- tools;
- skills;
- handoffs;
- gates de aprovação;
- uso de Kanban e `delegate_task`;
- exemplo de campanha multiagente.

### 3. Roadmap de implementação

[`7ps-marketing-ai-v1-roadmap.md`](./7ps-marketing-ai-v1-roadmap.md)

Contém a sequência de implementação por fases e critérios de aceite, desde a simplificação da stack até Telegram, profiles, skills, Kanban, ferramentas criativas e preparação do Gestor de Tráfego.

---

## Decisões V1 consolidadas

| Área | Decisão |
|---|---|
| Produto | Agência de marketing com IA da 7Ps |
| Uso inicial | operação interna da 7Ps |
| Runtime | Hermes Agent |
| OpenClaw | desabilitado na V1 |
| Modelo | local, via Ollama |
| Canal principal | Telegram |
| WhatsApp/Evolution | fora da V1 |
| Infraestrutura | Docker Desktop como host |
| Docker-in-Docker | não usar |
| Profiles | 7 especialistas persistentes |
| Skills | metodologia operacional 7Ps |
| Subagents | trabalho temporário/isolado |
| Kanban | coordenação durável entre profiles |
| Meta Ads | integração preservada; configuração posterior |
| Google Ads | integração preservada; configuração posterior |
| Ferramentas criativas | preservar e integrar conforme viabilidade |
| Multi-clínica | V2 |
| CliniSmart | integração futura, após validação interna |

---

## Profiles V1

```text
1. diretor
2. estrategista
3. analista
4. copywriter
5. criativo
6. conteudo
7. gestor-trafego
```

O Diretor será a interface principal com o usuário via Telegram.

---

## Regra arquitetural central

```text
Profile   = funcionário/departamento persistente
Skill     = metodologia, conhecimento ou procedimento
Subagent  = força de trabalho temporária
Kanban    = coordenação e estado durável do trabalho
Tool/MCP  = capacidade prática de execução
Knowledge = fonte oficial de verdade da 7Ps
```

---

## Estado atual

**Planejamento arquitetural V1 documentado.**

Próximo checkpoint: iniciar a **Fase 0/Fase 1 do roadmap**, preparar a estrutura local definitiva em `D:\Docker\7ps-marketing`, clonar o fork para a localização final e adaptar a stack para Hermes-only preservando as integrações planejadas.
