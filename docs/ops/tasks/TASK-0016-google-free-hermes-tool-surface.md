# TASK-0016 — Validar Google AI Studio Free via FreeLLMAPI com tool surface reduzida

Status: READY

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
