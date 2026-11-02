# TASK-0018 — Gemini 3.8 Flash como candidato principal do Diretor

Status: READY

## Objetivo

Validar `gemini-3.8-flash` como backend principal provisório do Diretor via:

```text
Hermes -> FreeLLMAPI -> Google AI Studio -> Gemini 3.8 Flash
```

A TASK deve resolver também a divergência entre o catálogo local do FreeLLMAPI e os modelos realmente disponíveis no projeto Google.

## Contexto confirmado

- Gemini 2.5 Flash retornou `404 model_not_found`;
- a documentação atual do Google restringe Gemini 2.5 a projetos/usuários que já o utilizavam anteriormente;
- para projetos novos, o Google recomenda modelos atuais como Gemini 3.5 Flash-Lite e Gemini 3.8 Flash;
- Gemini 3.8 Flash é modelo estável atual, com:
  - contexto de entrada 1.048.576;
  - output máximo 65.536;
  - function calling;
  - thinking;
  - foco explícito em agentes autônomos e workflows complexos;
- o projeto Google do usuário está no Free Tier, sem billing;
- o AI Studio do usuário mostra para Gemini 3.8 Flash:
  - 5 RPM;
  - 250K TPM;
  - 20 RPD;
- Google e Groq estão configurados e healthy no FreeLLMAPI;
- unified key atual do FreeLLMAPI permanece em uso por decisão explícita do usuário;
- custo autorizado continua R$ 0,00 / US$ 0,00;
- Ollama permanece auxiliar, sem modelo carregado;
- tool surface do Diretor deve permanecer role-scoped/demand-scoped.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 30 minutos
Corrective attempts: máximo 2
Model downloads allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Billing allowed: NÃO
Premium FreeLLMAPI allowed: NÃO
Other new providers allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## Segurança

Não:

- exibir unified key;
- exibir provider keys;
- rotacionar unified key nesta TASK;
- criar novas chaves;
- habilitar billing;
- habilitar Premium;
- enviar dados reais/confidenciais.

Usar somente dados sintéticos.

## PRE-CHECK

Confirmar:

- FreeLLMAPI healthy;
- Google healthy;
- Groq healthy;
- Hermes gateway/dashboard healthy;
- Ollama sem modelo carregado;
- Google Free Tier continua sem billing;
- custo permanece zero.

## 1. Verificar disponibilidade no FreeLLMAPI

Usando a unified key já armazenada localmente e sem exibi-la, consultar:

```text
GET http://freellmapi:3001/v1/models
```

Procurar exatamente:

```text
gemini-3.8-flash
```

Registrar apenas:

```text
LOCAL_CATALOG_3_8=YES | NO
```

### Se presente

Prosseguir para a etapa 3.

### Se ausente

Prosseguir para a etapa 2.

## 2. Reconciliar catálogo FreeLLMAPI

O FreeLLMAPI Free usa catálogo mensal e sincronização automática.

Executar uma única reinicialização do serviço FreeLLMAPI para revalidar o estado/sync:

```bash
docker compose restart freellmapi
```

Aguardar o serviço ficar healthy.

Consultar novamente `/v1/models`.

### Se aparecer

Prosseguir normalmente.

### Se continuar ausente

NÃO:

- editar SQLite manualmente;
- habilitar Premium;
- alterar código do FreeLLMAPI;
- atualizar versão do container apenas para tentar obter catálogo novo.

Retornar:

```text
USER_ACTION_REQUIRED_ADD_MODEL
```

e informar que o usuário deve usar o mecanismo suportado do dashboard FreeLLMAPI:

```text
Chaves -> Google -> Adicionar modelo
```

Valores a informar:

```text
Provider: Google
Model ID: gemini-3.8-flash
Display name: Gemini 3.8 Flash
Context window: 1048576
Supports tools: YES
Supports vision: YES
RPM: 5
TPM: 250000
RPD: 20
```

Não solicitar a chave Google novamente.

Após o usuário adicionar o modelo, esta mesma TASK pode ser retomada da etapa 3.

## 3. Teste direto de existência real

Antes de benchmark completo, enviar UMA solicitação curta diretamente pelo FreeLLMAPI fixando:

```text
model = gemini-3.8-flash
```

Prompt:

```text
Responda apenas: GEMINI 3.8 ONLINE
```

Não enviar sampling params opcionais explicitamente nesta prova.

Registrar:

- HTTP;
- latência;
- modelo efetivamente servido;
- fallback, se houver.

### Gate

Se retornar `model_not_found`, registrar divergência catálogo/provider e retornar BLOCKED.

Se retornar erro de parâmetro incompatível, registrar mensagem sanitizada e retornar BLOCKED sem patchar FreeLLMAPI.

Se HTTP 200, prosseguir.

## 4. Benchmark direto de qualidade

Respeitar o limite de 5 RPM.

Aguardar pelo menos 15 segundos entre chamadas diretas.

### A — português/instrução

```text
Explique em português, em no máximo 5 linhas, a diferença entre estratégia de marketing e execução de campanha.
```

### B — planejamento com incerteza

```text
Uma empresa B2B recebe 100 leads por mês, converte 10 em reunião e fecha 2 clientes. Sem assumir dados ausentes, diga o que pode e o que não pode ser concluído. Depois proponha três hipóteses de teste priorizadas e a métrica que validaria cada uma.
```

### C — diagnóstico

```text
Uma empresa tem tráfego suficiente no site, mas não informou taxa de conversão, origem dos leads nem ticket médio. Monte um diagnóstico em quatro etapas separando fatos, hipóteses e dados ainda necessários.
```

Notas 0–5 para:

- português;
- seguir instruções;
- planejamento;
- tratamento de incerteza.

### Gate de qualidade

Prosseguir para Hermes apenas se:

- português >= 3;
- instruções >= 3;
- planejamento >= 3;
- incerteza >= 3;
- sem falsa certeza grave.

## 5. Resfriamento de quota

Após os testes diretos, aguardar pelo menos 60 segundos antes dos testes Hermes.

Não usar outras chamadas Google durante esse intervalo.

## 6. Configurar Hermes temporariamente

Configurar:

```text
provider = custom
base_url = http://freellmapi:3001/v1
model.default = gemini-3.8-flash
context_length = 131072
```

Não usar 1M como contexto operacional inicial.

Não alterar unified key.

## 7. Hermes one-shot com tool surface mínima

Usar apenas:

```text
terminal,web,todo
```

Executar com override por sessão.

Prompt:

```text
Responda apenas: DIRETOR GEMINI 3.8 OK
```

PASS se:

- resposta correta;
- sem 413;
- sem 429;
- sem 5xx;
- modelo efetivo = gemini-3.8-flash.

## 8. Tool calling real

Executar somente:

```text
Use a ferramenta de terminal para listar apenas os nomes dos arquivos Markdown do diretório atual e informe quantos encontrou.
```

PASS somente com:

1. tool_use real;
2. execução real;
3. tool_result;
4. resposta final baseada no resultado.

Se houver 429, respeitar Retry-After e repetir UMA vez.

## 9. Planejamento via Hermes

Após novo intervalo adequado ao rate limit, executar:

```text
Uma empresa recebe 100 leads por mês, mas não informou origem dos leads, taxa de qualificação nem ticket médio. Estruture um diagnóstico em três passos sem inventar qual é o gargalo.
```

Nota mínima: 3/5.

Critérios:

- identifica informação insuficiente;
- separa fatos e hipóteses;
- prioriza evidência;
- não inventa certeza;
- resposta utilizável.

## 10. Aprovação

### PASS

Aprovar `gemini-3.8-flash` como backend principal provisório do Diretor se:

- teste direto HTTP 200;
- qualidade direta >=3 em todos os critérios;
- Hermes one-shot PASS;
- terminal tool calling PASS;
- planejamento Hermes >=3;
- sem 413;
- sem 429 recorrente;
- sem 5xx persistente;
- custo zero.

Persistir:

```text
provider = custom
base_url = http://freellmapi:3001/v1
model.default = gemini-3.8-flash
model.context_length = 131072
```

Política de tools:

```text
role-scoped/demand-scoped
default inicial do Diretor = terminal,web,todo
```

Groq permanece fallback/chat rápido.
Ollama permanece auxiliar/local.

### FAIL

Se falhar critério decisivo:

- remover model.default;
- restaurar estado sem backend aprovado;
- preservar Google/Groq;
- preservar Ollama sem carga;
- registrar motivo.

## 11. Restart/persistência

Somente se PASS:

1. reiniciar Hermes;
2. confirmar gateway/dashboard;
3. executar um one-shot curto;
4. confirmar model/provider persistidos;
5. confirmar Ollama sem modelo carregado.

## Fora de escopo

Não:

- testar Gemini 2.5 novamente;
- testar Gemini 3.1 Flash Lite novamente;
- testar Gemini 3.6 Flash novamente;
- atualizar FreeLLMAPI para v0.16.17 nesta TASK;
- habilitar Premium;
- adicionar outro provider;
- mudar arquitetura.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md`;
- `docs/ops/decisions/ADR-0001-freellmapi-evaluation.md`.

Registrar explicitamente a causa do 404 do Gemini 2.5 como restrição de acesso para projetos novos, não falha da chave.

Se PASS, consolidar no ADR:

```text
FreeLLMAPI = router primário da V1
Gemini 3.8 Flash = backend principal provisório do Diretor
Groq = fallback/chat rápido
Ollama = auxiliar/local
Tool surface = role-scoped/demand-scoped
```

Fazer commit + push.

## Retorno

```text
TASK-0018: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
Catálogo 3.8: <presente/adicionado/ausente>
Gemini 3.8 direto: <status + notas>
Hermes one-shot: <status>
Tool calling: <status>
Planejamento Hermes: <nota>
429/5xx: <resumo>
Modelo selecionado: <gemini-3.8-flash | nenhum>
Contexto: <131072 | nenhum>
Groq: <fallback preservado/status>
Ollama: <sem carga/status>
Custo: R$ 0,00 / US$ 0,00
Ação humana: <nenhuma | adicionar modelo no FreeLLMAPI>
Bloqueio: <nenhum ou curto>
```
