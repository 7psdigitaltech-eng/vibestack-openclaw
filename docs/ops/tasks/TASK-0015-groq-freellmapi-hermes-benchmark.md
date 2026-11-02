# TASK-0015 — Configurar Groq no FreeLLMAPI e validar Hermes remoto

Status: READY

## Objetivo

Validar de ponta a ponta a nova rota de inferência:

```text
Hermes -> FreeLLMAPI -> Groq -> modelo remoto
```

Usar somente Groq nesta primeira prova para reduzir variáveis.

Modelos candidatos preferenciais:

1. `openai/gpt-oss-20b`
2. `qwen/qwen3.8-27b`

Ambos devem ter contexto >=64K e suporte a tool calling na configuração/catálogo efetivo.

## Por que Groq primeiro

- contexto >=64K;
- tool calling suportado;
- baixa latência;
- não depende da RAM local para inferência;
- política de dados mais adequada à V1 interna que Google AI Studio Free;
- permite testar dois modelos com uma única credencial.

Google AI Studio permanece candidato a fallback posterior, não entra nesta TASK.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 30 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Browser/account action allowed: SIM, somente Groq e FreeLLMAPI
Provider key creation allowed: SIM, somente Groq
Provider key disclosure allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Paid upgrade/billing allowed: NÃO
Other providers allowed: NÃO

## Política obrigatória de conta e custo

Conta autorizada para qualquer login/criação de conta nesta TASK:

```text
Google account: edujr.android@gmail.com
```

Custo máximo autorizado:

```text
R$ 0,00
US$ 0,00
```

### Permitido

- usar "Continue with Google"/"Sign in with Google" exclusivamente com `edujr.android@gmail.com`;
- criar conta Groq gratuita vinculada exclusivamente a `edujr.android@gmail.com`;
- criar projeto/workspace gratuito, quando necessário para obter API key;
- criar API key Groq dentro do Free Tier;
- usar somente recursos explicitamente gratuitos;
- executar testes respeitando as quotas gratuitas.

### Proibido

- usar qualquer outra conta Google ou e-mail;
- criar conta com outro e-mail;
- adicionar cartão ou qualquer meio de pagamento;
- habilitar billing;
- fazer upgrade para Developer/Paid ou equivalente;
- comprar créditos;
- iniciar assinatura;
- ativar recurso que gere cobrança;
- aceitar trial que exija meio de pagamento ou possa converter automaticamente em cobrança sem nova aprovação humana;
- alterar orçamento/limites pagos;
- contornar paywall, quota ou rate limit por meio de cobrança;
- reutilizar credencial de outra conta/projeto.

### STOP obrigatório

Interromper e retornar `USER_ACTION_REQUIRED` se ocorrer qualquer um dos casos:

- a conta Google exibida não for exatamente `edujr.android@gmail.com`;
- for necessário informar senha manualmente;
- houver CAPTCHA;
- houver 2FA/MFA ou aprovação no celular;
- houver escolha/aceite de termos ou consentimento que exija decisão humana;
- houver solicitação de cartão, pagamento, billing, crédito ou upgrade;
- não for possível comprovar que a operação permanece no Free Tier;
- a plataforma tentar selecionar automaticamente outra conta.

O Work NÃO deve tentar resolver nenhum desses gates sozinho.

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## Segurança de credenciais

A chave Groq e a unified key do FreeLLMAPI:

- nunca devem aparecer em terminal/log;
- nunca devem ser gravadas em Markdown;
- nunca devem ser commitadas;
- nunca devem ser retornadas ao Chat.

Registrar somente:

```text
GROQ_KEY=SET
FREELLMAPI_API_KEY=SET
```

com tamanho, se útil.

## PRE-CHECK

Confirmar:

- FreeLLMAPI healthy;
- `http://127.0.0.1:3001` acessível no Windows;
- Hermes gateway ativo;
- dashboard Hermes ativo;
- Ollama ativo, mas sem modelo carregado;
- comunicação `openclaw-vibestack -> http://freellmapi:3001/api/ping` = PASS;
- RAM baseline registrada.

## 1. Obter/adicionar chave Groq

### Caminho preferencial

1. abrir o Groq Console oficial;
2. usar login Google exclusivamente com `edujr.android@gmail.com`;
3. se a conta Groq gratuita ainda não existir, criá-la somente quando isso puder ser concluído sem custo, sem cartão e sem gate humano;
4. confirmar visualmente que a sessão Groq resultante pertence a `edujr.android@gmail.com`;
5. criar uma chave dedicada com nome equivalente a:
   `7ps-marketing-freellmapi`;
6. inserir imediatamente a chave no FreeLLMAPI pela página local `http://127.0.0.1:3001` em Keys -> Groq;
7. não salvar a chave em arquivo intermediário;
8. não exibir a chave em logs.

### Gate humano

Se login, CAPTCHA, senha, 2FA/MFA, aceite explícito de termos ou qualquer confirmação humana impedir o Work:

- NÃO contornar;
- retornar `USER_ACTION_REQUIRED`;
- indicar somente a ação necessária e o site/tela em que o usuário deve intervir;
- manter como requisito a conta `edujr.android@gmail.com`;
- não pedir que senha, código 2FA ou API key seja enviado ao Chat.

Se o usuário concluir o login/criação e deixar a sessão autenticada, a mesma TASK pode ser retomada para criar/inserir a API key e continuar.

Se aparecer qualquer opção paga, cartão, Developer Tier, billing, compra de crédito ou trial com cobrança potencial:

- NÃO selecionar;
- retornar `USER_ACTION_REQUIRED` ou `BLOCKED`;
- registrar `COST_GUARD=TRIGGERED`.

Nunca habilitar billing ou plano pago.

## 2. Validar chave Groq no FreeLLMAPI

Confirmar via dashboard/API local, sem exibir segredo:

- provider `groq` presente;
- key enabled;
- health válido;
- modelos Groq routable > 0.

Registrar os modelos Groq efetivamente disponíveis que tenham:

- contexto >= 64.000;
- suporte a tools.

## 3. Selecionar candidatos efetivos

Procurar no catálogo local, nesta ordem:

1. `openai/gpt-oss-20b`
2. `qwen/qwen3.8-27b`

Para cada um confirmar:

- presente no catálogo local;
- routable pela chave Groq;
- contexto >=64K;
- tools = true.

Se um não existir, marcar `UNAVAILABLE_IN_LOCAL_CATALOG` e continuar com o outro.

Se nenhum existir/for elegível, retornar BLOCKED sem escolher modelo substituto silenciosamente.

## 4. Teste direto FreeLLMAPI

Para cada candidato elegível, executar via:

```text
http://freellmapi:3001/v1/chat/completions
```

usando a unified API key do FreeLLMAPI.

Se a unified key ainda não existir/estiver disponível, obtê-la pelo dashboard local e armazená-la somente no ambiente persistente Hermes, conforme etapa seguinte.

### Teste A — Português/instrução

```text
Explique em português, em no máximo 5 linhas, a diferença entre estratégia de marketing e execução de campanha.
```

### Teste B — Planejamento

```text
Uma empresa B2B recebe 100 leads por mês, converte 10 em reunião e fecha 2 clientes. Identifique o principal gargalo provável e proponha três hipóteses de teste em ordem de prioridade.
```

Registrar:

- sucesso;
- latência;
- modelo efetivamente servido;
- provider efetivo;
- eventual fallback;
- qualidade indicativa 0–5;
- erros/rate limits.

Não registrar respostas extensas.

## 5. Configurar Hermes -> FreeLLMAPI

Escolher temporariamente o melhor candidato do teste direto.

No ambiente persistente do Hermes:

### ~/.hermes/.env

Garantir:

```text
FREELLMAPI_API_KEY=<unified key>
```

Permissão restrita.

### config.yaml

Configurar de forma equivalente a:

```yaml
model:
  provider: custom
  api_mode: chat_completions
  base_url: http://freellmapi:3001/v1
  api_key: "${FREELLMAPI_API_KEY}"
  default: <modelo-candidato>
  context_length: <contexto-do-modelo, mínimo 64000>
  default_headers:
    User-Agent: hermes-agent
```

Preservar demais chaves do config.

Não executar `npx freellmapi setup-hermes` se isso exigir instalar Node/npm adicional no container; configurar somente com recursos já presentes.

## 6. Reiniciar apenas o necessário

Reiniciar o container Hermes para carregar a configuração persistida.

Não rebuildar.

Revalidar:

- Hermes gateway;
- dashboard Hermes;
- FreeLLMAPI;
- Ollama.

## 7. Teste one-shot Hermes

Executar:

```text
Responda em português apenas: HERMES REMOTO OK
```

PASS se:

- Hermes aceitar o contexto;
- resposta vier pelo FreeLLMAPI/Groq;
- nenhum modelo Ollama for carregado;
- container permanecer responsivo.

Registrar modelo/provider efetivamente servido.

## 8. Tool calling real

Executar tarefa somente leitura:

```text
Use a ferramenta de terminal para listar apenas os nomes dos arquivos Markdown do diretório atual e informe quantos encontrou.
```

PASS somente se:

1. Hermes emitir chamada de ferramenta;
2. ferramenta for executada de verdade;
3. resultado for devolvido ao modelo;
4. resposta final usar esse resultado.

Resposta inventada, comando em texto ou JSON cru sem execução = FAIL.

## 9. Comparar segundo modelo

Se ambos os candidatos estiverem disponíveis:

- alterar somente `model.default`;
- repetir one-shot;
- repetir tool calling;
- medir latência;
- avaliar português/instrução/planejamento.

Não executar ambos em paralelo.

## 10. Seleção

Escolher um modelo apenas se:

- contexto >=64K;
- one-shot PASS;
- tool calling real PASS;
- português >=3/5;
- instruções >=3/5;
- estabilidade >=3/5.

Critérios de desempate:

1. tool calling mais confiável;
2. qualidade de planejamento;
3. latência;
4. consumo de quota.

Se nenhum passar:

```text
GROQ NÃO APROVADO COMO BACKEND DO DIRETOR
```

e restaurar Hermes para estado sem modelo default aprovado.

## 11. Persistência/restart

Se houver vencedor:

1. deixar o vencedor em `model.default`;
2. reiniciar o container Hermes;
3. repetir one-shot curto;
4. confirmar persistência;
5. confirmar FreeLLMAPI/Groq continuam sendo usados;
6. confirmar Ollama sem modelo carregado.

## POST-CHECK

PASS quando:

- Groq key configurada localmente e saudável;
- FreeLLMAPI roteia para Groq;
- Hermes usa FreeLLMAPI;
- contexto >=64K aceito;
- tool calling real PASS;
- ao menos um modelo aprovado;
- restart/persistência PASS;
- nenhum segredo exposto;
- nenhuma cobrança/plano pago habilitado;
- conta Groq confirmada como vinculada a `edujr.android@gmail.com`;
- custo efetivo e autorizado = zero.

PARTIAL se a infraestrutura estiver pronta, mas apenas ação humana de login/API key impedir a conclusão.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md` com uma seção comparativa "Local vs FreeLLMAPI/Groq".

Se houver vencedor, atualizar ADR-0001 para registrar a evidência, mas manter a decisão final de arquitetura como "em avaliação" até confirmar estabilidade em uso real.

Fazer commit + push.

## Retorno

```text
TASK-0015: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
Groq: <configurado/status>
gpt-oss-20b: <status + nota>
qwen3.8-27b: <status + nota ou indisponível>
Modelo selecionado: <modelo | nenhum>
Contexto: <tokens>
Hermes one-shot: <status>
Tool calling: <status>
Ollama carregado: <sim/não>
RAM stack: <valor>
Conta utilizada: <edujr.android@gmail.com | não configurada>
Custo: <R$ 0,00 / US$ 0,00 | gate acionado>
Ação humana pendente: <nenhuma | descrição curta>
Bloqueio: <nenhum ou curto>
```
