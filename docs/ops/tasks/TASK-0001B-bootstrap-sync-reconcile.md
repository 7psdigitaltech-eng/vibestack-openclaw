# TASK-0001B — Bootstrap seguro do clone e reconciliação da TASK-0001

Status: SUPERSEDED

## Objetivo

Resolver o bloqueio de bootstrap do Work/Local sem repetir a execução técnica da TASK-0001.

A tarefa deve:

1. sincronizar o clone local com `origin/main` apenas por fast-forward seguro;
2. confirmar que os documentos operacionais mais recentes existem localmente;
3. reconciliar o resultado real da execução local anterior da TASK-0001;
4. atualizar e sincronizar a documentação com o GitHub remoto.

## Execution target

```text
Execution target: LOCAL_DESKTOP
Surface: ChatGPT Work — Local
Local repo path: D:\Docker\7ps-marketing\repo
Required capabilities:
- local filesystem
- Git
- acesso ao remote origin
```

## Execution budget

```text
Execution class: S
Bootstrap budget: 2 min
Task budget after bootstrap: 5 min
Diagnostic hypotheses: max 1
Corrective attempts: max 1
Docker changes allowed: NO
Rebuild allowed: NO
New dependency allowed: NO
Architecture change allowed: NO
Destructive actions allowed: NO
Force/reset/clean/rebase allowed: NO
```

Aplicar:
- `docs/ops/execution-environments.md`
- `docs/ops/execution-guardrails.md`

## Fase 0 — Bootstrap de sincronização

Esta fase é autorizada ANTES da leitura local desta TASK.

No clone local:

1. entrar em `D:\Docker\7ps-marketing\repo`;
2. executar `git status --porcelain`;
3. executar `git fetch origin`;
4. verificar `HEAD`, `origin/main` e relação de ancestry;
5. somente se:
   - working tree estiver limpo;
   - não houver commits locais pendentes;
   - branch local estiver apenas atrás de `origin/main`;

   executar:

   ```bash
   git merge --ff-only origin/main
   ```

6. confirmar existência local de:
   - `docs/ops/execution-environments.md`
   - `docs/ops/execution-guardrails.md`
   - `docs/ops/tasks/TASK-0001B-bootstrap-sync-reconcile.md`

Se houver dirty tree, branch ahead, divergência ou conflito:

```text
TASK-0001B: BLOCKED
Reason: unsafe local git state
```

e parar.

É proibido:
- `git reset --hard`;
- `git clean`;
- rebase;
- force push;
- descarte de arquivos locais;
- alteração de Docker;
- alteração de `.env`.

## Fase 1 — Preflight

Após bootstrap PASS:

1. ler integralmente esta TASK no clone local;
2. confirmar acesso ao Git;
3. confirmar remote `origin`;
4. NÃO exigir Docker nesta tarefa, pois nenhuma ação Docker está autorizada.

## Fase 2 — Reconciliar TASK-0001

Não repetir comandos Docker da TASK-0001.

Verificar apenas o estado local já existente da execução anterior:

### Caso A — existe commit local anterior com documentação/evidência

- identificar SHA real;
- verificar que não contém `.env` nem segredos;
- preservar documentação operacional mais recente;
- integrar apenas se seguro;
- push;
- confirmar SHA no remote.

### Caso B — não existe commit, mas há evidência local suficiente do PASS anterior

Sem alterar Docker:

- atualizar `docs/ops/tasks/TASK-0001-rotate-secrets-start-hermes-stack.md`;
- atualizar `docs/ops/current-state.md`;
- registrar somente evidência sanitizada;
- commit;
- push;
- confirmar SHA remoto.

### Caso C — não há evidência suficiente

Não repetir a TASK-0001.

Registrar:

```text
TASK-0001B: BLOCKED
Reason: insufficient retained evidence from prior local execution
```

e parar.

## Critérios de aceite

PASS somente se:

- bootstrap seguro = PASS;
- clone local contém os documentos operacionais atuais;
- nenhuma mudança Docker foi realizada;
- TASK-0001 foi reconciliada com base em evidência real existente;
- current-state foi atualizado;
- nenhum segredo foi commitado;
- commit real foi enviado ao GitHub;
- SHA remoto foi confirmado.

## Resultado

Status: READY
Bootstrap sync:
Local git state:
TASK-0001 reconciled:
Current-state updated:
Remote commit:
Secrets committed:
Desvios:
Bloqueio:


## Superseded

Esta TASK foi encerrada como parte da simplificação do fluxo Chat → GitHub → Work. Sincronização do clone e preflight passam a ser etapas iniciais da própria TASK operacional, conforme `docs/ops/WORK.md`.
