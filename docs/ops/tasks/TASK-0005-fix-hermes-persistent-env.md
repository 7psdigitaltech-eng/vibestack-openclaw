# TASK-0005 — Corrigir precedência do ambiente Hermes e abrir API 8642

Status: READY

## Objetivo

Corrigir o Hermes sem rebuild, considerando que o arquivo persistente em `/root/.hermes/.env` pode sobrescrever variáveis recebidas do Docker.

## Ambiente

Work / Local

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## Execução

### 1. Alinhar o ambiente persistente do Hermes

Dentro do container em execução, atualizar de forma segura:

```text
/root/.hermes/.env
```

Garantir estes valores:

```text
API_SERVER_ENABLED=true
API_SERVER_HOST=0.0.0.0
API_SERVER_PORT=8642
API_SERVER_KEY=<usar exatamente a chave já presente no ambiente do container>
```

Regras:

- NÃO imprimir o valor de `API_SERVER_KEY`;
- NÃO criar nova chave;
- NÃO alterar outros segredos;
- preservar outras linhas existentes no arquivo;
- manter permissão restrita do arquivo.

A chave deve ser copiada programaticamente do ambiente atual do container para o arquivo persistente, sem aparecer em stdout/stderr.

### 2. Reiniciar sem rebuild

Executar somente restart/recreate necessário para o Hermes reler o ambiente persistente.

Permitido:

```bash
docker compose restart openclaw-vibestack
```

Se restart simples não recriar o estado necessário, permitido:

```bash
docker compose up -d --force-recreate --no-build
```

Não fazer build/rebuild.

### 3. Validar internamente

Confirmar dentro do container:

- Ollama listener 11434;
- Hermes API listener 8642;
- dashboard/bridge listener 9119;
- processo `hermes gateway run` ativo;
- ausência de restart loop.

Validar `/health` da API Hermes internamente.

### 4. Validar port mapping

Confirmar Docker publicando:

- 127.0.0.1:11434 -> 11434
- 127.0.0.1:8642 -> 8642
- 127.0.0.1:9119 -> 9119

Nesta TASK, falha de HTTP feita a partir do shell isolado do Work NÃO bloqueia se:
- listener interno = PASS;
- health interno = PASS;
- port mapping = PASS.

### 5. Documentar

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Fazer commit + push.

## Limites

- até 10 minutos;
- máximo 2 tentativas;
- sem rebuild;
- sem instalação;
- sem pull de modelo;
- sem mudança de arquitetura;
- sem Telegram/Meta/Higgsfield.

Se 8642 continuar fechada após alinhar `/root/.hermes/.env` e reiniciar, registrar BLOCKED e incluir apenas o erro sanitizado relevante do Hermes.

## Critérios de aceite

PASS quando:

- `/root/.hermes/.env` está alinhado;
- chave existente foi reutilizada sem exposição;
- Hermes API escuta em 8642;
- `/health` interno responde;
- Ollama segue em 11434;
- dashboard segue em 9119;
- port mappings estão corretos;
- nenhum segredo foi exposto;
- documentação foi atualizada;
- commit remoto confirmado.

## Retorno

```text
TASK-0005: PASS | BLOCKED | FAILED
Commit: <sha>
Hermes 8642: <status>
Hermes /health: <status>
Ollama 11434: <status>
Dashboard 9119: <status>
Port mapping: <status>
Desvios: <curto>
Bloqueio: <curto ou nenhum>
```
