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

Status: READY
Classificação: A | B | C | D
Git:
Docker:
Compose:
Env sanitizado:
Hermes persistente:
Ollama persistente:
Diferenças em relação ao checkpoint pós-build:
Ação mínima recomendada:

Fazer commit + push.

## Retorno ao Chat

Retornar somente:

```text
TASK-0006: PASS | BLOCKED
Classificação: A | B | C | D
Commit: <sha>
Diferenças: <resumo curto>
Ação mínima recomendada: <uma linha>
```
