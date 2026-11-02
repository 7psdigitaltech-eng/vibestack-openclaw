# TASK-0003 — Bring-up definitivo Hermes + Ollama

Status: READY

## Objetivo

Subir pela primeira vez a stack local já construída, usando a imagem existente, e validar Hermes + Ollama.

Também rotacionar os segredos que foram expostos anteriormente no chat.

## Ambiente

Work / Local

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Antes de executar, sincronizar o clone com `origin/main` por fast-forward seguro.

## Regras

- ausência de container NÃO é bloqueio;
- usar a imagem existente `vibestack-openclaw:latest`;
- NÃO fazer build/rebuild;
- NÃO instalar dependências;
- NÃO puxar modelos;
- NÃO habilitar OpenClaw;
- NÃO habilitar Evolution;
- NÃO alterar arquitetura;
- NÃO exibir segredos.

## Execução

### 1. Rotacionar segredos expostos

Atualizar no `.env`, sem imprimir os valores:

- HERMES_API_SERVER_KEY
- OPENCLAW_GATEWAY_TOKEN
- EVOLUTION_API_KEY
- EVOLUTION_INSTANCE_TOKEN
- POSTGRES_PASSWORD
- GOG_KEYRING_PASSWORD

Gerar valores criptograficamente seguros.

Validar apenas presença/tamanho, nunca o conteúdo.

### 2. Subir a stack sem build

Executar:

```bash
docker compose up -d --no-build
```

Se não existir container, o Compose deve criá-lo a partir da imagem existente.

### 3. Validar

Confirmar:

- serviço principal em running/up;
- sem restart loop;
- Hermes gateway ativo;
- Hermes API responde em `127.0.0.1:8642`;
- dashboard responde em `127.0.0.1:9119`;
- Ollama responde em `127.0.0.1:11434`;
- OpenClaw continua desabilitado;
- Evolution continua desabilitado.

Pode consultar logs sanitizados do serviço principal.

### 4. Documentar

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Fazer commit + push.

## Limites

- alvo: até 10 minutos;
- máximo 2 tentativas corretivas;
- se `docker compose up -d --no-build` falhar, diagnosticar somente o erro direto;
- não fazer rebuild como fallback;
- se a segunda tentativa falhar, retornar BLOCKED.

## Critério de aceite

PASS somente se:

- segredos expostos foram rotacionados;
- container foi criado/iniciado;
- Hermes API responde;
- dashboard responde;
- Ollama responde;
- nenhum segredo foi exposto/versionado;
- documentação foi atualizada;
- commit remoto confirmado.

## Retorno

```text
TASK-0003: PASS | BLOCKED | FAILED
Commit: <sha>
Container: <status>
Hermes: <status>
Ollama: <status>
Dashboard: <status>
Desvios: <curto>
Bloqueio: <curto ou nenhum>
```
