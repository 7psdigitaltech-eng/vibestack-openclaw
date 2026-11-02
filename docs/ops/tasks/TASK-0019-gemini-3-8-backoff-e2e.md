# TASK-0019 — Revalidar Gemini 3.8 Flash com backoff e prova end-to-end mínima

Status: READY

## Objetivo

Revalidar `gemini-3.8-flash` após o erro upstream Google 503 observado na TASK-0018.

Esta TASK deve distinguir:

1. indisponibilidade transitória do Google;
2. indisponibilidade recorrente que inviabiliza o modelo como backend principal;
3. eventual incompatibilidade posterior entre Hermes e FreeLLMAPI.

Não trocar de provider ou modelo nesta TASK.

## Contexto confirmado

- FreeLLMAPI local contém `gemini-3.8-flash`;
- provider Google está healthy;
- projeto Google está no Free Tier, sem billing;
- custo autorizado: R$ 0,00 / US$ 0,00;
- primeiro teste da TASK-0018 retornou:
  - FreeLLMAPI HTTP 502;
  - upstream Google HTTP 503;
- Google documenta 503 como serviço temporariamente sobrecarregado/indisponível e recomenda retry com exponential backoff;
- relatos recentes no Google AI Developers Forum mostram 503 especificamente no Gemini 3.8 Flash Free Tier por alta demanda;
- Ollama está sem modelo carregado;
- Groq permanece configurado, mas não será usado nesta TASK;
- unified key atual do FreeLLMAPI permanece em uso por decisão explícita do usuário; não rotacionar nesta TASK.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 20 minutos
Corrective attempts: máximo 3 para 503
Model downloads allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Billing allowed: NÃO
Premium allowed: NÃO
Fallback cross-provider allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## Segurança

Não:

- exibir unified key;
- exibir Google/Groq keys;
- criar/rotacionar chaves;
- habilitar billing;
- alterar Premium;
- enviar dados reais/confidenciais.

Usar apenas prompts sintéticos.

## PRE-CHECK

Confirmar:

- FreeLLMAPI healthy;
- Google key/provider healthy;
- Groq healthy;
- Hermes gateway/dashboard healthy;
- Ollama sem modelo carregado;
- `gemini-3.8-flash` presente no catálogo;
- Google Free Tier sem billing;
- custo zero.

## 1. Prova direta com backoff

Fixar:

```text
model = gemini-3.8-flash
```

Prompt:

```text
Responda apenas: GEMINI 3.8 ONLINE
```

Não enviar tools.
Não enviar sampling params opcionais.
Não usar `auto`.

Executar no máximo 4 tentativas totais:

- tentativa 1: imediata;
- se 503: aguardar ~10s;
- tentativa 2;
- se 503: aguardar ~30s;
- tentativa 3;
- se 503: aguardar ~60s;
- tentativa 4.

Adicionar pequeno jitter quando simples de fazer, sem ultrapassar o hard budget.

Registrar para cada tentativa:

- HTTP FreeLLMAPI;
- classe upstream sanitizada;
- latência;
- X-Routed-Via se disponível;
- X-Fallback-Trail se disponível.

Não registrar conteúdo de segredo/header de autenticação.

### Gate A

Se todas as quatro tentativas resultarem em Google 503:

```text
GEMINI_3_8_UNAVAILABLE_RECURRENT
```

Retornar BLOCKED.

Isso reprova a estabilidade atual como backend principal, mas NÃO significa chave inválida ou modelo inexistente.

Se qualquer tentativa retornar HTTP 200, prosseguir.

## 2. Teste único de planejamento direto

Após um HTTP 200, aguardar pelo menos 20 segundos.

Executar apenas:

```text
Uma empresa recebe 100 leads por mês, converte 10 em reunião e fecha 2 clientes. Sem assumir dados ausentes, diga o que pode e o que não pode ser concluído e proponha três hipóteses de teste priorizadas.
```

Avaliar 0–5:

- português;
- aderência;
- planejamento;
- tratamento de incerteza.

Gate mínimo: todos >=3.

Se ocorrer 503:
- uma única repetição após ~30s;
- se persistir, BLOCKED por estabilidade.

Se qualidade <3 em qualquer critério decisivo:
- FAILED qualidade;
- não executar Hermes.

## 3. Resfriamento

Aguardar pelo menos 60 segundos antes da prova Hermes.

## 4. Configurar Hermes temporariamente

Configurar:

```text
provider = custom
base_url = http://freellmapi:3001/v1
model.default = gemini-3.8-flash
model.context_length = 131072
```

Para esta prova, desabilitar raciocínio explícito por sessão/config temporária equivalente a:

```text
reasoning = none
```

Objetivo: evitar que o Hermes envie `reasoning_effort` e que o adapter FreeLLMAPI converta isso para um parâmetro legado durante a primeira validação.

Não alterar permanentemente a política global de reasoning sem evidência.

## 5. Hermes one-shot mínimo

Usar tool surface:

```text
terminal
```

Prompt:

```text
Responda apenas: DIRETOR 3.8 OK
```

Se 503:
- respeitar backoff;
- repetir UMA vez após ~30s.

PASS se:

- resposta correta;
- modelo = gemini-3.8-flash;
- sem 413;
- sem 400 de parâmetro;
- sem 429;
- sem 5xx persistente.

## 6. Tool calling real

Após novo intervalo de pelo menos 30 segundos:

```text
Use a ferramenta de terminal para listar apenas os nomes dos arquivos Markdown do diretório atual e informe quantos encontrou.
```

PASS somente se houver:

1. tool call real;
2. execução real;
3. tool result;
4. resposta final baseada no resultado.

Se 503:
- uma repetição após backoff;
- se persistir, BLOCKED estabilidade.

Se erro 400 relacionado a reasoning/thinking/sampling:
- registrar mensagem sanitizada;
- NÃO patchar FreeLLMAPI;
- retornar BLOCKED compatibilidade.

## 7. Decisão

### PASS

Aprovar `gemini-3.8-flash` como backend principal provisório do Diretor se:

- ao menos uma prova direta HTTP 200;
- planejamento direto >=3 em todos os critérios;
- Hermes one-shot PASS;
- terminal tool calling PASS;
- sem 503 persistente;
- sem 429;
- sem incompatibilidade de parâmetros;
- custo zero.

Persistir:

```text
provider = custom
base_url = http://freellmapi:3001/v1
model.default = gemini-3.8-flash
model.context_length = 131072
```

Manter reasoning sem override global até próxima TASK específica.

Tool surface permanece role-scoped/demand-scoped.

### BLOCKED

Se a disponibilidade continuar instável:

- remover model.default;
- restaurar Hermes sem backend aprovado;
- manter Google/Groq configurados;
- manter Ollama sem carga;
- registrar `GEMINI_3_8_UNAVAILABLE_RECURRENT`.

Próximo passo recomendado nesse caso: testar um terceiro provider gratuito compatível com >=64K + tools, em vez de insistir em novas tentativas Google no mesmo momento.

## POST-CHECK

Confirmar:

- Hermes/dashboard healthy;
- FreeLLMAPI healthy;
- Google/Groq preservados;
- Ollama sem carga;
- custo zero;
- nenhum segredo exposto.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md`;
- ADR-0001 somente se houver PASS ou evidência forte de indisponibilidade recorrente.

Fazer commit + push.

## Retorno

```text
TASK-0019: PASS | BLOCKED | FAILED
Commit: <sha>
Gemini 3.8 tentativas diretas: <resumo>
Planejamento direto: <nota/resumo>
Hermes one-shot: <status>
Tool calling: <status>
503/429/400: <resumo>
Modelo selecionado: <gemini-3.8-flash | nenhum>
Contexto: <131072 | nenhum>
Reasoning teste: none
Groq: preservado
Ollama: sem carga
Custo: R$ 0,00 / US$ 0,00
Bloqueio: <nenhum ou curto>
```
