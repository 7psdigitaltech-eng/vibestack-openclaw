# Estado Atual — 7Ps Marketing AI

Última atualização: 2026-11-02

## Ambiente local

- Windows 10
- Docker Desktop 4.90.0
- Docker Engine 29.7.2
- Docker Compose v5.5.1
- Backend: WSL2
- CPUs disponíveis ao Docker: 8
- Memória disponível ao Docker: ~9.7 GiB
- WSL configurado com:
  - memory=10GB
  - processors=8
  - swap=6GB
  - autoMemoryReclaim=gradual

## Diretório do projeto

```text
D:\Docker\7ps-marketing\repo
```

## Persistência

```text
D:\Docker\7ps-marketing\data\
├── openclaw
├── ollama
├── hermes
├── higgsfield
├── lmstudio
├── knowledge
├── artifacts
└── backups
```

Mounts efetivos validados pelo Docker Compose:

```text
D:/Docker/7ps-marketing/data/openclaw   -> /root/.openclaw
D:/Docker/7ps-marketing/data/ollama     -> /var/lib/ollama
D:/Docker/7ps-marketing/data/lmstudio   -> /root/.lmstudio/models
D:/Docker/7ps-marketing/data/hermes     -> /root/.hermes
D:/Docker/7ps-marketing/data/higgsfield -> /root/.higgsfield
```

## Build

Imagem construída com sucesso:

```text
vibestack-openclaw:latest
```

Configuração:

```text
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
INSTALL_EVOLUTION=false
COMPOSE_PROFILES=
```

## Estado operacional

- imagem: construída;
- container principal: ainda não iniciado após o build;
- Hermes: instalado, ainda não validado em runtime;
- Ollama: instalado, ainda não validado em runtime;
- OpenClaw: não instalado na imagem atual;
- LM Studio: não instalado;
- Evolution/WhatsApp: desabilitado;
- Meta Ads: ferramenta instalada, sem credenciais;
- Google Ads: ferramenta instalada, sem credenciais;
- Higgsfield: CLI instalada, autenticação ainda não realizada;
- AtlasCloud: ferramenta instalada, sem API key;
- Telegram: ainda não configurado.

## Segurança

Durante a instalação inicial, segredos gerados automaticamente foram exibidos em saída compartilhada no Chat.

Regra: considerar esses valores comprometidos e rotacioná-los antes de operar a stack.

Nenhum valor secreto deve ser registrado neste arquivo.

## Bloqueio operacional atual

A primeira tentativa de executar a `TASK-0001` via Work foi bloqueada antes da execução técnica: o ambiente do Work utilizado não possuía acesso comprovado ao repositório local em `D:\Docker\7ps-marketing\repo` nem permissão para operar o Docker Desktop.

Nenhuma alteração local foi realizada por essa tentativa.

## Próximo checkpoint

Validar um mecanismo de execução local autorizado (`LOCAL_DESKTOP`) antes de reabrir a `TASK-0001`.
