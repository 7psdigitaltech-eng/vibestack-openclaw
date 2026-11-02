# Modelo Operacional — Chat + GitHub + Work

Este documento define o fluxo oficial de planejamento, execução e registro do projeto **7Ps Marketing AI**.

O objetivo é separar governança de execução, manter o GitHub como fonte de verdade e reduzir a necessidade de interação manual no PowerShell, IDE ou terminal local.

## 1. Princípio central

```text
Chat   = decisão, arquitetura, priorização e aprovação
GitHub = fonte de verdade, instruções, estado, evidências e histórico
Work   = execução técnica no ambiente local/repositório
```

O Chat não deve depender de logs extensos copiados manualmente do terminal quando o Work puder executar e registrar o resultado no repositório.

O Work não deve tomar decisões arquiteturais relevantes sem que elas tenham sido definidas ou autorizadas no Chat.

---

## 2. Fluxo oficial

### Etapa A — Planejamento no Chat

No Chat são definidos:

- objetivo;
- contexto;
- decisões arquiteturais;
- alternativas avaliadas;
- riscos;
- critérios de aceite;
- limites de escopo;
- autorização para execução.

Quando a decisão estiver madura, ela deve ser registrada no GitHub antes da execução.

### Etapa B — Pacote de execução no GitHub

Cada execução técnica recebe um identificador único:

```text
TASK-0001
TASK-0002
TASK-0003
...
```

O pacote deve ser salvo em:

```text
docs/ops/tasks/
```

Formato recomendado:

```text
docs/ops/tasks/TASK-0001-<slug>.md
```

Cada tarefa deve conter:

1. objetivo;
2. contexto e decisões já aprovadas;
3. estado inicial conhecido;
4. pré-requisitos;
5. arquivos/serviços envolvidos;
6. instruções de execução;
7. comandos permitidos ou esperados;
8. restrições;
9. política de segredos;
10. critérios de aceite;
11. validações pós-execução;
12. rollback, quando aplicável;
13. seção de resultado a ser preenchida pelo Work.

### Etapa C — Execução no Work

O prompt enviado ao Work deve ser curto.

Modelo:

```text
Execute a tarefa TASK-0001 documentada no repositório
7psdigitaltech-eng/vibestack-openclaw.

Leia e siga integralmente:
docs/ops/tasks/TASK-0001-<slug>.md

Execute as ações necessárias no ambiente local, valide os critérios de aceite,
registre no próprio arquivo o resultado, evidências relevantes, desvios e arquivos
alterados, e faça o commit quando a tarefa exigir mudança versionada.

Não altere decisões de arquitetura. Se houver bloqueio que exija nova decisão,
registre como BLOCKED e encerre a execução.
```

O Work deve usar o documento da tarefa como contrato de execução.

### Etapa D — Registro do resultado

Ao concluir, o Work deve atualizar a seção de resultado da própria tarefa com:

```text
Status: PASS | PARTIAL | BLOCKED | FAILED
Data:
Ambiente:
Commit:
Arquivos alterados:
Validações executadas:
Resultado:
Desvios:
Pendências:
Próximo passo recomendado:
```

Logs extensos devem ser resumidos. Evidências úteis podem ser armazenadas em arquivo separado quando necessário.

### Etapa E — Retorno ao Chat

O retorno do Work para o Chat deve ser curto.

Modelo:

```text
TASK-0001: PASS
Commit: <sha>
Critérios de aceite: aprovados
Desvios: nenhum
Detalhes e evidências registrados no GitHub.
```

O Chat consulta o GitHub, interpreta o resultado e decide o próximo passo.

---

## 3. Estrutura recomendada

```text
docs/
├── README.md
├── 7ps-marketing-ai-v1-architecture.md
├── 7ps-marketing-ai-v1-agents.md
├── 7ps-marketing-ai-v1-roadmap.md
├── 7ps-marketing-ai-operating-model.md
└── ops/
    ├── current-state.md
    ├── decisions/
    │   └── ADR-XXXX-<slug>.md
    └── tasks/
        ├── TASK-0001-<slug>.md
        ├── TASK-0002-<slug>.md
        └── ...
```

### current-state.md

Mantém o estado técnico conhecido do ambiente:

- versão do Docker;
- recursos WSL;
- localização dos dados;
- imagem construída;
- serviços ativos;
- integrações configuradas;
- integrações instaladas mas não autorizadas;
- último checkpoint concluído.

Isso reduz dependência de memória de conversa.

### ADRs

Usar ADR apenas para decisões relevantes e duráveis, por exemplo:

- Hermes em vez de OpenClaw;
- Ollama como backend local;
- Telegram como canal V1;
- Docker Desktop como host;
- não usar Evolution na V1.

Não criar ADR para decisões pequenas ou operacionais.

---

## 4. Regras para execução

### Ambiente de execução obrigatório

Toda TASK técnica deve declarar o alvo de execução e seguir:

```text
docs/ops/execution-environments.md
```

Para `LOCAL_DESKTOP`, um preflight de no máximo 2 minutos deve comprovar acesso ao repositório local, Git, Docker e Docker Compose antes de iniciar o orçamento da TASK. Se o executor não tiver acesso real ao host, registrar `BLOCKED` imediatamente, sem tentar contornar com cloud, outro clone ou outro Docker.

### Guardrails obrigatórios do Work

Toda TASK técnica deve seguir também:

```text
docs/ops/execution-guardrails.md
```

Esses guardrails definem orçamento de execução, limite de tentativas, caminho mínimo, fail-closed, proibição de fallback silencioso e gatilhos de STOP/BLOCKED.

Cada TASK técnica deve declarar explicitamente:

```text
Execution class: S | M | L
Hard budget:
Diagnostic hypotheses:
Corrective attempts:
Rebuild allowed:
New dependency allowed:
Architecture change allowed:
Destructive actions allowed:
Fallback source/environment allowed:
```

Na ausência de classificação, assumir classe M e aplicar os limites padrão do documento de guardrails.


### Uma tarefa por unidade lógica

Evitar prompts do tipo “configure tudo”.

Preferir:

```text
TASK-0004 — subir stack Hermes-only
TASK-0005 — validar Ollama
TASK-0006 — selecionar modelo local
TASK-0007 — configurar Telegram
```

Cada tarefa deve terminar em um estado verificável.

### Pré-check e pós-check obrigatórios

Toda tarefa técnica deve declarar:

```text
PRE-CHECK
EXECUÇÃO
POST-CHECK
```

Isso torna a execução reproduzível e facilita diagnóstico.

### Idempotência

Sempre que possível, comandos devem poder ser executados novamente sem destruir estado válido.

### Rollback

Mudanças de infraestrutura, configuração ou código com risco relevante devem incluir instrução de reversão.

---

## 5. Git e branches

Para mudanças simples de documentação, pode-se trabalhar diretamente na branch adotada pelo projeto.

Para mudanças em:

- Dockerfile;
- docker-compose.yml;
- entrypoint;
- scripts;
- código dos agentes;
- integrações;
- automações;

preferir:

```text
task/TASK-XXXX-<slug>
```

e, quando fizer sentido, revisão via PR antes de incorporar à `main`.

O arquivo da tarefa deve registrar branch e commit final.

---

## 6. Política de segredos

Segredos nunca devem ser:

- enviados ao Chat;
- incluídos em resposta do Work;
- gravados em Markdown;
- commitados no Git;
- incluídos em screenshots ou logs versionados.

Exemplos:

```text
.env
tokens
API keys
OAuth secrets
passwords
private keys
```

Validações devem mostrar somente estado ou metadados, por exemplo:

```text
HERMES_API_SERVER_KEY: SET
HERMES_API_SERVER_KEY_LENGTH: 64
```

e nunca o valor.

Caso um segredo seja exposto em Chat, log ou commit, ele deve ser considerado comprometido e rotacionado.

---

## 7. Evidências

Guardar somente evidências que ajudem a reproduzir ou auditar o trabalho.

Boas evidências:

- versão da ferramenta;
- serviço healthy;
- saída sanitizada de teste;
- arquivo alterado;
- hash do commit;
- porta respondendo;
- teste funcional.

Evitar:

- logs gigantes;
- dumps completos do ambiente;
- credenciais;
- dados pessoais;
- saídas sem relação com o critério de aceite.

---

## 8. Status padrão

Usar apenas:

```text
PLANNED
READY
RUNNING
PASS
PARTIAL
BLOCKED
FAILED
SUPERSEDED
```

`READY` significa que a tarefa já possui instrução completa no GitHub e pode ser enviada ao Work.

`BLOCKED` significa que o Work encontrou uma decisão que deve voltar ao Chat.

---

## 9. Regra de autoridade

```text
Chat decide.
GitHub registra.
Work executa.
GitHub comprova.
Chat avalia e decide o próximo passo.
```

O Work pode resolver detalhes técnicos locais necessários para cumprir uma tarefa, desde que não alterem arquitetura, escopo, segurança ou objetivo aprovado.

Quando uma execução exigir nova decisão relevante, o Work deve parar no ponto seguro, registrar o bloqueio e devolver o controle ao Chat.

---

## 10. Aplicação imediata ao estado atual

O build inicial da stack já foi concluído com:

```text
INSTALL_OPENCLAW=false
INSTALL_HERMES=true
INSTALL_OLLAMA=true
INSTALL_LMSTUDIO=false
INSTALL_EVOLUTION=false
```

A persistência efetiva do Compose foi validada em:

```text
D:/Docker/7ps-marketing/data/openclaw
D:/Docker/7ps-marketing/data/ollama
D:/Docker/7ps-marketing/data/hermes
D:/Docker/7ps-marketing/data/higgsfield
D:/Docker/7ps-marketing/data/lmstudio
```

O próximo trabalho operacional deve seguir este novo protocolo, começando por uma tarefa formal para:

1. rotacionar o segredo do Hermes exposto durante a instalação;
2. subir a stack;
3. validar container, Hermes Gateway, dashboard e Ollama;
4. registrar o estado pós-startup sem expor segredos.
