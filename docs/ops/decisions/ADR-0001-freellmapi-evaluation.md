# ADR-0001 — Avaliar FreeLLMAPI como router primário de inferência da V1

Status: APPROVED_FOR_EVALUATION
Data: 2026-11-02

## Contexto

A arquitetura inicial da 7Ps Marketing AI priorizou Ollama local como backend do Hermes.

Os benchmarks TASK-0011 e TASK-0012 mostraram:

- `qwen3:4b` e `llama3.2:3b` funcionam diretamente no Ollama em contexto reduzido;
- Hermes Agent 0.21.1 exige contexto mínimo de 64.000 tokens;
- 64K pressionou excessivamente a memória disponível ao Docker (~9,7 GiB);
- 16K é rejeitado pelo Hermes antes do fluxo agentic/tool calling;
- nenhum modelo local foi aprovado para o Diretor.

O FreeLLMAPI oferece um endpoint OpenAI-compatible local, integração explícita com Hermes Agent, roteamento por provedores externos, suporte a tool calling e catálogo com modelos de contexto >=64K.

## Decisão para avaliação

Avaliar o FreeLLMAPI como camada de roteamento primária entre Hermes e provedores externos gratuitos/freemium.

Arquitetura experimental:

```text
Hermes
  |
  v
FreeLLMAPI (container local)
  |
  +--> provider remoto A
  +--> provider remoto B
  +--> fallback
```

Ollama permanece instalado como backend auxiliar/experimental e não será removido.

## Regras

- FreeLLMAPI será inicialmente executado como serviço separado dentro da stack Docker do projeto;
- persistência própria em `D:/Docker/7ps-marketing/data/freellmapi`;
- dashboard/API publicado somente em loopback do host;
- Hermes e FreeLLMAPI se comunicam pela rede interna do Compose;
- nenhuma credencial de provedor será commitada;
- somente modelos com contexto >=64K e suporte a tools poderão compor a cadeia do Diretor;
- a decisão de tornar FreeLLMAPI o backend primário só será consolidada após benchmark real Hermes + tool calling.

## Relação com a arquitetura existente

Esta decisão não remove o princípio de custo operacional mínimo.

Ela altera apenas a interpretação de "prioridade para modelos locais": o benchmark local já foi executado e não aprovou um modelo para o Diretor no hardware atual. A opção híbrida/nuvem é, portanto, a próxima etapa prevista pelo roadmap.

## Critério para consolidação

FreeLLMAPI será adotado como backend primário da V1 se:

- container local operar de forma estável e leve;
- Hermes acessar o endpoint pela rede Docker;
- pelo menos um provider/modelo >=64K + tools responder de forma utilizável;
- tool calling real funcionar pelo Hermes;
- qualidade superar os candidatos locais testados;
- limites gratuitos forem aceitáveis para validação interna.

Até lá, o status permanece APPROVED_FOR_EVALUATION.

## Evidencia adicional — TASK-0016

Gemini via FreeLLMAPI realizou tool calling real em superficies restritas e com dados sinteticos; resultado de planejamento mostrou diagnostico nao sustentado, e a chave unificada foi exibida pelo painel na saida da ferramenta. A chave precisa ser regenerada antes de nova avaliacao. Tools do Diretor devem ser role-scoped/demand-scoped. A decisao arquitetural permanece APPROVED_FOR_EVALUATION; nenhum backend foi aprovado por esta TASK.

## Evidencia adicional — TASK-0017

O catalogo local indicou gemini-2.5-flash como disponivel, mas a primeira chamada ao Google retornou 404 e foi classificada model_not_found pelo router. A disponibilidade real permanece nao comprovada; nenhum backend foi aprovado nesta TASK. O status do ADR permanece APPROVED_FOR_EVALUATION.
