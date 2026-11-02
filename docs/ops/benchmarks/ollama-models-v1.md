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
