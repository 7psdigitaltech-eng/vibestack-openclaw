# Estado Atual — 7Ps Marketing AI

Última atualização: 2026-11-02

## Ambiente local

- Windows 10
- Docker Desktop 4.90.0
- Docker Engine 29.7.2
- Docker Compose v5.5.1
- Backend: WSL2
- CPUs disponíveis ao Docker: 8
- Memória disponível ao Docker: ~9.7 GiB
- WSL configurado com:
  - memory=10GB
  - processors=8
  - swap=6GB
  - autoMemoryReclaim=gradual

## Diretório do projeto

```text
D:\Docker\7ps-marketing\repo
```

## Persistência

```text
D:\Docker\7ps-marketing\data\
├── openclaw
├── ollama
├── hermes
├── higgsfield
├── lmstudio
├── knowledge
├── artifacts
└── backups
```

Mounts efetivos validados pelo Docker Compose:

```text
D:/Docker/7ps-marketing/data/openclaw   -> /root/.openclaw
D:/Docker/7ps-marketing/data/ollama     -> /var/lib/ollama
D:/Docker/7ps-marketing/data/lmstudio   -> /root/.lmstudio/models
D:/Docker/7ps-marketing/data/hermes     -> /root/.hermes
D:/Docker/7ps-marketing/data/higgsfield -> /root/.higgsfield
```

## Build

Imagem construída com sucesso:

```text
vibestack-openclaw:latest
```

Configuração:

```text
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
INSTALL_EVOLUTION=false
COMPOSE_PROFILES=
```

## Estado operacional

- imagem: construída;
- container principal: ainda não iniciado após o build;
- Hermes: instalado, ainda não validado em runtime;
- Ollama: instalado, ainda não validado em runtime;
- OpenClaw: não instalado na imagem atual;
- LM Studio: não instalado;
- Evolution/WhatsApp: desabilitado;
- Meta Ads: ferramenta instalada, sem credenciais;
- Google Ads: ferramenta instalada, sem credenciais;
- Higgsfield: CLI instalada, autenticação ainda não realizada;
- AtlasCloud: ferramenta instalada, sem API key;
- Telegram: ainda não configurado.

## Segurança

Durante a instalação inicial, segredos gerados automaticamente foram exibidos em saída compartilhada no Chat.

Regra: considerar esses valores comprometidos e rotacioná-los antes de operar a stack.

Nenhum valor secreto deve ser registrado neste arquivo.

## Bloqueio operacional atual

A primeira tentativa de executar a `TASK-0001` via Work foi bloqueada antes da execução técnica: o ambiente do Work utilizado não possuía acesso comprovado ao repositório local em `D:\Docker\7ps-marketing\repo` nem permissão para operar o Docker Desktop.

Nenhuma alteração local foi realizada por essa tentativa.

## Bloqueio operacional atual

- Work/Cloud: não possui acesso ao host local; não usar para TASKs `LOCAL_DESKTOP`.
- Work/Local: acesso local confirmado, porém a primeira reconciliação falhou porque o clone em `D:\Docker\7ps-marketing\repo` estava desatualizado e ainda não continha as TASKs/documentos operacionais mais recentes.
- O protocolo anterior exigia ler a TASK local antes de permitir sincronização, criando um deadlock de bootstrap.

## Próximo checkpoint

Executar `TASK-0001B` em Work/Local para sincronizar o clone por fast-forward seguro e, na mesma execução, reconciliar o resultado local anterior sem repetir ações Docker.

## Verificacao TASK-0002 — 2026-11-02

Status: BLOCKED. Docker Engine acessivel; Compose nao identificou containers do projeto, inclusive parados. Ollama, API Hermes e dashboard nao responderam nas portas locais previstas. As quatro opcoes solicitadas coincidem com os valores esperados no arquivo local. Nenhum container foi criado ou iniciado, e a rotacao anterior continua sem evidencia suficiente. Decidir como estabelecer o runtime e comprovar a rotacao antes da operacao.

## Verificacao TASK-0003 — 2026-11-02

Status: BLOCKED. Os seis segredos identificados foram rotacionados e validados por presenca e tamanho, sem exposicao. A stack foi iniciada com a imagem existente, sem rebuild: container em execucao, sem reinicios observados, e processo Hermes gateway presente. OpenClaw e Evolution seguem desabilitados na configuracao local. As portas 11434, 8642 e 9119 nao retornaram HTTP no host; no container 11434 e 9119 estavam abertas e 8642 fechada. Runtime nao validado. Proximo passo: diagnosticar a exposicao dos servicos e inicializacao da API.

## Verificacao TASK-0004 — 2026-11-02

Status: BLOCKED. API_SERVER_ENABLED habilitado no ambiente e no exemplo; container recriado sem rebuild, em execucao sem reinicios observados. Listeners internos: 11434 e 9119 abertos, 8642 fechado. As tres portas estao mapeadas em 127.0.0.1 pelo Docker, mas os testes HTTP no host Windows retornaram 000. Nenhum segredo foi registrado. Proximo passo: investigar inicializacao da API e acesso host em tarefa autorizada.

## Verificacao TASK-0005 — 2026-11-02

Status: BLOCKED. Ambiente persistente Hermes alinhado com chave existente e permissao restrita; servico reiniciado sem rebuild. Container em execucao e gateway presente; 11434 e 9119 abertos internamente, 8642 fechado e health inacessivel. As tres portas estao publicadas em 127.0.0.1. A busca sanitizada nos logs nao identificou erro relevante. Proximo passo: diagnostico direcionado da inicializacao da API.

## Restauracao TASK-0007 — 2026-11-02

Checkpoint pos-build restaurado: imagem vibestack-openclaw:latest preservada; nenhum container do projeto; diretorios persistentes Hermes e Ollama vazios. Estado anterior preservado em D:/Docker/7ps-marketing/data/backups/reset-postbuild-2026-11-02/. Segredos rotacionados preservados; flags e mounts validados; linha experimental API_SERVER_ENABLED=true removida. Nenhum volume removido ou rebuild executado. Proximo passo: primeiro startup controlado.

## Startup TASK-0008 — 2026-11-02

Status: BLOCKED; Fase 1 pendente. Stack iniciada a partir do checkpoint restaurado, sem rebuild. Container em execucao e gateway Hermes ativo; Ollama responde HTTP interno 200 na 11434. Bridge do dashboard 9119 aberta, mas HTTP interno encerra sem resposta e servidor interno 9120 fechado. Portas 9119 e 11434 publicadas em loopback. Restart e persistencia nao validados porque o dashboard nao estabilizou; acesso host nao verificado. Proximo passo: diagnosticar inicializacao do dashboard.

## Verificacao TASK-0009 — 2026-11-02

Status: BLOCKED; Fase 1 pendente. O entrypoint foi ajustado para --skip-build, mas nao implantado. Rebuild com cache foi interrompido ao iniciar download de uma nova imagem-base, antes das etapas de instalacao. Imagem anterior e container em execucao preservados; restart e persistencia nao validados. Proximo passo: definir reproducao da base/cache ou escopo autorizado de rebuild.

## Resultado TASK-0010 — 2026-11-02

Fase 1: PASS. Overlay da imagem local aplicada sem download ou instalacao; imagem anterior preservada pela tag pre-dashboard-fix. Gateway ativo, dashboard e Ollama retornaram HTTP interno 200, e dashboard respondeu HTTP 200 no host Windows. Mapeamentos de 9119 e 11434 em loopback. Container e servicos revalidados apos restart; config.yaml e install_id persistentes preservados. Proximo checkpoint: configuracao e benchmark de modelo local em tarefa propria.

## Benchmark TASK-0011 — 2026-11-02

Status: BLOCKED. Hermes configurado com provider custom, Ollama local e contexto solicitado 65536; modelos qwen3:4b e llama3.2:3b baixados. Primeiro teste qwen interrompido apos pressao de memoria e ausencia de resposta; modelo descarregado, container em execucao e endpoints locais respondendo. Nenhum modelo aprovado ou selecionado por falta de comparacao e testes de ferramenta. Proximo passo: definir teste seguro de contexto e recursos em tarefa autorizada.

## Benchmark TASK-0012 — 2026-11-02

Status: BLOCKED; Fase 2 não concluída. Testes diretos dos dois modelos em 16K concluídos, mas Hermes exige pelo menos 64K e recusou os testes one-shot e de ferramenta. Nenhum modelo local aprovado ou selecionado; configuração temporária de modelo removida. Contexto 16384 permanece configurado sem aprovação para Hermes. Serviços ativos, modelos descarregados. Avaliar alternativa híbrida/nuvem ou outro modelo/runtime compatível em tarefa própria.

## FreeLLMAPI TASK-0014 — 2026-11-02

Status: PASS. FreeLLMAPI v0.16.16 instalado e saudavel; painel e /api/ping respondem HTTP 200 em 127.0.0.1:3001; container Hermes alcanca freellmapi:3001/api/ping por HTTP 200. Reinicio do router validado, com banco persistente preservado. RAM idle observada 65,15 MiB; CPU 0,01%; soma da stack observada 491,75 MiB. Dados em D:/Docker/7ps-marketing/data/freellmapi. INSTALADO = SIM; CONFIGURADO COM PROVIDER = NÃO; AUTORIZADO COMO BACKEND DO HERMES = NÃO. Configuracao de modelo/provider Hermes nao foi alterada.

## TASK-0015 — 2026-11-02

Status: PARTIAL / USER_ACTION_REQUIRED. Infraestrutura validada; o Chrome disponivel apresentou login Groq e a conta nao foi confirmada. Nenhuma API key ou provider Groq foi configurado; Hermes permanece sem alteracao de modelo/provider. Custo das acoes executadas: zero. Aguardando console Groq autenticado na conta autorizada e confirmacao do Free Tier para retomar.

## Retomada TASK-0015 — 2026-11-02

Status: PARTIAL / USER_ACTION_REQUIRED; COST_GUARD=TRIGGERED. Provider Groq configurado manualmente, chave habilitada e saudavel no FreeLLMAPI. Nao foi possivel confirmar Free Tier no console Groq acessivel; nenhum benchmark remoto ou alteracao de provider Hermes foi executado. Custo desta retomada zero. Aguardando comprovacao do plano gratuito na conta autorizada.

## TASK-0015 benchmark remoto — 2026-11-02

Status: BLOCKED. Groq Free e router responderam aos testes diretos de qwen3.8-27b e gpt-oss-20b; Hermes com ferramentas completas recebeu 413 e os testes de ferramenta restritos receberam 429. Nenhum tool calling real comprovado, portanto nenhum modelo aprovado ou selecionado. Hermes restaurado para endpoint Ollama local, contexto 16384 e sem modelo default; chave unificada mantida no ambiente persistente restrito. Servicos ativos e Ollama sem modelo carregado. Nenhuma mudanca de billing; custo Free zero. Proximo passo: avaliar limites de payload/quota em tarefa propria.

## TASK-0016 — 2026-11-02

Status: PARTIAL / USER_ACTION_REQUIRED. Google e Groq habilitados e saudaveis no FreeLLMAPI; catalogo contem candidatos Gemini. Benchmark nao iniciado porque Tier Free, ausencia de billing e limites ativos do projeto Google autorizado nao foram comprovados. Nenhuma inferencia ou mudanca de configuracao Hermes; custo das acoes executadas zero. Tools do Diretor: role-scoped/demand-scoped, sem biblioteca completa por chamada. Aguardar comprovacao do projeto autorizado antes de testar.

## TASK-0016 benchmark Google — 2026-11-02

Status: BLOCKED. Gemini 3.1 Flash Lite respondeu a testes sinteticos e executou terminal real em superficies restritas; planejamento apresentou conclusao sem base suficiente. Gemini 3.6 retornou 502 no planejamento. A chave unificada FreeLLMAPI foi exibida em saida da ferramenta pelo exemplo do painel e deve ser considerada comprometida; regeneracao e atualizacao das integracoes pendentes. Hermes restaurado para Ollama local, contexto 16384 e sem default aprovado; router e servicos ativos. Nenhuma chave de provider foi observada exposta. Custo Free zero. Tools do Diretor permanecem em avaliacao role-scoped/demand-scoped.


## Decisão de segurança — unified key FreeLLMAPI — 2026-11-02

O usuário optou por manter temporariamente a unified API key atual do FreeLLMAPI, apesar da exposição observada durante a TASK-0016.

Estado:

```text
Rotação: ADIADA POR DECISÃO DO USUÁRIO
Uso permitido: avaliação interna atual
Uso em produção/exposição externa: NÃO AUTORIZADO sem rotação prévia
Provider keys Google/Groq: não observadas expostas
```

A exposição permanece registrada como incidente. Novos testes internos não devem ser bloqueados exclusivamente pela ausência de rotação, mas o valor da chave nunca deve ser exibido ou registrado.
