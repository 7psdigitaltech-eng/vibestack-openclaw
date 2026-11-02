# TASK-0011 — Configurar Ollama no Hermes e benchmark dos modelos locais

Status: BLOCKED

## Objetivo

Executar a Fase 2 do roadmap da 7Ps Marketing AI:

1. configurar o Hermes para usar o Ollama local;
2. baixar e testar, de forma sequencial:
   - `qwen3:4b`
   - `llama3.2:3b`
3. comparar desempenho, qualidade e capacidade agentic;
4. selecionar um modelo padrão somente se houver evidência suficiente.

Não avançar para Telegram nesta TASK.

## Contexto confirmado

Fase 1: PASS.

Ambiente:

- Docker Desktop / WSL2;
- ~9.7 GiB disponíveis ao Docker;
- 8 CPUs;
- Hermes como runtime;
- Ollama ativo em `127.0.0.1:11434` dentro do mesmo container;
- dashboard funcional;
- OpenClaw/Evolution/LM Studio desabilitados.

A documentação do Hermes recomenda endpoint local:

```text
provider: custom
base_url: http://127.0.0.1:11434/v1
```

O Hermes detecta o `num_ctx` do Ollama e permite limitar via `model.context_length`.

Para este benchmark, usar teto inicial de:

```text
model.context_length = 65536
```

Isso evita que modelos com janela anunciada muito maior tentem alocar contexto excessivo.

## Ambiente de execução

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 30 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Model downloads allowed: SIM, somente os dois modelos definidos nesta TASK
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Destructive actions allowed: NÃO
Parallel model benchmark allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## PRE-CHECK

Confirmar:

- container estável;
- Hermes gateway ativo;
- dashboard responde;
- Ollama responde em 11434;
- pelo menos ~6 GB livres no disco destinado a `data/ollama`;
- nenhum modelo grande carregado em memória;
- config atual do Hermes pode ser lida sem expor segredos.

Registrar baseline:

- RAM Docker usada;
- CPU em repouso;
- tamanho atual de `data/ollama`;
- modelos já existentes.

## 1. Configurar Hermes para Ollama

Dentro do container, configurar de forma persistente:

```bash
hermes config set model.provider custom
hermes config set model.base_url http://127.0.0.1:11434/v1
hermes config set model.context_length 65536
```

Não definir API key real; Ollama local não exige chave.

Validar que a configuração ficou em `config.yaml` sem expor segredos.

## 2. Baixar os modelos

Baixar sequencialmente:

```bash
ollama pull qwen3:4b
ollama pull llama3.2:3b
```

Não baixar outros modelos.

Registrar para cada um:

- tamanho em disco;
- sucesso/falha do pull;
- identificador/tag efetivo.

## 3. Benchmark direto no Ollama

Executar os mesmos testes para cada modelo, um por vez.

Antes de trocar de modelo:

- descarregar o anterior com `ollama stop <modelo>` quando aplicável;
- esperar memória estabilizar;
- não executar dois modelos simultaneamente.

### Teste A — Português e instruções

Prompt equivalente:

```text
Explique em português, em no máximo 5 linhas, a diferença entre estratégia de marketing e execução de campanha.
```

Avaliar:

- português;
- clareza;
- aderência ao limite;
- coerência.

### Teste B — Raciocínio/plano

Prompt equivalente:

```text
Uma empresa B2B recebe 100 leads por mês, converte 10 em reunião e fecha 2 clientes. Identifique o principal gargalo provável e proponha três hipóteses de teste em ordem de prioridade.
```

Avaliar:

- raciocínio;
- estrutura;
- prioridade;
- ausência de invenção.

### Teste C — Resumo

Fornecer um texto curto padronizado de aproximadamente 500–800 palavras e pedir resumo executivo em 5 bullets.

Avaliar:

- fidelidade;
- cobertura;
- concisão.

### Métricas Ollama

Para cada teste registrar, quando disponível no retorno da API:

- total_duration;
- load_duration;
- prompt_eval_count;
- prompt_eval_duration;
- eval_count;
- eval_duration;
- tokens/s aproximado;
- RAM antes/durante/depois;
- CPU durante execução.

## 4. Benchmark via Hermes

Para cada modelo, configurar:

```bash
hermes config set model.default <modelo>
```

Usar o modo one-shot do Hermes (`-z`) ou equivalente suportado pela versão instalada.

Executar os mesmos Testes A e B através do Hermes.

### Teste D — Tool calling real

Criar uma operação segura e somente leitura que obrigue uso de ferramenta.

Exemplo:

```text
Use a ferramenta de terminal para listar apenas os nomes dos arquivos .md existentes no diretório atual e depois diga quantos encontrou.
```

Critério:

- PASS somente se o Hermes realmente invocar a ferramenta e usar o resultado;
- resposta textual fingindo ter usado ferramenta = FAIL;
- JSON cru de tool call sem execução = FAIL.

Não permitir escrita, remoção ou alteração de arquivos neste teste.

## 5. Contexto e estabilidade

Para cada modelo via Hermes, registrar:

- se 65536 de contexto foi aceito;
- se houve OOM;
- se houve timeout;
- se houve truncamento;
- se houve erro de tool schema;
- se o container reiniciou;
- pico de RAM observado.

Se 65536 causar OOM em um modelo:

1. registrar o fato;
2. permitir uma única repetição com `model.context_length=32768`;
3. marcar explicitamente que o modelo não atende ao alvo de 64K naquele hardware.

Não reduzir abaixo de 32768 nesta TASK.

## 6. Pontuação comparativa

Criar tabela em Markdown com notas de 0 a 5:

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

Calcular pontuação ponderada.

### Regra de seleção

Um modelo só pode ser escolhido como padrão do Diretor se:

- Tool calling real = PASS;
- estabilidade >= 3/5;
- não causar restart/OOM recorrente;
- qualidade de português >= 3/5;
- seguir instruções >= 3/5.

Se nenhum atender, registrar:

```text
NENHUM MODELO LOCAL APROVADO
```

e não escolher por conveniência.

## 7. Modelo padrão

Se houver vencedor claro:

```bash
hermes config set model.default <modelo-vencedor>
```

Manter:

```text
provider = custom
base_url = http://127.0.0.1:11434/v1
context_length = valor aprovado
```

Executar um teste final one-shot simples após a seleção.

## POST-CHECK

Confirmar:

- Hermes continua ativo;
- dashboard continua ativo;
- Ollama continua ativo;
- config persiste;
- modelo padrão selecionado ou decisão explícita de não selecionar;
- nenhum modelo ficou duplicado/temporário;
- nenhuma arquitetura foi alterada.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Criar:

```text
docs/ops/benchmarks/ollama-models-v1.md
```

O relatório deve conter:

- hardware/limites;
- versões;
- modelos/tags;
- métricas;
- notas;
- falhas;
- resultado de tool calling;
- decisão final;
- limitações.

Não registrar prompts/respostas extensos; resumir evidências.

Fazer commit + push.

## Retorno

```text
TASK-0011: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
qwen3:4b: <aprovado/reprovado + nota>
llama3.2:3b: <aprovado/reprovado + nota>
Modelo selecionado: <modelo | nenhum>
Contexto aprovado: <tokens>
Tool calling: <resumo>
Bloqueio: <nenhum ou curto>
```

## Resultado — 2026-11-02

Status: BLOCKED. Provider custom, endpoint Ollama local e contexto solicitado 65536 configurados; ambos os modelos baixados. Primeiro teste direto qwen sem resposta sob pressao de memoria (pelo menos 8,791 GiB de 9,715 GiB); cliente interrompido, modelo descarregado e servicos recuperados. OOM e suporte operacional a 64K nao foram confirmados. Llama e tool calling nao foram testados; nenhum modelo selecionado ou aprovado por falta de evidencia. Detalhes e limitacoes em docs/ops/benchmarks/ollama-models-v1.md.
