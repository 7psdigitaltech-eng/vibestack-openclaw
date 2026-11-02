# Work Handoff — padrão simples

Este é o padrão operacional canônico para enviar tarefas do Chat ao Work.

## Princípio

```text
Chat decide e documenta a TASK no GitHub.
Work executa a TASK.
GitHub guarda o resultado.
Chat recebe apenas um resumo curto.
```

## Seleção do ambiente

- TASK que depende do PC, Docker, PowerShell, WSL ou arquivos em `D:\` → **Work / Local**.
- TASK que depende apenas do repositório remoto, documentação, pesquisa ou GitHub → **Work / Cloud**.

## Prompt curto padrão

Para Work / Local:

```text
Execute a TASK-XXXX deste projeto em modo Local.

Repo local: D:\Docker\7ps-marketing\repo
Instruções completas: docs/ops/tasks/TASK-XXXX-<slug>.md

Antes, sincronize o clone com origin/main usando somente fast-forward seguro.
Depois execute exatamente a TASK, documente o resultado, faça commit/push quando aplicável e retorne apenas status + SHA + bloqueio/desvio.
```

Para Work / Cloud:

```text
Execute a TASK-XXXX deste projeto em modo Cloud.

Repo: 7psdigitaltech-eng/vibestack-openclaw
Instruções completas: docs/ops/tasks/TASK-XXXX-<slug>.md

Execute exatamente a TASK, documente o resultado no GitHub e retorne apenas status + SHA + bloqueio/desvio.
```

## Regras mínimas

1. A TASK contém o detalhamento técnico; o prompt do Chat não repete esse conteúdo.
2. Em Work / Local, sincronizar o clone antes de ler a TASK:
   - `git fetch origin`
   - se estiver limpo e apenas atrás: `git merge --ff-only origin/main`
   - se houver conflito/divergência/alteração local inesperada: `BLOCKED`
3. Caminho mínimo seguro.
4. Máximo padrão: 15 min e 2 tentativas corretivas, salvo instrução diferente na TASK.
5. Não fazer rebuild, instalar dependência, mudar arquitetura ou ampliar escopo sem autorização explícita na TASK.
6. Ao cumprir os critérios de aceite: parar.
7. Se não conseguir pelo caminho previsto: `BLOCKED`, registrar motivo e parar.
8. Nunca expor ou versionar segredos.
9. Quando houver alteração versionada: commit + push.
10. Retorno ao Chat deve ser curto:

```text
TASK-XXXX: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha real ou n/a>
Desvios: <curto>
Bloqueio: <curto ou nenhum>
```

## Regra de eficiência

Não criar TASKs intermediárias apenas para sincronização, reconciliação ou preflight quando isso puder ser resolvido como etapa inicial da própria TASK.

O objetivo é reduzir atrito, não criar burocracia.
