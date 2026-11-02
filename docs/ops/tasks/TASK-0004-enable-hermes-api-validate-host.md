# TASK-0004 — Habilitar Hermes API e validar portas no host

Status: READY

## Objetivo

Corrigir o bloqueio encontrado na TASK-0003 sem rebuild.

Causa já identificada:

- Hermes gateway está em execução;
- Ollama e dashboard possuem listener dentro do container;
- porta 8642 está fechada dentro do container;
- Hermes exige `API_SERVER_ENABLED=true` para iniciar o API server; o default é false.

## Ambiente

Work / Local

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro antes de executar.

## Execução

### 1. Habilitar API server no ambiente local

No `.env`, garantir:

```text
API_SERVER_ENABLED=true
```

Não alterar nem imprimir `HERMES_API_SERVER_KEY`.

Como o compose utiliza `env_file: .env`, essa variável será enviada ao container.

### 2. Tornar a configuração reproduzível

Atualizar `.env.example` para incluir:

```text
API_SERVER_ENABLED=true
```

próximo às variáveis Hermes.

Não adicionar segredo real.

### 3. Recriar container sem rebuild

Executar:

```bash
docker compose up -d --force-recreate --no-build
```

Não fazer build/rebuild.

### 4. Validar dentro do container

Confirmar listeners:

- 11434 — Ollama;
- 8642 — Hermes API;
- 9119 — bridge/dashboard.

Validar também logs sanitizados do Hermes apenas se necessário.

### 5. Validar publicação de portas

Executar inspeção Docker (`docker compose ps`, `docker port` ou equivalente) e confirmar que estão publicadas em loopback do Windows:

- 127.0.0.1:11434
- 127.0.0.1:8642
- 127.0.0.1:9119

### 6. Testar pelo host Windows

Não usar apenas o localhost de um shell isolado do Work.

Executar o teste HTTP no contexto Windows/host, por exemplo via `powershell.exe` ou `curl.exe` do Windows.

Validar:

- Ollama: endpoint HTTP responde em 11434;
- Hermes: `/health` responde em 8642;
- Dashboard: HTTP responde em 9119.

Para qualquer endpoint que exija autenticação, usar a chave do ambiente local sem imprimi-la.

## Limites

- até 10 minutos;
- máximo 2 tentativas corretivas;
- sem rebuild;
- sem instalação;
- sem troca de arquitetura;
- sem pull de modelo;
- não mexer em Telegram/Meta/Higgsfield.

Se `API_SERVER_ENABLED=true` fizer 8642 abrir mas algum teste no host ainda falhar, registrar separadamente:
- listener interno;
- port mapping Docker;
- teste Windows host;

e parar, sem expandir investigação.

## Critérios de aceite

PASS quando:

- API server Hermes está habilitado;
- 8642 está aberta dentro do container;
- Ollama continua ativo;
- dashboard continua ativo;
- mapeamentos Docker estão corretos;
- testes pelo host Windows respondem;
- nenhum segredo foi exposto;
- `.env.example` foi atualizado sem segredo;
- documentação foi atualizada;
- commit + push concluídos.

## Registro

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

## Retorno

```text
TASK-0004: PASS | BLOCKED | FAILED
Commit: <sha>
Hermes API: <status>
Ollama: <status>
Dashboard: <status>
Host ports: <status>
Desvios: <curto>
Bloqueio: <curto ou nenhum>
```
