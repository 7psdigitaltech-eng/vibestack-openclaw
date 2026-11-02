# TASK-0016 — Validar Google AI Studio Free via FreeLLMAPI com tool surface reduzida

Status: BLOCKED

## Objetivo

Validar uma segunda rota gratuita para o Hermes:

```text
Hermes -> FreeLLMAPI -> Google AI Studio -> Gemini
```

A TASK também deve corrigir a metodologia de tools do benchmark:

- NÃO enviar o conjunto completo de ferramentas Hermes;
- começar com `terminal` apenas;
- se passar, testar uma superfície mínima compatível com o futuro Diretor;
- manter Groq configurado, mas não usá-lo como primário durante este benchmark.

## Contexto

TASK-0015 demonstrou:

- Groq + FreeLLMAPI responde rápido em chat;
- `qwen/qwen3.8-27b` e `openai/gpt-oss-20b` responderam diretamente;
- Hermes com conjunto completo de tools recebeu HTTP 413;
- Hermes com tools reduzidas respondeu one-shot;
- tool calling real recebeu HTTP 429;
- Groq Free Tier possui limite de tokens insuficiente para comprovar um loop Hermes estável no benchmark atual.

Conclusão operacional provisória:

```text
Groq = manter como fallback/chat rápido
Groq = NÃO aprovado como backend primário do Diretor nesta fase
```

Google AI Studio será avaliado como segundo provider.

## Política obrigatória de conta e custo

Conta Google autorizada:

```text
edujr.android@gmail.com
```

Custo máximo autorizado:

```text
R$ 0,00
US$ 0,00
```

### Proibido

- vincular conta de faturamento;
- cadastrar cartão;
- habilitar Paid Tier;
- comprar créditos;
- aceitar upgrade pago;
- criar recurso com cobrança;
- usar outra conta Google;
- usar dados reais/confidenciais de clientes no benchmark.

### Privacidade

O Google informa que conteúdo do Gemini API Free Tier pode ser usado para melhorar seus produtos.

Portanto esta TASK deve usar APENAS prompts sintéticos/de teste.

Não enviar:

- dados de clientes;
- dados pessoais;
- credenciais;
- documentos privados;
- informações comerciais confidenciais.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 30 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Browser/account action allowed: SIM, somente Google AI Studio e FreeLLMAPI
Provider key creation allowed: SIM, somente Google AI Studio Free
Provider key disclosure allowed: NÃO
Billing allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Other new providers allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## PRE-CHECK

Confirmar:

- FreeLLMAPI healthy;
- Groq permanece healthy;
- Hermes gateway/dashboard ativos;
- Ollama ativo sem modelo carregado;
- configuração Hermes restaurada e sem model.default aprovado;
- conta/custo Groq não foram alterados.

Registrar baseline de RAM.

## 1. Criar/obter Google AI Studio API key

### Caminho preferencial

Se Work tiver acesso a uma sessão Google já autenticada:

1. abrir Google AI Studio oficial;
2. confirmar que a conta é exatamente `edujr.android@gmail.com`;
3. criar/usar um projeto Free sem billing;
4. criar API key dedicada para:
   `7ps-marketing-freellmapi`;
5. não vincular billing;
6. inserir a chave diretamente no FreeLLMAPI local em Keys -> Google;
7. não salvar a chave em arquivo intermediário;
8. não exibir a chave em logs ou documentação.

### USER_ACTION_REQUIRED

Se houver:

- login;
- senha;
- CAPTCHA;
- MFA/2FA;
- confirmação no celular;
- consentimento que exija decisão humana;
- seleção/criação de projeto que possa habilitar billing;
- qualquer tela de cobrança;

parar.

Não contornar autenticação.

Retornar somente a ação humana necessária.

Nunca pedir que API key, senha ou código MFA seja enviado ao Chat.

## 2. Confirmar Free Tier e limites ativos

No Google AI Studio, confirmar para o projeto usado:

- Tier = Free / sem billing;
- nenhuma conta de faturamento vinculada;
- limites ativos do projeto/modelos.

Registrar apenas os limites, sem IDs sensíveis.

### Critério para benchmark agentic

Preferir modelo cujo limite de tokens por minuto permita pelo menos:

```text
>= 20.000 tokens/minuto
```

Se os limites ativos forem inferiores, registrar e ainda permitir um teste isolado, mas não aprovar como backend principal sem evidência de loop de ferramenta estável.

## 3. Validar provider Google no FreeLLMAPI

Confirmar:

- provider `google` presente;
- key enabled/healthy;
- modelos routable > 0.

Listar somente modelos que tenham:

- Free Tier disponível;
- contexto >= 64.000;
- function/tool calling;
- output de texto;
- status não deprecated.

Prioridade sugerida, SOMENTE se presentes no catálogo local:

1. `gemini-3.1-flash-lite`;
2. `gemini-3.6-flash`;
3. `gemini-2.5-flash-lite`;
4. `gemini-2.5-flash`.

Não forçar modelo ausente.

Selecionar no máximo 2 candidatos para evitar consumo desnecessário de quota.

## 4. Teste direto FreeLLMAPI

Para cada candidato selecionado, executar somente:

### A — português/instrução

```text
Explique em português, em no máximo 5 linhas, a diferença entre estratégia de marketing e execução de campanha.
```

### B — planejamento

```text
Uma empresa B2B recebe 100 leads por mês, converte 10 em reunião e fecha 2 clientes. Identifique o principal gargalo provável e proponha três hipóteses de teste em ordem de prioridade.
```

Registrar:

- HTTP;
- latência;
- modelo servido;
- qualidade indicativa 0–5;
- rate-limit/fallback se houver.

Não executar prompts adicionais sem necessidade.

## 5. Configurar Hermes temporariamente

Usar a unified API key do FreeLLMAPI já armazenada localmente.

Configurar Hermes para:

```text
provider = custom
base_url = http://freellmapi:3001/v1
model.default = <candidato>
context_length = valor >= 64000 compatível com o modelo
```

Não expor a unified key.

## 6. Tool surface de prova — terminal somente

NÃO alterar permanentemente todas as tools.

Executar teste one-shot com override por sessão equivalente a:

```bash
hermes chat --oneshot --toolsets terminal ...
```

Usar no máximo 3 turns.

Primeiro prompt:

```text
Responda apenas: HERMES GOOGLE OK
```

Depois, após confirmar estabilidade:

```text
Use a ferramenta de terminal para listar somente os nomes dos arquivos Markdown do diretório atual e informe quantos encontrou.
```

PASS tool calling somente se houver:

1. tool_use real;
2. execução real;
3. tool_result;
4. resposta final baseada no resultado.

## 7. Tool surface mínima do futuro Diretor

Somente se terminal-only PASS.

Avaliar, sem tornar permanente ainda, uma superfície pequena com no máximo 5 toolsets relevantes.

Candidato inicial:

```text
terminal
web
todo
memory
delegation
```

Antes de executar inferência:

- listar quantas funções reais esses toolsets resolvem;
- executar `hermes prompt-size --json` ou diagnóstico equivalente;
- registrar tamanho do system prompt/tool schemas;
- se a superfície resolver mais de 10–15 funções ou crescer excessivamente, NÃO enviar;
- reduzir a seleção e documentar.

A meta arquitetural é tool routing por papel, não "todas as tools em todas as chamadas".

Executar apenas um one-shot simples e um tool call seguro se o payload for razoável.

## 8. Critérios de aprovação do provider/modelo

PASS como backend candidato do Diretor somente se:

- Tier Free confirmado;
- custo zero;
- contexto >=64K;
- one-shot Hermes PASS;
- terminal tool calling PASS;
- sem 413;
- sem 429 recorrente no fluxo mínimo;
- qualidade de português >=3/5;
- planejamento >=3/5;
- restart não perde configuração.

Se terminal-only passar, mas superfície mínima do Diretor ainda falhar por payload:

```text
PARTIAL — PROVIDER OK / TOOL ROUTING PENDENTE
```

Isso é diferente de reprovar o modelo.

## 9. Decisão sobre Groq

Não remover Groq.

Registrar:

```text
Groq:
- INSTALADO/CONFIGURADO = SIM
- custo = zero
- chat direto = PASS
- backend primário do Diretor = NÃO APROVADO
- candidato a fallback/chat rápido = SIM
```

## 10. Persistência

Se Google/modelo for aprovado:

1. deixar o modelo aprovado como default temporário da Fase 2;
2. reiniciar Hermes;
3. repetir one-shot curto;
4. confirmar FreeLLMAPI/Google;
5. confirmar Ollama sem modelo carregado.

Se não for aprovado:

- restaurar Hermes para estado sem default aprovado;
- manter providers configurados no FreeLLMAPI.

## POST-CHECK

Confirmar:

- custo zero;
- nenhum billing vinculado/ativado nesta TASK;
- Groq preservado;
- Google status documentado;
- Hermes estável;
- FreeLLMAPI estável;
- Ollama sem carga;
- segredos não expostos.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md`;
- ADR-0001 apenas se houver nova evidência relevante.

Registrar também a decisão arquitetural provisória:

```text
tools do Diretor serão role-scoped/demand-scoped;
não carregar a biblioteca completa de tools em toda chamada.
```

Fazer commit + push.

## Retorno

```text
TASK-0016: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
Google AI Studio: <status>
Tier/custo: <status>
Modelos testados: <resumo>
Modelo selecionado: <modelo | nenhum>
Contexto: <tokens>
Terminal tool calling: <PASS/FAIL/não executado>
Tool surface mínima: <PASS/PARTIAL/não executado>
Groq: <fallback preservado/status>
Ollama carregado: <sim/não>
RAM stack: <valor>
Ação humana: <nenhuma | curta>
Bloqueio: <nenhum ou curto>
```


## Estado confirmado manualmente — Google AI Studio

O usuário concluiu manualmente a criação e configuração da chave Google AI Studio no FreeLLMAPI.

Estado conhecido para retomada:

- conta autorizada: `edujr.android@gmail.com`;
- chave Google AI Studio: criada manualmente;
- provider Google: configurado no FreeLLMAPI;
- nenhuma chave deve ser exibida, copiada para documentação ou enviada ao Chat;
- não repetir login;
- não criar nova conta;
- não criar nova API key;
- não criar novo projeto se não for necessário;
- não habilitar billing;
- custo autorizado permanece `R$ 0,00 / US$ 0,00`.

Ao retomar esta TASK:

1. iniciar pela validação do provider Google no FreeLLMAPI;
2. confirmar key/provider healthy sem exibir segredo;
3. listar os modelos elegíveis com contexto >=64K e function/tool calling;
4. confirmar os limites ativos do Free Tier;
5. executar os testes diretos e depois Hermes com `terminal` apenas;
6. não repetir qualquer etapa de autenticação já concluída.

Se houver qualquer evidência de billing/pago, interromper e acionar o COST_GUARD.

## Resultado parcial — 2026-11-02

Status: PARTIAL / USER_ACTION_REQUIRED. Provider Google presente, habilitado e saudavel no FreeLLMAPI; Groq igualmente habilitado e saudavel. Catalogo local inclui gemini-3.1-flash-lite e gemini-3.6-flash com contexto reportado de 1.048.576, alem de gemini-2.5-flash-lite e gemini-2.5-flash; elegibilidade final, ferramentas, disponibilidade gratuita e limites ativos do projeto ainda precisam ser confirmados. O Chrome conectado mostrou uma conta Google diferente da autorizada; nenhuma alteracao de conta ou login foi efetuada. Nao foi possivel comprovar Tier Free e ausencia de billing no projeto associado a chave. Nenhuma inferencia remota, configuracao Hermes, troca de provider, novo projeto ou chave foi realizada nesta retomada; custo das acoes executadas zero. Baseline: Hermes 376,3 MiB e router 82,3 MiB; ambos ativos; gateway, dashboard, Ollama e ping interno OK; nenhum modelo Ollama carregado; Hermes sem default aprovado. Decisao provisoria: tools do Diretor serao role-scoped/demand-scoped, sem carregar biblioteca completa em toda chamada. Acao humana: confirmar na conta autorizada que o projeto usado pela chave esta no Tier Free, sem billing vinculado, e informar os limites ativos dos candidatos; nao enviar credenciais.


## Evidência manual de Free Tier e limites — 2026-11-02

O usuário forneceu evidência visual do Google AI Studio para o projeto associado à chave configurada no FreeLLMAPI.

### Billing / tier

Confirmado na interface:

- página "Gasto da API Gemini" marcada como `Nível gratuito`;
- projeto: `Default Gemini Project`;
- mensagem: o projeto não tem faturamento configurado;
- página "Faturamento da API Gemini": `Nenhuma conta de faturamento`;
- existe botão `Configurar faturamento`, mas ele NÃO foi acionado.

Portanto:

```text
GOOGLE_TIER=FREE
GOOGLE_BILLING_LINKED=NO
AUTHORIZED_COST=R$ 0,00 / US$ 0,00
COST_GUARD=PASS
```

O Work não deve bloquear novamente por falta de comprovação de Free Tier/billing enquanto este mesmo projeto/chave estiver em uso.

### Limites ativos exibidos pelo Google AI Studio

Candidatos prioritários da TASK:

| Modelo | RPM | TPM | RPD |
|---|---:|---:|---:|
| Gemini 3.1 Flash Lite | 15 | 250K | 500 |
| Gemini 3.6 Flash | 5 | 250K | 20 |
| Gemini 2.5 Flash Lite | 10 | 250K | 20 |
| Gemini 2.5 Flash | 5 | 250K | 20 |

Todos os quatro candidatos exibem TPM de 250K, acima do critério mínimo desta TASK de 20K TPM para benchmark agentic.

Para o benchmark, manter a prioridade:

1. `gemini-3.1-flash-lite` — melhor margem de RPM/RPD entre os candidatos prioritários;
2. `gemini-3.6-flash` — segundo candidato para qualidade, limitado a 5 RPM / 20 RPD.

Não testar mais de dois modelos nesta TASK salvo necessidade explícita de fallback por indisponibilidade.

Ao retomar:
- não repetir login;
- não repetir criação de chave;
- não repetir verificação de billing;
- iniciar pelo catálogo FreeLLMAPI e benchmark direto;
- usar apenas dados sintéticos;
- preservar custo zero.

## Resultado da retomada — 2026-11-02

Status: BLOCKED. Free Tier sem billing e limites confirmados previamente pelo usuario; apenas prompts e arquivos sinteticos foram enviados. Google e Groq permanecem configurados. Gemini 3.1 Flash Lite: testes diretos A/B HTTP 200, 1,60 s e 3,30 s; portugues 4/5, planejamento 2/5 pela afirmacao nao sustentada sobre o gargalo, embora as hipoteses sejam uteis. Gemini 3.6 Flash: A HTTP 200 em 10,06 s; B HTTP 502 em 6,04 s, sem conclusao de planejamento. Catalogo reporta 1.048.576 tokens e tools para Gemini 3.1, mas nao foi testado preenchimento integral da janela.

Hermes com terminal-only: one-shot PASS e ferramenta real PASS sobre tres arquivos sinteticos, com retorno baseado no resultado. Superficie minima terminal/web/todo: seis funcoes, 8.316 bytes em schemas e estimativa offline conservadora de 37.838 bytes de sistema; one-shot e ferramenta real PASS. A selecao inicial de cinco toolsets resolveu oito funcoes e 15.629 bytes de schemas, com estimativa de sistema 38.814 bytes; optou-se pela superficie menor. Os testes usaram --ignore-rules, sem carregar memoria/regras pessoais; nao validam fluxo com contexto privado ou biblioteca completa. Restart de Hermes e repeticao do one-shot PASS; Ollama sem modelo carregado.

Incidente: ao abrir detalhes do modelo, o painel do FreeLLMAPI exibiu a chave unificada em exemplo de comando na saida da ferramenta. Nao houve exposicao da chave Google ou Groq observada. A chave unificada deve ser considerada comprometida e regenerada pelo usuario; outras integracoes que a usam precisarao ser atualizadas. Nenhum segredo foi escrito nesta documentacao. Hermes restaurado para Ollama local, contexto configurado 16384 e sem modelo default aprovado; chave anterior ainda presente no ambiente persistente, mas nao usada pela configuracao restaurada. Gateway, dashboard, Ollama e router responderam; RAM amostrada 343,9 MiB Hermes e 85 MiB router. Custo Free: zero. Nenhum billing ativado. Groq: configurado, chat direto PASS, nao aprovado como primario, candidato a fallback. Decisao provisoria: tools do Diretor role-scoped/demand-scoped. Proximo passo: regenerar a chave unificada e substituir o valor no ambiente Hermes antes de retomar avaliacao.
