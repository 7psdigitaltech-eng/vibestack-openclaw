# Contrato de Ambientes de Execução

Este documento define onde cada tipo de TASK pode ser executado.

## 1. Tipos de ambiente

### REPO_CLOUD

Usar quando a tarefa envolve apenas:

- leitura/escrita no GitHub;
- documentação;
- branches, commits, PRs;
- análise de código sem depender do host local;
- workflows GitHub Actions já autorizados.

Não assumir acesso a:

- `D:\`;
- Docker Desktop do usuário;
- processos locais do Windows;
- arquivos fora do repositório remoto.

### LOCAL_DESKTOP

**Superfície padrão:** ChatGPT Work em modo **Local**.

Não selecionar Work/Cloud para tarefas deste tipo.

Usar quando a tarefa depende de:

- repositório local;
- PowerShell/Git Bash local;
- Docker Desktop;
- WSL2;
- arquivos persistidos em `D:\Docker\...`;
- serviços ou portas no host do usuário.

Uma TASK `LOCAL_DESKTOP` só pode iniciar após provar acesso real ao host local.

## 2. Preflight obrigatório para LOCAL_DESKTOP

Antes de consumir o orçamento da TASK, executar um preflight com orçamento máximo de 2 minutos.

Confirmar, sem fazer alterações:

1. acesso ao diretório local declarado na TASK;
2. leitura do arquivo da TASK no clone local;
3. `git status` executável;
4. `docker info` executável;
5. `docker compose version` executável.

Resultado esperado:

```text
LOCAL_REPO_ACCESS=PASS
LOCAL_TASK_FILE=PASS
GIT_ACCESS=PASS
DOCKER_ACCESS=PASS
COMPOSE_ACCESS=PASS
```

Se qualquer item falhar:

```text
EXECUTION_ENVIRONMENT=BLOCKED
```

e encerrar imediatamente.

Não tentar instalar Docker, alterar permissões, mudar de ambiente, clonar em outro lugar, usar Docker remoto ou executar fallback em cloud sem autorização explícita.

## 3. Regra para sessões do Work

Não presumir que uma sessão do Work possui acesso local só porque a TASK menciona um caminho Windows.

Para tarefas `LOCAL_DESKTOP`, o executor precisa estar numa sessão/superfície que efetivamente permita acesso ao computador local.

Se o executor estiver em ambiente cloud/sandbox sem acesso ao host, deve retornar BLOCKED no preflight.

Para o fluxo atual da 7Ps, a seleção operacional é:

```text
TASK Execution target = LOCAL_DESKTOP
        ↓
abrir Work
        ↓
selecionar Local
        ↓
executar preflight
        ↓
somente após PASS executar a TASK
```

Work/Cloud é apropriado para `REPO_CLOUD`, não para Docker/filesystem do PC.

## 4. Cabeçalho obrigatório nas TASKs

Toda TASK técnica deve declarar:

```text
Execution target: REPO_CLOUD | LOCAL_DESKTOP
Local repo path: <quando aplicável>
Required capabilities:
```

Exemplo:

```text
Execution target: LOCAL_DESKTOP
Local repo path: D:\Docker\7ps-marketing\repo
Required capabilities:
- local filesystem
- Git
- Docker Desktop
- Docker Compose
```

## 5. Separação entre preflight e execução

O preflight de 2 minutos não autoriza nenhuma correção.

Ele só responde:

```text
o executor consegue operar o ambiente correto?
```

Somente após PASS começa o orçamento da TASK.

## 6. Estratégia futura para maior confiabilidade

Se tarefas LOCAL_DESKTOP forem frequentes e o Work não tiver acesso local consistente, avaliar uma camada de execução dedicada, como um runner local controlado, em tarefa arquitetural separada.

Essa decisão não deve ser implementada silenciosamente por uma TASK operacional.


## 7. Sincronização obrigatória com GitHub

Uma TASK local não é considerada concluída apenas porque os comandos locais passaram.

Antes de retornar `PASS`, o Work/Local deve:

1. atualizar os documentos exigidos pela TASK;
2. criar commit real;
3. fazer push para o remote autorizado;
4. confirmar que o commit existe em `origin`;
5. retornar o SHA real (mínimo 7 caracteres, não placeholder).

Se a execução local passou mas push/commit falhou:

```text
Status: PARTIAL
Local execution: PASS
GitHub synchronization: FAIL
```

Nunca retornar `PASS` com SHA fictício, exemplo, placeholder ou commit apenas local.
