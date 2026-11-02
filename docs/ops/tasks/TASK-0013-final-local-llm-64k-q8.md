# TASK-0013 — Teste decisivo local: llama3.2:3b em 64K com KV cache q8_0

Status: SUPERSEDED

## Objetivo

Executar um último teste controlado da hipótese de LLM local no hardware atual.

O Hermes Agent 0.21.1 exige, por código, contexto mínimo de 64.000 tokens. O teste anterior mostrou que 16K é operacional no Ollama, porém é recusado pelo Hermes.

O Ollama suporta quantização do KV cache. Nesta TASK será testado apenas:

- modelo: `llama3.2:3b`;
- contexto: 64.000;
- KV cache: `q8_0`;
- Flash Attention: habilitado;
- concorrência: 1;
- máximo de modelos carregados: 1.

Não testar qwen nesta TASK.
Não testar q4_0 nesta TASK.

## Por que llama3.2:3b

No benchmark direto em 16K:

- melhor nota indicativa: 33/85 vs 20/85;
- melhor português;
- menor pico de RAM;
- menor latência;
- operação direta mais estável.

Este teste decide apenas se ele consegue operar o Hermes com tool calling em 64K dentro do orçamento de memória disponível.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 20 minutos
Diagnostic hypotheses: máximo 1
Corrective attempts: máximo 1
Model downloads allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Parallel model execution allowed: NÃO
q4_0 allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## PRE-CHECK

Confirmar:

- container estável;
- Hermes gateway ativo;
- dashboard HTTP 200;
- Ollama HTTP 200;
- `llama3.2:3b` já instalado;
- nenhum modelo carregado;
- RAM do container em repouso;
- configuração atual do Hermes sem modelo default aprovado.

Não remover qwen; apenas não carregá-lo.

## 1. Preservar configuração anterior

Registrar apenas os valores NÃO secretos atuais de:

- `model.provider`;
- `model.base_url`;
- `model.context_length`;
- `model.ollama_num_ctx`, se existir;
- presença/ausência de:
  - `OLLAMA_FLASH_ATTENTION`;
  - `OLLAMA_KV_CACHE_TYPE`;
  - `OLLAMA_NUM_PARALLEL`;
  - `OLLAMA_MAX_LOADED_MODELS`.

Nunca exibir segredos.

## 2. Configurar Ollama para memória reduzida

No `.env` local, garantir:

```text
OLLAMA_FLASH_ATTENTION=true
OLLAMA_KV_CACHE_TYPE=q8_0
OLLAMA_NUM_PARALLEL=1
OLLAMA_MAX_LOADED_MODELS=1
```

Não alterar outras variáveis.

Recriar somente o container principal sem rebuild para que o processo Ollama herde essas variáveis:

```bash
docker compose up -d --force-recreate --no-deps --no-build openclaw-vibestack
```

Após estabilização, confirmar de forma sanitizada que o processo/container recebeu as quatro variáveis, sem imprimir qualquer segredo.

## 3. Configurar Hermes para 64K

Persistir:

```bash
hermes config set model.provider custom
hermes config set model.base_url http://127.0.0.1:11434/v1
hermes config set model.default llama3.2:3b
hermes config set model.context_length 64000
hermes config set model.ollama_num_ctx 64000
```

Confirmar configuração.

## 4. Teste direto de carga em Ollama

Executar UM prompt curto direto no Ollama com contexto solicitado de 64K.

Registrar:

- modelo carregou ou não;
- RAM antes/pico/depois;
- CPU;
- load_duration;
- total_duration;
- tokens/s;
- se houve erro de Flash Attention/KV cache;
- se o serviço permaneceu responsivo.

### Fail-safe

Interromper imediatamente se:

- RAM do container >= 90% do limite por período sustentado;
- Docker ficar sem resposta;
- Ollama parar de responder;
- container reiniciar;
- houver OOMKill;
- modelo ficar sem produzir resposta além do timeout operacional.

Se ocorrer, descarregar o modelo, restaurar responsividade e retornar BLOCKED.

## 5. Teste Hermes simples

Somente se o teste direto passar.

Executar via Hermes one-shot:

```text
Responda apenas: TESTE LOCAL OK
```

Critérios:

- Hermes aceita 64K;
- inferência retorna;
- nenhum erro de contexto;
- ambiente permanece responsivo.

## 6. Tool calling real

Somente se o teste simples passar.

Executar uma tarefa segura e somente leitura:

```text
Use a ferramenta de terminal para listar somente os nomes dos arquivos Markdown do diretório atual e informe quantos encontrou.
```

PASS somente se:

1. Hermes realmente invocar a ferramenta;
2. houver execução real;
3. o resultado da ferramenta for usado na resposta final.

FAIL se:

- modelo apenas disser o comando;
- imprimir JSON cru;
- inventar resultado;
- não executar ferramenta;
- travar durante a chamada.

## 7. Estabilidade

Se tool calling passar:

- descarregar o modelo;
- executar um restart do container;
- aguardar serviços;
- revalidar dashboard, Ollama e Hermes;
- carregar novamente `llama3.2:3b`;
- executar um one-shot simples;
- confirmar persistência da configuração 64K/q8_0.

## Decisão

### PASS — modelo local aprovado

Somente se:

- 64K aceita;
- RAM permanece abaixo do fail-safe;
- one-shot funciona;
- tool calling real = PASS;
- restart/persistência = PASS.

Nesse caso:

- manter `llama3.2:3b` como `model.default`;
- manter contexto 64K;
- manter q8_0;
- adicionar as quatro variáveis Ollama em `.env.example`, sem segredos, com comentário de que fazem parte da configuração validada deste hardware;
- registrar Fase 2 como PASS COM LIMITAÇÃO DE HARDWARE.

### BLOCKED/FAILED — hipótese local encerrada

Se qualquer requisito decisivo falhar:

- não testar q4_0;
- não testar qwen em 64K novamente;
- descarregar modelos;
- remover `model.default`;
- restaurar `model.context_length` para 16384 apenas como valor de benchmark, deixando explícito que NÃO é válido para Hermes;
- manter serviços estáveis;
- registrar:

```text
NENHUM MODELO LOCAL APROVADO NO HARDWARE ATUAL
```

Próximo passo recomendado: arquitetura híbrida/nuvem para o modelo principal do Diretor, mantendo Ollama opcional para tarefas auxiliares que não dependam do runtime agentic do Hermes.

## Fora de escopo

Não:

- q4_0;
- novo modelo;
- novo download;
- alteração do mínimo de 64K no código do Hermes;
- fork/patch do Hermes para reduzir o mínimo;
- Telegram;
- API 8642;
- rebuild;
- nova dependência.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md`.

Registrar claramente a decisão final sobre viabilidade local.

Fazer commit + push.

## Retorno

```text
TASK-0013: PASS | BLOCKED | FAILED
Commit: <sha>
llama3.2:3b 64K q8_0: <status>
Pico RAM: <valor>
Hermes one-shot: <status>
Tool calling: <PASS/FAIL/não executado>
Restart/persistência: <status>
Decisão Fase 2: <local aprovado | local não aprovado>
Bloqueio: <nenhum ou curto>
```


## Superseded — 2026-11-02

Esta TASK foi suspensa antes da execução.

Motivo: os benchmarks locais já comprovaram o conflito entre o mínimo rígido de 64K do Hermes e o orçamento de memória disponível. Antes de continuar otimizando KV cache local, foi aprovada a avaliação do FreeLLMAPI como router remoto de inferência, conforme ADR-0001 e TASK-0014.

Não executar esta TASK enquanto a avaliação FreeLLMAPI estiver ativa.
