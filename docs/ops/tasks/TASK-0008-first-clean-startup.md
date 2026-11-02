# TASK-0008 — Primeiro startup limpo Hermes + Ollama

Status: READY

## Objetivo

Executar o primeiro startup controlado da stack a partir do checkpoint pós-build restaurado.

Nesta etapa validar somente:

- container principal;
- Hermes gateway;
- dashboard Hermes;
- Ollama.

A API OpenAI-compatible do Hermes na porta 8642 NÃO faz parte deste checkpoint e não deve ser usada como critério de bloqueio.

## Ambiente

Work / Local

Repo:

```text
D:\Docker\7ps-marketing\repo
```

Sincronizar com `origin/main` por fast-forward seguro.

## Pré-condições esperadas

- imagem `vibestack-openclaw:latest` existente;
- nenhum container atual do projeto;
- `data/hermes` vazio;
- `data/ollama` vazio;
- segredos rotacionados presentes;
- `INSTALL_OPENCLAW=false`;
- `INSTALL_HERMES=true`;
- `INSTALL_OLLAMA=true`;
- `INSTALL_LMSTUDIO=false`;
- `INSTALL_EVOLUTION=false`;
- `COMPOSE_PROFILES=`.

Se essas condições divergirem materialmente, retornar BLOCKED sem corrigir arquitetura.

## Execução

### 1. Subir usando a imagem já construída

Executar:

```bash
docker compose up -d --no-build
```

Não fazer build/rebuild.

### 2. Aguardar estabilização curta

Aguardar apenas o necessário para os serviços iniciarem, sem ultrapassar o orçamento da TASK.

### 3. Validar container

Confirmar:

- container do serviço `openclaw-vibestack` em running/up;
- sem restart loop;
- imagem usada = `vibestack-openclaw:latest`.

### 4. Validar Hermes gateway

Dentro do container, confirmar:

- processo `hermes gateway run` ativo;
- comando/status do gateway, se disponível, indica execução normal;
- nenhum erro fatal de startup nos logs sanitizados.

Não exigir listener na porta 8642.

### 5. Validar dashboard Hermes

Dentro do container, confirmar:

- listener/bridge da porta 9119 ativo;
- HTTP local do container para `http://127.0.0.1:9119` responde.

Confirmar também que o Docker publica 9119 em loopback do host.

O shell isolado do Work não precisa conseguir abrir o localhost do Windows para esta TASK passar.

### 6. Validar Ollama

Dentro do container, confirmar:

- processo/listener Ollama ativo na porta 11434;
- endpoint local do Ollama responde;
- ausência de modelos é aceitável nesta etapa.

Confirmar também publicação da 11434 em loopback do host.

### 7. Confirmar plataformas desabilitadas

Confirmar sem alterar:

- OpenClaw desabilitado;
- Evolution desabilitado;
- LM Studio desabilitado.

## Fora de escopo

Não:

- habilitar API Hermes 8642;
- configurar provider/model do Hermes;
- puxar modelo Ollama;
- configurar Telegram;
- configurar Meta/Google Ads;
- configurar Higgsfield;
- editar entrypoint;
- editar docker-compose;
- rebuildar imagem;
- instalar dependências.

## Critérios de aceite

PASS quando:

- container está estável;
- Hermes gateway está ativo;
- dashboard responde internamente em 9119;
- Ollama responde internamente em 11434;
- mappings 9119 e 11434 estão corretos;
- OpenClaw/Evolution/LM Studio continuam desabilitados;
- nenhum segredo foi exposto;
- documentação atualizada;
- commit + push concluídos.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Registrar o primeiro startup limpo como concluído e indicar o próximo checkpoint: configuração/benchmark de modelo local.

## Limites

- até 10 minutos;
- máximo 2 tentativas corretivas;
- sem rebuild;
- sem instalação;
- sem mudança arquitetural.

## Retorno

```text
TASK-0008: PASS | BLOCKED | FAILED
Commit: <sha>
Container: <status>
Hermes gateway: <status>
Dashboard 9119: <status>
Ollama 11434: <status>
Desvios: <curto>
Bloqueio: <nenhum ou curto>
```
