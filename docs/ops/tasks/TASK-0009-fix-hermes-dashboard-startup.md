# TASK-0009 — Corrigir startup do dashboard Hermes e concluir Fase 1

Status: READY

## Contexto confirmado

A TASK-0008 isolou o bloqueio no dashboard:

- container: estável;
- Hermes gateway: ativo;
- Ollama 11434: HTTP interno 200;
- bridge 9119: aberta;
- servidor real do dashboard em 9120: não abriu.

A revisão do Hermes atual confirmou:

1. a imagem já pré-compila o dashboard durante o build;
2. o build atual gera o bundle esperado em:
   `/opt/hermes-agent/hermes_cli/web_dist/index.html`;
3. em Docker, o Hermes suporta/recomenda `--skip-build` para servir esse bundle pronto;
4. `--tui` é apenas compatibilidade legada e hoje é ignorado.

Portanto, o dashboard não deve tentar reavaliar/recompilar a UI no boot do container.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 15 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Rebuild allowed: SIM, somente rebuild da imagem existente usando cache
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

Confirmar antes da alteração:

- container atual usa `vibestack-openclaw:latest`;
- Hermes gateway está ativo;
- Ollama responde internamente em 11434;
- `/opt/hermes-agent/hermes_cli/web_dist/index.html` existe dentro da imagem/container atual.

Se o bundle não existir, retornar BLOCKED e não ampliar o escopo.

## EXECUÇÃO

### 1. Corrigir entrypoint

No bloco que inicia o dashboard Hermes, alterar o comando para usar o bundle pré-compilado:

De:

```bash
hermes dashboard --host 127.0.0.1 --port "$HERMES_WEB_INTERNAL_PORT" --no-open --tui
```

Para:

```bash
hermes dashboard --host 127.0.0.1 --port "$HERMES_WEB_INTERNAL_PORT" --no-open --skip-build
```

Não alterar:

- porta interna 9120;
- bridge socat 9119 -> 9120;
- gateway Hermes;
- Ollama;
- API 8642;
- arquitetura Compose.

### 2. Rebuild controlado

Rebuildar somente a imagem do serviço, usando cache:

```bash
docker compose build openclaw-vibestack
```

Não usar `--no-cache`.

### 3. Recriar o container

Subir a nova imagem:

```bash
docker compose up -d --force-recreate --no-deps openclaw-vibestack
```

### 4. Validar startup

Confirmar:

- container running/up;
- sem restart loop;
- Hermes gateway ativo;
- Ollama 11434 responde internamente;
- servidor dashboard interno 9120 está ouvindo;
- bridge 9119 está ouvindo;
- HTTP interno em `http://127.0.0.1:9119` responde;
- mapping Docker de 9119 e 11434 permanece em loopback.

Consultar `/var/log/hermes-web.log` somente de forma sanitizada se necessário.

### 5. Validar acesso local pelo Windows host

Testar `http://127.0.0.1:9119` usando ferramenta do próprio Windows host.

Se o Work não puder acessar o namespace do Windows, registrar `HOST_ACCESS=UNVERIFIED_BY_WORK`, sem inventar resultado.

### 6. Restart + persistência

Após o dashboard estabilizar:

1. registrar estado/configuração persistente essencial do Hermes sem conteúdo secreto;
2. executar:
   `docker compose restart openclaw-vibestack`;
3. aguardar estabilização;
4. revalidar:
   - container;
   - Hermes gateway;
   - dashboard 9119/9120;
   - Ollama 11434;
5. confirmar que estado/configuração persistente do Hermes sobreviveu ao restart.

## POST-CHECK

PASS técnico quando:

- dashboard interno funciona;
- Ollama continua funcionando;
- gateway continua funcionando;
- restart funciona;
- persistência sobrevive.

A Fase 1 só pode ser marcada como totalmente concluída se o dashboard também for comprovadamente acessível pelo host Windows.

Se todos os critérios técnicos passarem mas o Work não conseguir comprovar o host Windows, retornar PARTIAL e registrar apenas essa pendência.

## Fora de escopo

Não:

- habilitar API Hermes 8642;
- configurar modelo/provider;
- puxar modelo Ollama;
- configurar Telegram;
- alterar Compose;
- instalar dependências;
- mudar arquitetura.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Registrar explicitamente se a Fase 1 ficou:

- PASS; ou
- PARTIAL apenas por falta de comprovação do acesso via host Windows.

Fazer commit + push.

## Retorno

```text
TASK-0009: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
Dashboard 9120: <status>
Dashboard 9119: <status>
Hermes gateway: <status>
Ollama 11434: <status>
Restart/persistência: <status>
Host Windows: <status>
Bloqueio: <nenhum ou curto>
```
