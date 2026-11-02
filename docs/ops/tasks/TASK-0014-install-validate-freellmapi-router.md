# TASK-0014 — Instalar e validar FreeLLMAPI como router interno

Status: PASS

## Objetivo

Adicionar o FreeLLMAPI como segundo serviço da stack 7Ps Marketing AI e validar:

- startup;
- persistência;
- consumo local;
- acesso pelo host Windows;
- comunicação interna a partir do container Hermes.

NÃO configurar providers externos nesta TASK.
NÃO alterar o provider/modelo atual do Hermes nesta TASK.

## Contexto

A Fase 1 está PASS.

A Fase 2 local não aprovou `qwen3:4b` nem `llama3.2:3b` para o Diretor devido ao mínimo rígido de 64K do Hermes e à pressão de memória local.

O ADR-0001 autoriza avaliar FreeLLMAPI como router de inferência.

Versão pinada para esta avaliação:

```text
ghcr.io/tashfeenahmed/freellmapi:v0.16.16
```

Não usar `:latest` nesta TASK.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: M
Hard budget: 20 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Image pull allowed: SIM, somente FreeLLMAPI v0.16.16
Rebuild allowed: NÃO
New dependency allowed: NÃO
Architecture change allowed: SIM, somente adicionar o serviço FreeLLMAPI aprovado
Destructive actions allowed: NÃO
Provider credentials allowed: NÃO

Repo:

```text
D:\Docker\7ps-marketing\repo
```

## PRE-CHECK

Sincronizar com `origin/main` por fast-forward seguro.

Confirmar:

- stack atual estável;
- Hermes gateway ativo;
- dashboard Hermes HTTP 200;
- Ollama HTTP 200;
- nenhum modelo Ollama carregado;
- porta 3001 do host está livre;
- diretório `D:\Docker\7ps-marketing\data` acessível.

Registrar baseline de CPU/RAM do container principal.

## EXECUÇÃO

### 1. Persistência local

Criar, se não existir:

```text
D:\Docker\7ps-marketing\data\freellmapi
```

Não apagar conteúdo existente se houver.

### 2. Segredo de criptografia

No `.env` local, adicionar somente se ainda não existir:

```text
FREELLMAPI_ENCRYPTION_KEY=<64 hex aleatórios>
FREELLMAPI_DATA_DIR=D:/Docker/7ps-marketing/data/freellmapi
FREELLMAPI_PORT=3001
FREELLMAPI_IMAGE=ghcr.io/tashfeenahmed/freellmapi:v0.16.16
```

Gerar `FREELLMAPI_ENCRYPTION_KEY` criptograficamente.

Nunca imprimir o valor.
Nunca versionar o `.env`.

Validar apenas:

```text
FREELLMAPI_ENCRYPTION_KEY=SET
length=64
```

### 3. Atualizar .env.example

Adicionar sem segredo real:

```text
# FreeLLMAPI — router OpenAI-compatible para providers remotos.
FREELLMAPI_IMAGE=ghcr.io/tashfeenahmed/freellmapi:v0.16.16
FREELLMAPI_PORT=3001
FREELLMAPI_DATA_DIR=/root/.freellmapi
FREELLMAPI_ENCRYPTION_KEY=
```

### 4. Adicionar serviço ao docker-compose.yml

Adicionar serviço irmão `freellmapi` na mesma stack/rede default:

```yaml
  freellmapi:
    image: ${FREELLMAPI_IMAGE:-ghcr.io/tashfeenahmed/freellmapi:v0.16.16}
    restart: unless-stopped
    environment:
      NODE_ENV: production
      PORT: 3001
      ENCRYPTION_KEY: ${FREELLMAPI_ENCRYPTION_KEY}
      FREELLMAPI_UPDATE_CHECK: "off"
    ports:
      - "127.0.0.1:${FREELLMAPI_PORT:-3001}:3001"
    volumes:
      - ${FREELLMAPI_DATA_DIR:-/root/.freellmapi}:/app/server/data
    healthcheck:
      test:
        - CMD
        - node
        - -e
        - "fetch('http://127.0.0.1:3001/api/ping').then((r)=>{if(!r.ok)process.exit(1)}).catch(()=>process.exit(1))"
      interval: 10s
      timeout: 5s
      start_period: 15s
      retries: 6
```

Regras:

- NÃO usar `env_file: .env` no serviço FreeLLMAPI;
- passar somente variáveis explicitamente necessárias;
- isso evita expor segredos Meta/Google/Higgsfield/etc. ao novo container;
- NÃO adicionar Watchtower;
- NÃO expor em `0.0.0.0`;
- NÃO adicionar provider keys.

### 5. Validar configuração Compose

Executar validação do Compose antes de subir.

Confirmar que:

- serviço `freellmapi` existe;
- bind host = `127.0.0.1:3001`;
- bind de dados aponta para `D:/Docker/7ps-marketing/data/freellmapi`;
- container não recebe variáveis de credenciais alheias.

### 6. Baixar e subir somente FreeLLMAPI

Executar somente o pull necessário para a imagem pinada e subir o serviço:

```bash
docker compose pull freellmapi
docker compose up -d --no-deps freellmapi
```

Não rebuildar imagem alguma.

### 7. Validar container

Confirmar:

- container running;
- health = healthy;
- imagem exata v0.16.16;
- sem restart loop.

### 8. Validar host Windows

Testar no host:

```text
http://127.0.0.1:3001/api/ping
http://127.0.0.1:3001/
```

Esperado:

- `/api/ping` responde;
- dashboard carrega localmente.

Não criar conta/admin automaticamente nesta TASK.

### 9. Validar comunicação Hermes -> FreeLLMAPI

A partir do container `openclaw-vibestack`, testar:

```text
http://freellmapi:3001/api/ping
```

PASS somente se responder pela rede interna do Compose.

Não usar `host.docker.internal`.
Não alterar Hermes config.

### 10. Medir overhead

Registrar:

- RAM idle do FreeLLMAPI;
- CPU idle;
- RAM total da stack antes/depois;
- tamanho inicial da pasta persistente.

O objetivo é confirmar que o router é significativamente mais leve que carregar um LLM local.

### 11. Restart/persistência

Reiniciar somente FreeLLMAPI.

Confirmar:

- volta healthy;
- dashboard volta;
- Hermes container continua alcançando `freellmapi:3001`;
- conteúdo persistente continua presente.

## POST-CHECK

PASS quando:

- serviço FreeLLMAPI integrado ao Compose;
- imagem pinada v0.16.16;
- health PASS;
- dashboard local acessível;
- Hermes container alcança o router internamente;
- persistência funciona;
- consumo local registrado;
- nenhum provider configurado;
- nenhum segredo exposto;
- Hermes/Ollama existentes permanecem estáveis.

## Fora de escopo

Não:

- criar chave Google/Groq/OpenRouter/NVIDIA;
- inserir provider key;
- configurar fallback chain;
- rodar `setup-hermes`;
- mudar `model.provider` do Hermes;
- selecionar modelo remoto;
- executar tool calling;
- remover Ollama;
- executar TASK-0013;
- instalar Watchtower.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Registrar o FreeLLMAPI como:

```text
INSTALADO = SIM
CONFIGURADO COM PROVIDER = NÃO
AUTORIZADO COMO BACKEND DO HERMES = NÃO
```

Fazer commit + push.

## Retorno

```text
TASK-0014: PASS | PARTIAL | BLOCKED | FAILED
Commit: <sha>
FreeLLMAPI: <status>
Host 3001: <status>
Hermes -> FreeLLMAPI: <status>
RAM idle: <valor>
Persistência: <status>
Providers: nenhum
Bloqueio: <nenhum ou curto>
```

## Resultado — 2026-11-02

Status: PASS. FreeLLMAPI v0.16.16 instalado como servico Compose, running/healthy, sem restart loop; imagem pinada. A configuracao passa somente NODE_ENV, PORT, ENCRYPTION_KEY e FREELLMAPI_UPDATE_CHECK; chave local gerada com 64 caracteres sem exposicao. Dados persistidos em D:/Docker/7ps-marketing/data/freellmapi. Windows host: /api/ping e / HTTP 200 em 127.0.0.1:3001. Do container Hermes, freellmapi:3001/api/ping HTTP 200. Apos restart exclusivo do router, health, endpoints e comunicacao interna voltaram a responder e freeapi.db permaneceu presente. Hermes dashboard e Ollama mantiveram HTTP interno 200.

Baseline anterior: container principal 352,3 MiB e CPU 0,95%, sem modelo Ollama carregado. Amostra em repouso apos restart: FreeLLMAPI 65,15 MiB e CPU 0,01%; principal 426,6 MiB e CPU 0,63%; soma aproximada 491,75 MiB. Dados iniciais: 3 arquivos, 2.525.424 bytes (incluindo arquivos SQLite WAL/SHM); tamanho sujeito a variacao. Nenhum provider externo ou chave de provider foi configurado; provider/modelo Hermes nao alterados.

INSTALADO = SIM
CONFIGURADO COM PROVIDER = NÃO
AUTORIZADO COMO BACKEND DO HERMES = NÃO
