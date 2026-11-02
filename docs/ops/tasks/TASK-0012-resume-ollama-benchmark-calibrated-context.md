# TASK-0012 — Retomar benchmark Ollama com contexto calibrado

Status: BLOCKED

## Objetivo

Concluir a comparação entre `qwen3:4b` e `llama3.2:3b` sem repetir a saturação causada pelo contexto de 65.536 tokens.

Esta TASK deve produzir evidência suficiente para uma destas decisões:

1. selecionar um modelo local para o Diretor;
2. aprovar um modelo com limitação explícita de contexto;
3. concluir que nenhum dos dois é viável neste hardware.

## Decisão de contexto

O roadmap do projeto exige estabilidade com contexto maior, mas NÃO exige 64K como critério de aceite.

Resultado anterior:

- 65.536 tokens: não validado;
- qwen3:4b consumiu pelo menos 8,791 GiB dos 9,715 GiB disponíveis;
- ambiente ficou pouco responsivo;
- não houve OOMKill confirmado;
- ambos os modelos estão baixados.

Portanto:

- NÃO testar 65.536 novamente;
- iniciar em 16.384;
- testar 32.768 somente se houver folga real;
- cada modelo deve ser isolado para que uma falha não impeça o teste do outro.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 30 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Model downloads allowed: NÃO
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Parallel model benchmark allowed: NÃO
64K context test allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## PRE-CHECK

Confirmar:

- container running e estável;
- Hermes gateway ativo;
- dashboard HTTP 200;
- Ollama HTTP 200;
- `qwen3:4b` presente;
- `llama3.2:3b` presente;
- nenhum modelo carregado em `ollama ps`;
- RAM do container em repouso registrada.

## 1. Medir overhead fixo do Hermes

Executar offline:

```bash
hermes prompt-size
```

Registrar no relatório:

- tamanho total informado;
- principais componentes responsáveis pelo prompt fixo;
- tool schemas/skills que mais contribuem, se a saída detalhar.

Não desabilitar tools, skills ou MCPs nesta TASK.

O objetivo é medir o estado real atual, não otimizar ainda.

## 2. Ajustar contexto operacional para 16K

Persistir:

```bash
hermes config set model.context_length 16384
```

Manter:

```text
provider = custom
base_url = http://127.0.0.1:11434/v1
```

Validar configuração sem expor segredos.

## 3. Benchmark qwen3:4b

Garantir que nenhum outro modelo esteja carregado.

### 3.1 Direto no Ollama

Executar Testes A, B e C definidos na TASK-0011 com contexto 16.384.

Registrar:

- resposta obtida ou não;
- total_duration;
- load_duration;
- prompt_eval_count;
- prompt_eval_duration;
- eval_count;
- eval_duration;
- tokens/s;
- RAM máxima observada;
- CPU aproximada;
- estabilidade.

### 3.2 Via Hermes

Configurar:

```bash
hermes config set model.default qwen3:4b
```

Executar via one-shot:

- Teste A;
- Teste B;
- Teste D de tool calling real e somente leitura.

Tool calling só recebe PASS se a ferramenta for efetivamente executada.

### 3.3 Fail-safe

Se ocorrer qualquer uma destas condições:

- RAM do container >= 90% do limite por período sustentado;
- Docker/serviços ficam sem resposta;
- ausência de resposta do modelo além do timeout operacional;
- restart/OOM;

então:

1. interromper o teste desse modelo;
2. descarregar com `ollama stop qwen3:4b`;
3. esperar o ambiente estabilizar;
4. registrar qwen como inviável no contexto testado;
5. CONTINUAR para o llama.

A falha do qwen não bloqueia o teste do llama.

## 4. Benchmark llama3.2:3b

Descarregar qwen e aguardar estabilização.

Configurar:

```bash
hermes config set model.default llama3.2:3b
```

Repetir exatamente:

- Teste A direto;
- Teste B direto;
- Teste C direto;
- Teste A via Hermes;
- Teste B via Hermes;
- Teste D tool calling real.

Aplicar o mesmo fail-safe.

A falha do llama não apaga os resultados do qwen.

## 5. Teste opcional em 32K

Somente executar para o MELHOR modelo em 16K, e apenas se TODAS forem verdadeiras:

- testes 16K concluídos;
- tool calling = PASS;
- pico de RAM <= 75% do limite Docker;
- container permaneceu responsivo;
- resposta em tempo utilizável.

Se elegível:

```bash
hermes config set model.context_length 32768
```

Executar somente:

- Teste A via Hermes;
- Teste D tool calling.

Se RAM >= 90% ou o ambiente perder responsividade, interromper imediatamente e voltar para 16K.

32K é bônus de estabilidade, não requisito para aprovação.

## 6. Pontuação

Usar a mesma tabela da TASK-0011:

| Critério | Peso |
|---|---:|
| Português | 1 |
| Seguir instruções | 2 |
| Raciocínio/planejamento | 2 |
| Resumo | 1 |
| Tool calling real | 4 |
| Estabilidade | 3 |
| Latência | 2 |
| RAM/eficiência | 2 |

### Critério mínimo de aprovação

Um modelo pode ser selecionado se:

- tool calling real = PASS;
- português >= 3/5;
- seguir instruções >= 3/5;
- estabilidade >= 3/5;
- não causar restart/OOM recorrente;
- operar de forma utilizável em pelo menos 16K.

Se o modelo só for aprovado em 16K, registrar explicitamente:

```text
APROVADO COM LIMITAÇÃO DE CONTEXTO: 16K
```

Isso NÃO impede a Fase 2 de passar se o Diretor for funcional nesse limite.

## 7. Decisão final

Se houver vencedor:

- deixar o vencedor em `model.default`;
- deixar `model.context_length` no maior contexto validado com segurança;
- executar um teste final simples.

Se nenhum passar:

```text
NENHUM MODELO LOCAL APROVADO
```

Não avançar para Telegram e recomendar avaliação híbrida/nuvem ou novo modelo local.

## POST-CHECK

Confirmar:

- Hermes ativo;
- dashboard ativo;
- Ollama ativo;
- modelo selecionado ou decisão explícita de nenhum;
- contexto persistido no valor aprovado;
- nenhum modelo ficou simultaneamente carregado;
- container responsivo.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;
- `docs/ops/benchmarks/ollama-models-v1.md`.

Registrar:

- resultado do `hermes prompt-size`;
- comparação 16K;
- eventual teste 32K;
- métricas;
- tool calling;
- decisão final;
- limitação de contexto.

Fazer commit + push.

## Retorno

```text
TASK-0012: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
Prompt fixo Hermes: <resumo>
qwen3:4b: <status + nota + contexto>
llama3.2:3b: <status + nota + contexto>
Modelo selecionado: <modelo | nenhum>
Contexto aprovado: <tokens | nenhum>
Tool calling: <resumo>
Bloqueio: <nenhum ou curto>
```

## Resultado — 2026-11-02

Status: BLOCKED. Testes diretos em 16K concluídos para ambos, com truncamentos; qwen 20/85 e llama 33/85 em pontuação indicativa. Hermes recusou ambos os modelos em 16K por exigir mínimo de 64K, impedindo A, B e tool calling real. 32K não elegível; 64K não testado. Nenhum modelo selecionado ou aprovado; seleção temporária removida. Contexto 16384 configurado, não aprovado para Hermes. Gateway, dashboard e Ollama ativos; modelos descarregados; sem restart/OOMKill. Métricas e limitações em docs/ops/benchmarks/ollama-models-v1.md. Próximo passo: avaliar alternativa híbrida/nuvem ou outro modelo/runtime compatível em tarefa autorizada.
