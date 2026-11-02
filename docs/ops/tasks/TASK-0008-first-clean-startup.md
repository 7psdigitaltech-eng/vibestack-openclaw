# TASK-0008 — Primeiro startup limpo Hermes + Ollama

Status: BLOCKED

## Objetivo

Executar o primeiro startup controlado da stack a partir do checkpoint pós-build restaurado.

Nesta etapa validar somente:

- container principal;
- Hermes gateway;
- dashboard Hermes;
- Ollama.

A API OpenAI-compatible do Hermes na porta 8642 NÃO faz parte deste checkpoint e não deve ser usada como critério de bloqueio.

## Ambiente

Execution target: LOCAL_DESKTOP
Execution surface: Work / Local
Execution class: S
Hard budget: 10 minutos
Diagnostic hypotheses: máximo 2
Corrective attempts: máximo 2
Rebuild allowed: NÃO
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

Confirmar as pré-condições abaixo antes de qualquer startup.

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

## EXECUÇÃO

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

### 7. Validar restart e persistência

Depois que container, Hermes gateway, dashboard e Ollama estiverem estáveis:

1. registrar os arquivos/estado persistente essenciais criados em `data/hermes`;
2. executar um único:
   `docker compose restart openclaw-vibestack`;
3. aguardar estabilização curta;
4. confirmar novamente:
   - container em running/up;
   - Hermes gateway ativo;
   - dashboard 9119 ativo;
   - Ollama 11434 ativo;
5. confirmar que a configuração/estado persistente do Hermes não foi perdido pelo restart.

Não editar configuração para fazer o restart passar.

### 8. Confirmar acessibilidade local do dashboard

O critério arquitetural da Fase 1 exige que o dashboard Hermes seja acessível localmente.

Validar nesta ordem:

1. HTTP interno do container em 9119;
2. port mapping Docker em `127.0.0.1:9119`;
3. tentativa pelo Windows host com ferramenta do próprio host (`curl.exe`, PowerShell ou equivalente).

Se a sessão Work/Local não conseguir testar o localhost do Windows, mas:
- HTTP interno = PASS;
- mapping Docker = PASS;

registrar:

`HOST_ACCESS=UNVERIFIED_BY_WORK`

e NÃO declarar a Fase 1 totalmente concluída. O startup pode ser tecnicamente validado, mas o checkpoint de Fase 1 permanece pendente de confirmação local.

### 9. Confirmar plataformas desabilitadas

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

## POST-CHECK

Revalidar container, Hermes gateway, dashboard, Ollama, restart e persistência após a execução.

## Critérios de aceite

PASS da TASK quando:

- container está estável;
- Hermes gateway está ativo;
- dashboard responde internamente em 9119;
- Ollama responde internamente em 11434;
- mappings 9119 e 11434 estão corretos;
- um restart foi executado e os serviços voltaram;
- o estado/configuração persistente do Hermes sobreviveu ao restart;
- OpenClaw/Evolution/LM Studio continuam desabilitados;
- nenhum segredo foi exposto;
- documentação atualizada;
- commit + push concluídos.

## Documentação

Atualizar:

- esta TASK;
- `docs/ops/current-state.md`.

Registrar o primeiro startup limpo como concluído.

Somente indicar a Fase 1 como concluída se também houver evidência de que o dashboard está acessível localmente pelo host Windows.

Se o Work não conseguir comprovar acesso pelo host, registrar a Fase 1 como `PARTIAL`/pendente de validação local do dashboard, sem iniciar a Fase 2 automaticamente.

Depois da Fase 1 validada, o próximo checkpoint é a configuração/benchmark de modelo local.

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

## Resultado — 2026-11-02

Status: BLOCKED. Pre-condicoes confirmadas: imagem existente, nenhum container do projeto, diretorios Hermes/Ollama vazios, segredos presentes e flags esperadas. Stack iniciada sem rebuild; container em execucao, zero reinicios observados, gateway Hermes ativo. Ollama: listener 11434 e HTTP interno 200. Dashboard: bridge 9119 aberta, mas HTTP interno encerra a conexao sem resposta; servidor interno 9120 fechado. Mapeamentos 9119 e 11434 em loopback confirmados. Nenhum indicador de falha fatal foi identificado na consulta sanitizada. Como dashboard nao estabilizou, o restart e o teste de persistencia previstos apos a estabilizacao nao foram executados; acesso host nao verificado. OpenClaw, Evolution e LM Studio desabilitados na configuracao. Nenhum segredo exibido; Fase 1 pendente. Proximo passo: diagnosticar a inicializacao do dashboard em tarefa autorizada.
