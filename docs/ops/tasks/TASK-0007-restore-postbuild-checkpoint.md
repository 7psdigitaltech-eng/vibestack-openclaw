# TASK-0007 — Restaurar checkpoint pós-build

Status: READY

## Objetivo

Retornar o ambiente local ao checkpoint imediatamente posterior ao build inicial, desconsiderando as tentativas operacionais realizadas depois do reinício do PC.

Estado alvo:

```text
imagem vibestack-openclaw:latest preservada
nenhum container do projeto em execução/criado
Hermes data vazio
Ollama data sem estado criado pelas tentativas
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
INSTALL_EVOLUTION=false
COMPOSE_PROFILES=
mounts continuam em D:/Docker/7ps-marketing/data/...
segredos continuam rotacionados e NÃO são exibidos
```

## Ambiente

Work / Local

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro antes de executar.

## Preservar

- imagem `vibestack-openclaw:latest`;
- repositório local;
- diretórios raiz em `D:\Docker\7ps-marketing\data`;
- segredos já rotacionados no `.env`;
- flags INSTALL_* e DATA_DIR já validadas;
- qualquer conteúdo não relacionado em `knowledge`, `artifacts` e `backups`.

## Remover/reverter

### 1. Container

Parar e remover somente os containers deste compose:

```bash
docker compose down
```

NÃO usar `-v`.
NÃO remover imagem.

### 2. Backup curto do estado criado após o checkpoint

Antes de limpar, mover o conteúdo atual de:

```text
D:\Docker\7ps-marketing\data\hermes
D:\Docker\7ps-marketing\data\ollama
```

para um backup datado dentro de:

```text
D:\Docker\7ps-marketing\data\backups\reset-postbuild-2026-11-02\
```

Subpastas:

```text
hermes-before-reset
ollama-before-reset
```

Não versionar esse backup no Git.

### 3. Recriar diretórios vazios

Garantir que existam e estejam vazios:

```text
D:\Docker\7ps-marketing\data\hermes
D:\Docker\7ps-marketing\data\ollama
```

### 4. .env local

Preservar todos os segredos atuais já rotacionados.

Remover somente a linha experimental:

```text
API_SERVER_ENABLED=true
```

se estiver presente.

Não imprimir valores secretos.

Confirmar apenas as flags:

```text
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
INSTALL_EVOLUTION=false
COMPOSE_PROFILES=
```

e DATA_DIRs apontando para `D:/Docker/7ps-marketing/data/...`.

### 5. .env.example versionado

Remover somente a linha:

```text
API_SERVER_ENABLED=true
```

adicionada durante as tentativas posteriores.

Não alterar outros exemplos.

## Validação

Confirmar:

- `docker compose ps -a` sem container do serviço principal;
- imagem `vibestack-openclaw:latest` ainda existe;
- `data/hermes` vazio;
- `data/ollama` vazio;
- backup criado;
- flags e mounts corretos;
- nenhum segredo exibido;
- nenhum volume Docker removido;
- nenhum build executado.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`;

registrando que o ambiente voltou ao checkpoint pós-build e que o próximo passo é o primeiro startup controlado.

Fazer commit + push das alterações versionadas.

## Limites

- até 10 minutos;
- sem rebuild;
- sem instalação;
- sem iniciar container depois da limpeza;
- sem alterar arquitetura.

## Retorno

```text
TASK-0007: PASS | BLOCKED | FAILED
Commit: <sha>
Container: <ausente/status>
Imagem: <preservada/status>
Hermes data: <vazio/status>
Ollama data: <vazio/status>
Backup: <status>
Env: <checkpoint/status>
Bloqueio: <nenhum ou curto>
```
