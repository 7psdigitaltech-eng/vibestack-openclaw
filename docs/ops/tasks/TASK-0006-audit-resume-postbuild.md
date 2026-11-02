# TASK-0006 — Auditoria curta para retomar checkpoint pós-build

Status: READY

## Objetivo

Descobrir o estado real do ambiente local após as execuções posteriores ao reinício do PC e comparar com o checkpoint que queremos retomar:

```text
build concluído
imagem vibestack-openclaw:latest existente
volumes em D:
Hermes + Ollama selecionados
OpenClaw/Evolution desabilitados
antes do primeiro startup limpo
```

Esta TASK é somente de auditoria.

## Ambiente

Work / Local

Repo local:

```text
D:\Docker\7ps-marketing\repo
```

Antes da auditoria, sincronizar o clone com `origin/main` apenas por fast-forward seguro.

## PROIBIDO nesta TASK

Não:

- iniciar container;
- parar container;
- reiniciar container;
- recriar container;
- remover container;
- editar `.env`;
- editar dados em `D:\Docker\7ps-marketing\data`;
- fazer build/rebuild;
- alterar imagem;
- remover volume;
- alterar configuração Hermes/Ollama;
- exibir segredos.

A única alteração permitida é registrar o resultado desta auditoria neste arquivo e fazer commit/push.

## Auditoria

### 1. Git local

Registrar:

- branch atual;
- working tree limpa ou não;
- HEAD local;
- origin/main;
- se há commits locais não enviados.

Não corrigir nada.

### 2. Docker

Registrar:

- Docker Engine acessível;
- imagem `vibestack-openclaw:latest` existente ou não;
- containers vinculados ao projeto, inclusive parados;
- nome, status e imagem do container, se existir.

Não iniciar/parar nada.

### 3. Compose

Validar somente:

- serviços efetivos;
- mounts efetivos;
- portas publicadas/configuradas.

Confirmar se os mounts continuam apontando para:

```text
D:/Docker/7ps-marketing/data/...
```

### 4. .env sanitizado

Registrar somente valores não secretos:

- INSTALL_OPENCLAW
- INSTALL_HERMES
- INSTALL_OLLAMA
- INSTALL_LMSTUDIO
- INSTALL_EVOLUTION
- COMPOSE_PROFILES
- OPENCLAW_DATA_DIR
- OLLAMA_DATA_DIR
- HERMES_DATA_DIR
- HIGGSFIELD_DATA_DIR
- LMSTUDIO_DATA_DIR

Para segredos, registrar apenas:

```text
<VAR>=SET|EMPTY
length=<n>
```

Nunca registrar conteúdo.

### 5. Estado persistente Hermes

No diretório:

```text
D:\Docker\7ps-marketing\data\hermes
```

listar somente:

- nomes dos arquivos/diretórios de primeiro nível;
- tamanho;
- data de modificação.

Não abrir conteúdo de arquivos que possam conter segredo.

Indicar especificamente se existem:

- `.env`;
- `config.yaml`;
- banco/sessões/cache, se visíveis pelo nome.

### 6. Estado persistente Ollama

No diretório:

```text
D:\Docker\7ps-marketing\data\ollama
```

registrar apenas estrutura de primeiro nível e se há modelos armazenados, sem modificar nada.

## Resultado esperado

Classificar o ambiente em uma destas situações:

### A — praticamente no checkpoint pós-build
Pouca ou nenhuma limpeza necessária.

### B — container criado, mas dados/configuração ainda recuperáveis
Precisamos apenas remover/recriar estado operacional específico.

### C — estado persistente Hermes foi alterado
Precisamos decidir quais arquivos persistentes restaurar/limpar antes do startup limpo.

### D — estado mais amplo divergente
Exige plano de restauração específico.

## Registro

Atualizar apenas esta seção:

### Resultado

Status: PASS
Classificação: C
Git: branch main, árvore limpa antes do registro; HEAD e origin/main ee523b40a6b3d5f9b72de80db65ee9f6aec73901; nenhum commit local pendente antes deste registro.
Docker: Engine 29.7.2 acessível; imagem vibestack-openclaw:latest presente; container repo-openclaw-vibestack-1 em execução, usando essa imagem.
Compose: serviço efetivo openclaw-vibestack; mounts de openclaw, ollama, lmstudio, hermes e higgsfield em D:/Docker/7ps-marketing/data/; portas 1234, 8642, 9119, 11434 e 18789 publicadas em 127.0.0.1.
Env sanitizado: INSTALL_OPENCLAW=false, INSTALL_HERMES=true, INSTALL_OLLAMA=true, INSTALL_LMSTUDIO=false, INSTALL_EVOLUTION=false, COMPOSE_PROFILES vazio. Os cinco diretórios *_DATA_DIR apontam para suas respectivas pastas em D:/Docker/7ps-marketing/data/.
Segredos: HERMES_API_SERVER_KEY, OPENCLAW_GATEWAY_TOKEN, EVOLUTION_API_KEY, EVOLUTION_INSTANCE_TOKEN, POSTGRES_PASSWORD e GOG_KEYRING_PASSWORD: SET, length=64 cada.
Hermes persistente: .env e config.yaml presentes, além de diretórios de sessões/cache e bancos de estado; metadados abaixo. A TASK-0005 alterou o .env persistente.
Ollama persistente: blobs e manifests; nenhum arquivo de manifesto de modelo encontrado.
Diferenças em relação ao checkpoint pós-build: container criado e em execução; estado persistente Hermes criado/alterado; .env local com segredos rotacionados e API_SERVER_ENABLED=true. Não se pode presumir que o estado esteja antes do primeiro startup.
Ação mínima recomendada: decidir quais arquivos persistentes Hermes preservar ou restaurar antes de qualquer limpeza ou startup limpo; não executar limpeza nesta auditoria.
**hermes — primeiro nivel (nome | bytes | data local):**

- audio_cache | diretorio | 2026-11-02 14:37:07
- bin | diretorio | 2026-11-02 14:37:34
- cache | diretorio | 2026-11-02 14:51:19
- cron | diretorio | 2026-11-02 15:00:51
- hooks | diretorio | 2026-11-02 14:37:07
- image_cache | diretorio | 2026-11-02 14:37:07
- kanban | diretorio | 2026-11-02 14:38:09
- logs | diretorio | 2026-11-02 14:45:13
- memories | diretorio | 2026-11-02 14:37:07
- pairing | diretorio | 2026-11-02 14:37:07
- pending_messages | diretorio | 2026-11-02 14:38:09
- platforms | diretorio | 2026-11-02 14:37:30
- sandboxes | diretorio | 2026-11-02 14:45:14
- sessions | diretorio | 2026-11-02 14:37:07
- skills | diretorio | 2026-11-02 14:50:10
- state | diretorio | 2026-11-02 15:01:16
- .env | 149 | 2026-11-02 14:49:46
- .install_id.lock | 0 | 2026-11-02 14:38:05
- .mcp-discovery.lock | 0 | 2026-11-02 14:50:31
- auth.lock | 0 | 2026-11-02 14:38:08
- channel_directory.json | 67 | 2026-11-02 14:59:49
- config.yaml | 1488 | 2026-11-02 14:50:00
- gateway_state.json | 567 | 2026-11-02 14:50:45
- gateway-starts.log | 54 | 2026-11-02 14:50:10
- gateway.lock | 154 | 2026-11-02 14:50:15
- gateway.pid | 154 | 2026-11-02 14:50:15
- gateway.sock | 0 | 2026-11-02 14:50:15
- install_id | 33 | 2026-11-02 14:38:05
- kanban.db | 118784 | 2026-11-02 14:38:18
- kanban.db.dispatch.lock | 0 | 2026-11-02 14:38:18
- kanban.db.init.lock | 0 | 2026-11-02 14:38:14
- response_store.db | 20480 | 2026-11-02 14:38:02
- runs_idempotency.db | 16384 | 2026-11-02 14:38:03
- SOUL.md | 667 | 2026-11-02 14:37:07
- spawn-ledger.json | 1398 | 2026-11-02 15:01:09
- state.db | 413696 | 2026-11-02 14:50:53
- state.db.auto-maintenance.lock | 0 | 2026-11-02 14:37:29
- state.db.fts_rebuild.lock | 0 | 2026-11-02 14:37:29
- web-ui-build-stamp.json | 137 | 2026-11-02 14:39:06

**ollama — primeiro nivel (nome | bytes | data local):**

- blobs | diretorio | 2026-11-02 14:37:02
- manifests | diretorio | 2026-11-02 14:50:01

## Retorno ao Chat

Retornar somente:

```text
TASK-0006: PASS | BLOCKED
Classificação: A | B | C | D
Commit: <sha>
Diferenças: <resumo curto>
Ação mínima recomendada: <uma linha>
```
