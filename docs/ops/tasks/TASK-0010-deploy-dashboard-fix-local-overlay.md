# TASK-0010 — Implantar correção do dashboard sobre a imagem local existente

Status: READY

## Objetivo

Implantar a correção já versionada no `entrypoint.sh` sem rebuild completo da stack e sem baixar imagem-base ou reinstalar dependências.

A correção fonte já está em `main`:

```text
hermes dashboard ... --no-open --skip-build
```

A imagem atualmente em execução ainda contém o entrypoint anterior.

## Estratégia aprovada

Criar uma camada Docker mínima usando exclusivamente a imagem local já existente como base.

```text
vibestack-openclaw:latest atual
        ↓ tag local de backup
vibestack-openclaw:pre-dashboard-fix
        ↓ COPY entrypoint.sh corrigido
vibestack-openclaw:latest novo
```

Nenhuma dependência será instalada.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: S
Hard budget: 10 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Full rebuild allowed: NÃO
Overlay build from local image: SIM
Network download allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: NÃO
Destructive actions allowed: NÃO
Fallback source/environment allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## PRE-CHECK

Confirmar:

- `entrypoint.sh` local contém `--skip-build` no comando do dashboard;
- imagem local `vibestack-openclaw:latest` existe;
- container atual permanece estável;
- nenhuma operação anterior deixou build em execução;
- bundle `/opt/hermes-agent/hermes_cli/web_dist/index.html` existe na imagem/container atual.

Se qualquer item essencial falhar, retornar BLOCKED.

## EXECUÇÃO

### 1. Preservar a imagem atual

Criar tag local:

```bash
docker tag vibestack-openclaw:latest vibestack-openclaw:pre-dashboard-fix
```

Não remover nenhuma imagem.

### 2. Construir somente a camada do entrypoint

Criar temporariamente um Dockerfile mínimo fora dos arquivos versionados ou removê-lo ao final:

```dockerfile
FROM vibestack-openclaw:pre-dashboard-fix
COPY entrypoint.sh /usr/local/bin/entrypoint.sh
RUN chmod +x /usr/local/bin/entrypoint.sh
```

Construir:

```bash
docker build --pull=false -f <dockerfile-temporario> -t vibestack-openclaw:latest .
```

Regras:

- NÃO usar o Dockerfile principal;
- NÃO usar `docker compose build`;
- NÃO usar `--pull`;
- NÃO baixar nenhuma imagem;
- NÃO instalar dependência;
- se houver tentativa de download remoto, interromper e retornar BLOCKED.

O build esperado deve conter apenas base local, COPY do entrypoint e chmod.

### 3. Recriar somente o container principal

```bash
docker compose up -d --force-recreate --no-deps --no-build openclaw-vibestack
```

### 4. Validar a correção implantada

Confirmar dentro do container:

- entrypoint efetivo contém `--skip-build`;
- container running/up e sem restart loop;
- Hermes gateway ativo;
- Ollama HTTP interno 200 em 11434;
- dashboard interno 9120 ouvindo;
- bridge 9119 ouvindo;
- HTTP interno em 9119 responde.

Se o dashboard continuar falhando, registrar saída sanitizada de `/var/log/hermes-web.log` e parar. Não criar nova hipótese nesta TASK.

### 5. Validar host Windows

Testar `http://127.0.0.1:9119` pelo Windows host.

Se Work não conseguir acessar o namespace do host, registrar:

```text
HOST_ACCESS=UNVERIFIED_BY_WORK
```

### 6. Restart + persistência

Se dashboard, gateway e Ollama estiverem estáveis:

1. registrar metadados essenciais do estado persistente Hermes;
2. executar `docker compose restart openclaw-vibestack`;
3. aguardar estabilização;
4. revalidar gateway, dashboard e Ollama;
5. confirmar persistência do estado/configuração Hermes.

## POST-CHECK

PASS quando:

- overlay local criada sem download;
- entrypoint corrigido está na imagem/container;
- dashboard 9120 e 9119 funcionam;
- Hermes gateway funciona;
- Ollama funciona;
- restart funciona;
- persistência sobrevive;
- host Windows confirma dashboard.

PARTIAL somente se todos os critérios técnicos passarem e faltar exclusivamente comprovação pelo host Windows.

## Rollback

Se a nova camada causar regressão:

```bash
docker tag vibestack-openclaw:pre-dashboard-fix vibestack-openclaw:latest
docker compose up -d --force-recreate --no-deps --no-build openclaw-vibestack
```

Registrar rollback e retornar BLOCKED.

## Fora de escopo

Não:

- executar Dockerfile principal;
- baixar base;
- reinstalar dependências;
- habilitar API 8642;
- configurar modelo/provider;
- puxar modelo Ollama;
- configurar Telegram;
- alterar Compose;
- mudar arquitetura.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Fazer commit + push.

## Retorno

```text
TASK-0010: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
Overlay image: <status>
Dashboard 9120: <status>
Dashboard 9119: <status>
Hermes gateway: <status>
Ollama 11434: <status>
Restart/persistência: <status>
Host Windows: <status>
Bloqueio: <nenhum ou curto>
```