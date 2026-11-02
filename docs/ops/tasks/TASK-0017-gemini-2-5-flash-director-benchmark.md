# TASK-0017 — Benchmark decisivo Gemini 2.5 Flash para o Diretor

Status: READY

## Objetivo

Validar se `gemini-2.5-flash` pode ser aprovado como backend principal do Diretor da 7Ps Marketing AI via:

```text
Hermes -> FreeLLMAPI -> Google AI Studio -> Gemini 2.5 Flash
```

A TASK deve responder uma única pergunta:

> O Gemini 2.5 Flash entrega qualidade de planejamento suficiente E mantém tool calling real estável com a superfície reduzida do Diretor?

Se sim, aprovar como backend principal provisório da V1.
Se não, registrar reprovação e encerrar esta rodada de Google Free sem testar modelos adicionais nesta TASK.

## Contexto confirmado

- Google AI Studio: Free Tier, sem billing;
- custo autorizado: R$ 0,00 / US$ 0,00;
- provider Google healthy no FreeLLMAPI;
- `gemini-3.1-flash-lite`: tool calling real PASS, planejamento 2/5;
- `gemini-3.6-flash`: erro 502 no planejamento;
- superfície reduzida `terminal,web,todo`: PASS em tool call real;
- biblioteca completa de tools NÃO deve ser enviada;
- unified API key atual do FreeLLMAPI permanece em uso por decisão explícita do usuário; rotação adiada;
- não exibir a unified key;
- Groq permanece configurado como fallback/chat rápido;
- Ollama permanece auxiliar e sem modelo carregado.

## Modelo alvo

```text
gemini-2.5-flash
```

Critérios conhecidos:

- contexto reportado pelo FreeLLMAPI: >= 64K;
- Google AI Studio Free Tier: 250K TPM;
- limite observado no projeto do usuário: 5 RPM / 250K TPM / 20 RPD;
- suporte a tools/function calling deve ser confirmado no catálogo local antes do teste.

Não testar Gemini 2.5 Pro nesta TASK.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 25 minutos
Corrective attempts: máximo 2
Model downloads allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Billing allowed: NÃO
Other new providers allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## Segurança e custo

Conta autorizada:

```text
edujr.android@gmail.com
```

Custo:

```text
R$ 0,00
US$ 0,00
```

Não:

- habilitar billing;
- criar nova chave;
- alterar provider keys;
- rotacionar a unified key nesta TASK;
- exibir qualquer segredo;
- enviar dados reais/confidenciais.

Usar somente dados sintéticos.

## PRE-CHECK

Confirmar:

- FreeLLMAPI healthy;
- Google provider healthy;
- Groq provider healthy;
- Hermes gateway/dashboard healthy;
- Ollama healthy e sem modelo carregado;
- `gemini-2.5-flash` presente no catálogo local;
- contexto >=64K;
- tools/function calling = true;
- Free Tier ainda ativo;
- billing ausente.

Se `gemini-2.5-flash` não estiver disponível/elegível, retornar BLOCKED sem substituir silenciosamente por outro modelo.

## 1. Teste direto FreeLLMAPI — qualidade

Fixar explicitamente:

```text
model = gemini-2.5-flash
```

Não usar `auto`.

### Teste A — português e instrução

```text
Explique em português, em no máximo 5 linhas, a diferença entre estratégia de marketing e execução de campanha.
```

Nota 0–5.

### Teste B — planejamento 1

```text
Uma empresa B2B recebe 100 leads por mês, converte 10 em reunião e fecha 2 clientes. Sem assumir dados que não foram fornecidos, identifique o que pode e o que não pode ser concluído. Em seguida proponha três hipóteses de teste em ordem de prioridade e diga qual métrica validaria cada hipótese.
```

Critério principal: NÃO declarar um gargalo como fato sem evidência.

Nota 0–5.

### Teste C — planejamento 2

```text
Uma empresa possui tráfego suficiente no site, mas não informou taxa de conversão, origem dos leads nem ticket médio. Crie um plano de diagnóstico em 4 etapas. Separe claramente fatos, hipóteses e dados que ainda precisam ser coletados.
```

Nota 0–5.

### Teste D — priorização

```text
Você tem três iniciativas possíveis: melhorar landing page, aumentar mídia paga ou revisar processo comercial. Não há dados suficientes para saber qual é o maior gargalo. Explique como priorizar sem inventar certeza e defina o primeiro experimento de baixo risco.
```

Nota 0–5.

Registrar:

- HTTP;
- latência;
- modelo servido;
- eventual fallback;
- notas;
- qualquer erro 429/5xx.

Não registrar respostas extensas; resumir evidência.

## 2. Gate de qualidade

Só avançar para Hermes se:

- português >= 3/5;
- planejamento B >= 3/5;
- planejamento C >= 3/5;
- priorização D >= 3/5;
- nenhuma resposta apresentar falsa certeza grave/repetida.

Se falhar, retornar:

```text
GEMINI_2_5_FLASH_REPROVADO_QUALIDADE
```

e não gastar quota com tool calling.

## 3. Configurar Hermes temporariamente

Configurar:

```text
provider = custom
base_url = http://freellmapi:3001/v1
model.default = gemini-2.5-flash
context_length = 131072
```

131072 é suficiente para superar o mínimo rígido de 64K do Hermes e reduz o orçamento lógico em relação a 1M.

Não alterar a unified key.

## 4. One-shot Hermes — superfície mínima

Usar somente:

```text
terminal,web,todo
```

Executar com override por sessão, sem tornar a seleção global permanente nesta etapa.

Prompt:

```text
Responda apenas: DIRETOR GEMINI 2.5 OK
```

PASS se resposta correta e sem 413/429/5xx.

## 5. Tool calling real — terminal

Prompt:

```text
Use a ferramenta de terminal para listar apenas os nomes dos arquivos Markdown do diretório atual e informe quantos encontrou.
```

PASS somente se houver:

1. tool_use real;
2. execução real;
3. tool_result;
4. resposta final baseada no resultado.

## 6. Tool calling real — segundo toolset

Validar uma segunda ferramenta da superfície `terminal,web,todo` com ação segura.

Preferir uma operação somente leitura em `web` ou `todo`. Se o toolset exigir escrita persistente, usar dados sintéticos e limpar imediatamente ao final.

Registrar uso real da ferramenta.

## 7. Planejamento via Hermes

Executar apenas UM teste final via Hermes:

```text
Uma empresa recebe 100 leads por mês, mas não informou origem dos leads, taxa de qualificação nem ticket médio. Estruture um diagnóstico em três passos sem inventar qual é o gargalo.
```

Critério:

- separa fatos/hipóteses;
- não inventa certeza;
- prioriza coleta de evidência;
- resposta utilizável.

Nota mínima: 3/5.

## 8. Estabilidade / quota

Durante os testes registrar:

- 429;
- 5xx;
- latência;
- número aproximado de chamadas;
- se houve fallback;
- se a resposta veio do modelo fixado.

Se ocorrer um 429 isolado:

1. respeitar Retry-After;
2. aguardar;
3. repetir UMA vez.

Se houver 429 recorrente, reprovar estabilidade para backend principal.

Se ocorrer 5xx isolado, repetir UMA vez.
Se 5xx persistir, reprovar estabilidade.

## 9. Seleção

### PASS

Aprovar `gemini-2.5-flash` como backend principal provisório do Diretor se:

- qualidade direta: todos critérios >=3/5;
- Hermes one-shot PASS;
- terminal tool calling PASS;
- segundo toolset PASS ou evidência suficiente de segunda ferramenta;
- planejamento via Hermes >=3/5;
- sem 413;
- sem 429 recorrente;
- sem 5xx persistente;
- custo zero;
- Free Tier confirmado.

Nesse caso, persistir:

```text
model.default = gemini-2.5-flash
model.context_length = 131072
provider = custom
base_url = http://freellmapi:3001/v1
```

Manter a política:

```text
Diretor tool surface = role-scoped/demand-scoped
Default inicial = terminal,web,todo
```

Não habilitar todos os toolsets globalmente.

### FAIL

Se qualquer critério decisivo falhar:

- remover `model.default`;
- restaurar Hermes para estado sem backend aprovado;
- manter Google/Groq configurados no FreeLLMAPI;
- registrar `GEMINI_2_5_FLASH_NAO_APROVADO`.

## 10. Restart e persistência

Somente se aprovado:

1. reiniciar container Hermes;
2. confirmar gateway/dashboard;
3. executar one-shot curto;
4. confirmar modelo/provider persistidos;
5. confirmar Ollama sem modelo carregado.

## POST-CHECK

Confirmar:

- custo zero;
- billing ausente;
- Google/Groq preservados;
- segredo não exposto;
- Ollama sem carga;
- Hermes estável;
- resultado de aprovação/reprovação explícito.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md`;
- `docs/ops/decisions/ADR-0001-freellmapi-evaluation.md`.

Se aprovado, registrar no ADR:

```text
FreeLLMAPI = backend router primário da V1
Google AI Studio / Gemini 2.5 Flash = backend principal provisório do Diretor
Groq = fallback/chat rápido
Ollama = auxiliar/local
Tool surface = role-scoped/demand-scoped
```

Fazer commit + push.

## Retorno

```text
TASK-0017: PASS | BLOCKED | FAILED
Commit: <sha>
Gemini 2.5 Flash direto: <notas/resumo>
Hermes one-shot: <status>
Terminal tool calling: <status>
Segundo toolset: <status>
Planejamento Hermes: <nota>
429/5xx: <resumo>
Modelo selecionado: <gemini-2.5-flash | nenhum>
Contexto: <131072 | nenhum>
Groq: <fallback preservado/status>
Ollama carregado: <sim/não>
Custo: R$ 0,00 / US$ 0,00
Bloqueio: <nenhum ou curto>
```