# TASK-0001 — Rotação de segredos e startup da stack Hermes-only

Status: READY

## Objetivo

Executar o primeiro startup controlado da stack 7Ps Marketing AI após o build, rotacionando previamente os segredos que foram expostos em saída compartilhada e validando Hermes + Ollama sem revelar nenhum segredo.

## Contexto aprovado

A arquitetura V1 definida é:

```text
OpenClaw: false
Hermes: true
Ollama: true
LM Studio: false
Evolution/WhatsApp: false
```

Diretório local do repositório:

```text
D:\Docker\7ps-marketing\repo
```

Persistência validada no disco D:.

A imagem `vibestack-openclaw:latest` já foi construída.

## Restrições

1. Não alterar arquitetura.
2. Não habilitar OpenClaw.
3. Não habilitar Evolution/WhatsApp.
4. Não configurar Meta Ads, Google Ads, AtlasCloud, Higgsfield ou Telegram nesta tarefa.
5. Não exibir, imprimir, copiar para logs ou versionar valores secretos.
6. Não executar `cat .env`.
7. Não usar comandos que mostrem valores completos de tokens.
8. Não commitAR o arquivo `.env`.
9. Se houver falha que exija decisão arquitetural, registrar BLOCKED e encerrar.

## PRE-CHECK

No ambiente local:

1. Confirmar Docker Desktop em execução.
2. Confirmar diretório do repo.
3. Confirmar imagem:
   `vibestack-openclaw:latest`.
4. Confirmar no `.env`, sem mostrar segredos:
   - INSTALL_OPENCLAW=false
   - INSTALL_HERMES=true
   - INSTALL_OLLAMA=true
   - INSTALL_LMSTUDIO=false
   - INSTALL_EVOLUTION=false
   - COMPOSE_PROFILES vazio
5. Confirmar mounts efetivos do Compose apontando para `D:/Docker/7ps-marketing/data/...`.

## EXECUÇÃO

### 1. Rotacionar segredos expostos

Rotacionar localmente, sem imprimir os novos valores:

- HERMES_API_SERVER_KEY
- OPENCLAW_GATEWAY_TOKEN
- EVOLUTION_API_KEY
- EVOLUTION_INSTANCE_TOKEN
- POSTGRES_PASSWORD
- GOG_KEYRING_PASSWORD

Usar geração criptograficamente segura com 32 bytes em hexadecimal quando compatível.

Mesmo os segredos de componentes atualmente desabilitados devem ser rotacionados porque seus valores anteriores foram expostos.

Validar apenas:

```text
<VAR>: SET
<VAR>_LENGTH: <n>
```

Nunca registrar o valor.

### 2. Subir a stack

Executar a partir do diretório do repo:

```bash
docker compose up -d
```

### 3. Validar container

Executar:

```bash
docker compose ps
```

Confirmar que o serviço principal está em estado running/up e não reiniciando continuamente.

### 4. Validar logs sanitizados

Analisar logs do serviço principal procurando:

- inicialização do Ollama;
- inicialização do Hermes dashboard;
- `hermes gateway run` em foreground;
- ausência de erro de API_SERVER_KEY vazio;
- ausência de crash/restart loop.

Não copiar tokens, keys ou conteúdo sensível para o relatório.

### 5. Validar Ollama

Executar o helper disponível na imagem:

```bash
docker compose exec openclaw-vibestack models-status
```

Também validar que o serviço responde internamente ou em `127.0.0.1:11434`, sem instalar modelo nesta tarefa.

### 6. Validar Hermes

Validar:

- processo/gateway ativo;
- API local na porta 8642;
- dashboard local na porta 9119.

A validação da API deve usar a chave local sem imprimi-la.

Não configurar provider/modelo nesta tarefa.

## Critérios de aceite

A tarefa é PASS quando:

- todos os segredos expostos foram rotacionados;
- nenhum segredo novo foi exibido ou commitado;
- container principal está estável;
- Hermes Gateway está ativo;
- Hermes API responde na porta 8642;
- dashboard Hermes responde na porta 9119;
- Ollama está ativo na porta 11434;
- OpenClaw continua desabilitado;
- Evolution/WhatsApp continua desabilitado;
- não houve alteração arquitetural.

## Rollback

Se a stack falhar após startup:

```bash
docker compose down
```

Não remover volumes ou diretórios persistentes.

Não executar `docker compose down -v`.

## Registro obrigatório pelo Work

Ao concluir, atualizar este bloco:

### Resultado

Status: READY
Data:
Ambiente:
Branch:
Commit:
Arquivos alterados:
Validações executadas:
Resultado:
Desvios:
Pendências:
Próximo passo recomendado:

## Atualização adicional

Após a execução, atualizar também:

```text
docs/ops/current-state.md
```

com o novo estado operacional, sem registrar segredos.
