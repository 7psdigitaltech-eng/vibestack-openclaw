# Benchmark Ollama — TASK-0011

Status: BLOCKED. A comparação não foi concluída e nenhum modelo foi selecionado nesta tarefa.

## Ambiente e configuração

- Docker Engine 29.7.2; 8 CPUs e 9,715 GiB de RAM disponíveis ao Docker; espaço livre inicial no disco de dados: 476.649.738.240 bytes.
- Baseline do container: CPU 0,36% e RAM 403,3 MiB; nenhum modelo carregado ou instalado no início.
- Ollama 0.34.0; Hermes Agent 0.21.1 (upstream 073c5787).
- Configuração persistente aplicada e confirmada: provider `custom`, base URL `http://127.0.0.1:11434/v1`, context_length 65536. Esse valor foi configurado, mas sua operação não foi validada.
- Downloads sequenciais concluídos: `qwen3:4b`, ID `359d7dd4bcda`, 2,5 GB, 180,6 s; `llama3.2:3b`, ID `a80c4f17acd5`, 2,0 GB, 130,6 s.

## Resultados e limitações

| Critério | qwen3:4b | llama3.2:3b |
|---|---|---|
| Teste direto A, contexto solicitado 65536 | Sem resposta útil; interrompido após pressão de memória e indisponibilidade temporária dos serviços | Não executado |
| Testes diretos B e C | Não executados | Não executados |
| Testes Hermes A e B | Não executados | Não executados |
| Tool calling real | Não testado | Não testado |
| Português, instruções, raciocínio, resumo e estabilidade | Sem evidência para atribuir nota | Sem evidência para atribuir nota |
| Latência, tokens/s e pontuação ponderada | Não mensuráveis; retorno da API não obtido | Não medidos |

Durante o primeiro teste direto, com contexto solicitado de 65536, temperatura 0,2 e limite de 350 tokens, o qwen foi solicitado com `think=false`. Foram observados CPU de 457,23% e pelo menos 8,791 GiB de RAM do container, contra limite de 9,715 GiB. O serviço não respondeu durante o teste e os comandos Docker também ficaram lentos. A medição pontual não estabelece o pico real. O cliente de teste foi interrompido e o modelo descarregado com `ollama stop qwen3:4b`. Após isso, nenhum modelo aparecia carregado; dashboard e Ollama voltaram a responder HTTP 200 internamente e o container estava running, sem restart registrado e sem OOMKilled registrado. A ausência de OOMKilled não prova ausência de pressão de memória no processo. Não se afirmou que 64K funciona, nem que houve OOM confirmado.

Não foi feita repetição em 32768: a exceção prevista dependia de OOM confirmado. Não foi iniciado outro teste sob a mesma pressão de memória. Não houve testes Hermes, schema de ferramentas, truncamento ou seleção de vencedor. A decisão é **NENHUM MODELO LOCAL APROVADO** por falta de evidência; isso não constitui reprovação definitiva de qualquer modelo.

Próximo passo: investigar o consumo e definir, em tarefa autorizada, um orçamento de contexto e timeout que permita comparar os modelos sem comprometer a responsividade do ambiente.

## TASK-0012 — comparação em 16.384 tokens, 2026-11-02

Status: BLOCKED. Nenhum modelo local foi aprovado ou selecionado: os testes Hermes A, B e D foram recusados antes da inferência porque Hermes Agent 0.21.1 exige no mínimo 64.000 tokens e a configuração operacional é 16.384. Não foi executada ferramenta. O teste de 32K não era elegível, pois exigia tool calling PASS; 64K foi proibido nesta TASK.

Baseline: container running, sem OOM ou reinícios, gateway ativo, dashboard e Ollama HTTP 200, nenhum modelo carregado, RAM 389 MiB/9,715 GiB, CPU 0,93%. `hermes prompt-size`: prompt de sistema 13.286 bytes, índice de skills 5.364 bytes, esquemas de 20 ferramentas 35.182 bytes; maiores grupos: file 5.404, delegation 4.023, skills 3.606, terminal 3.371 e memory 3.296 bytes. Não foram desativados tools, skills ou MCPs.

Os testes diretos usaram os mesmos prompts, contexto 16.384, temperatura 0,2 e máximo 350 tokens; para qwen foi enviado `think=false`. Tempos em segundos e RAM em bytes. CPU é média aproximada do container durante cada teste; memória é antes/pico observado/depois, não pico garantido do sistema. Os dois modelos foram executados sequencialmente.

| Modelo/teste | Total s | Load s | Prompt tokens / s | Saída tokens / s | Tokens/s saída | RAM antes / pico / depois | CPU média | Resultado |
|---|---:|---:|---:|---:|---:|---|---:|---|
| qwen A | 79,50 | 34,95 | 39 / 1,36 | 350 / 43,18 | 8,10 | 668286976 / 5730168832 / 5730136064 | 235% | truncado; raciocínio em inglês, sem resposta final adequada |
| qwen B | 45,83 | 0,03 | 67 / 2,39 | 350 / 43,36 | 8,07 | 5730136064 / 5792432128 / 5792411648 | 400% | truncado; não entregou plano final |
| qwen C | 106,22 | 0,03 | 842 / 42,46 | 350 / 63,67 | 5,50 | 5792411648 / 6116065280 / 5985665024 | 434% | truncado; não entregou cinco bullets |
| llama A | 42,23 | 29,96 | 58 / 1,87 | 111 / 10,40 | 10,68 | 813813760 / 4805808128 / 4805783552 | 134% | resposta clara em português; conclusão normal |
| llama B | 35,86 | 0,03 | 79 / 1,72 | 350 / 34,09 | 10,27 | 4805783552 / 4825432064 / 4825370624 | 399% | três hipóteses pouco sustentadas; truncado |
| llama C | 71,90 | 0,03 | 858 / 26,40 | 350 / 45,40 | 7,71 | 4825370624 / 5159731200 / 4992090112 | 444% | cinco bullets iniciados; último truncado, com atribuição imprecisa |

No qwen, os três testes terminaram por limite de geração (`length`); no llama, A terminou normalmente e B/C por limite. O pico observado foi 5,70 GiB para qwen e 4,81 GiB para llama. Nenhum teste direto atingiu o fail-safe de 90%; não houve OOMKill ou restart registrado. Não se mediu contexto real consumido de 16K: apenas foi solicitado esse limite.

### Pontuação indicativa (0–5)

Notas refletem somente as saídas observadas; estabilidade contempla a recusa do Hermes. Tool calling recebe zero porque a ferramenta não foi executada, sem concluir que o modelo seja incapaz fora deste runtime.

| Critério (peso) | qwen3:4b | llama3.2:3b |
|---|---:|---:|
| Português (1) | 0 | 4 |
| Seguir instruções (2) | 1 | 2 |
| Raciocínio/planejamento (2) | 1 | 1 |
| Resumo (1) | 0 | 3 |
| Tool calling real (4) | 0 | 0 |
| Estabilidade no fluxo completo (3) | 2 | 2 |
| Latência (2) | 2 | 3 |
| RAM/eficiência (2) | 3 | 4 |
| **Ponderada / 85** | **20** | **33** |

Decisão: **NENHUM MODELO LOCAL APROVADO**. A maior nota direta do llama não supera a exigência de tool calling real. Ambos os comandos Hermes retornaram erro de janela mínima em A, B e D; não houve invocação de ferramenta. O padrão temporário foi removido após os testes; context_length 16384 permanece configurado, porém não aprovado para o fluxo Hermes. Gateway, dashboard e Ollama ativos; ambos os modelos descarregados; nenhum restart ou OOMKill registrado. Próximo passo: avaliar alternativa híbrida/nuvem ou outro modelo/runtime compatível em tarefa autorizada, sem forçar 64K neste hardware.

## Local vs FreeLLMAPI/Groq — TASK-0015

Comparacao remota pendente. A infraestrutura FreeLLMAPI e Hermes passou no pre-check; o console Groq no Chrome disponivel apresentou tela de login, sem conta verificavel. Nenhuma chamada remota, chave ou teste de modelo foi realizado; nao ha pontuacao ou vencedor remoto. Custo das acoes executadas: zero. USER_ACTION_REQUIRED: deixar o console Groq autenticado na conta autorizada e retomar sob Free Tier comprovado.
