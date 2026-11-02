# TASK-0002 — Verificar runtime atual Hermes + Ollama

Status: BLOCKED

## Objetivo

Confirmar o estado operacional atual da stack local após a execução anterior, sem refazer instalação, build ou rotação de segredos.

Esta TASK serve apenas para validar o checkpoint da Fase 1 antes de avançar para o benchmark de modelos da Fase 2.

## Execução

Ambiente: Work / Local

Repo local:

```text
D:\Docker\7ps-marketing\repo
```

Antes de executar:

1. sincronizar com `origin/main` por fast-forward seguro;
2. se houver conflito/divergência inesperada, retornar BLOCKED.

Depois validar:

1. `docker compose ps`;
2. serviço principal estável, sem restart loop;
3. Ollama respondendo em `127.0.0.1:11434`;
4. Hermes API respondendo em `127.0.0.1:8642`;
5. Hermes dashboard respondendo em `127.0.0.1:9119`;
6. `INSTALL_OPENCLAW=false`;
7. `INSTALL_HERMES=true`;
8. `INSTALL_OLLAMA=true`;
9. `INSTALL_EVOLUTION=false`.

Não exibir segredos.

Se o container estiver parado, é permitido executar somente:

```bash
docker compose up -d
```

Não fazer build, rebuild, pull de modelo, instalação de dependência ou alteração de arquitetura.

## Docker access fallback

A interface do Docker Desktop pode estar ativa mesmo quando a sessão Work/Local não encontra o CLI no PATH.

Antes de declarar Docker indisponível:

1. tentar `docker version`;
2. se `docker` não for encontrado, localizar o executável padrão no Windows:
   `C:\Program Files\Docker\Docker\resources\bin\docker.exe`;
3. usar o caminho absoluto do executável para validar:
   - `version`;
   - `context show`;
   - `info`;
4. se o executável existir e o Engine responder, continuar a TASK normalmente;
5. somente registrar `BLOCKED` se o executável não existir ou se o Engine/socket continuar inacessível mesmo pelo caminho absoluto.

Não reinstalar Docker, não alterar permissões do Windows e não mudar contexto automaticamente além de leitura/validação.

## Limites

- alvo: 5 minutos;
- máximo 2 tentativas corretivas;
- caminho mínimo;
- ao validar todos os itens, parar.

## Resultado

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Fazer commit + push se houver alteração documental.

Retornar apenas:

```text
TASK-0002: PASS | BLOCKED | FAILED
Commit: <sha ou n/a>
Hermes: <status>
Ollama: <status>
Dashboard: <status>
Desvios: <curto>
Bloqueio: <curto ou nenhum>
```

## Resultado da verificacao — 2026-11-02

Status: BLOCKED
Docker Engine acessivel fora do isolamento; Compose nao identificou containers do projeto, inclusive parados. As portas 11434, 8642 e 9119 nao responderam. As quatro opcoes solicitadas no arquivo local coincidem com os valores esperados. Nenhum container foi criado ou iniciado; nao houve build nem rotacao de segredos. A existencia e rotacao de segredos anteriores permanecem sem evidencia suficiente. Proximo passo: decidir como estabelecer o runtime e comprovar a rotacao antes da operacao.
