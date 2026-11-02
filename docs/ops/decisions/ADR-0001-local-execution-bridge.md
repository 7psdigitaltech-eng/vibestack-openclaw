# ADR-0001 — Ponte de execução local para TASKs LOCAL_DESKTOP

Status: SUPERSEDED
Data: 2026-11-02

## Contexto

O fluxo Chat → GitHub → Work foi validado para planejamento/documentação, mas a primeira TASK operacional foi bloqueada porque a sessão do Work utilizada não possuía acesso ao clone local em `D:\Docker\7ps-marketing\repo` nem permissão para operar o Docker Desktop.

O projeto precisa de um caminho previsível para executar comandos no Windows local sem exigir interação manual recorrente no PowerShell.

O fork `7psdigitaltech-eng/vibestack-openclaw` é público. Portanto, não é recomendado conectar diretamente a ele um runner self-hosted com acesso amplo ao computador local.

## Opções

### Opção A — Work com acesso LOCAL_DESKTOP

Executar TASKs locais somente numa superfície/sessão do Work que comprove no preflight:

- acesso ao filesystem local;
- acesso ao clone do repo;
- Git;
- Docker Desktop;
- Docker Compose.

Vantagens:

- menor infraestrutura;
- experiência interativa;
- não exige criar executor adicional.

Limitações:

- disponibilidade/permissões podem variar por superfície/sessão;
- o preflight pode bloquear novamente;
- menos determinístico para automação recorrente.

### Opção B — GitHub Actions self-hosted runner em repositório privado de operações

Criar um repositório privado dedicado, por exemplo:

```text
7psdigitaltech-eng/7ps-local-ops
```

Registrar nele um self-hosted runner Windows dedicado/rotulado, executado como serviço local.

O repositório privado armazenaria somente workflows operacionais controlados. Esses workflows poderiam:

1. localizar o clone real em `D:\Docker\7ps-marketing\repo`;
2. sincronizar/validar a revisão autorizada;
3. executar scripts operacionais versionados;
4. operar Docker Desktop;
5. registrar apenas saída sanitizada;
6. terminar por timeout;
7. nunca publicar `.env` ou secrets.

Vantagens:

- execução local determinística;
- sem necessidade de PowerShell manual a cada TASK;
- timeouts e gates nativos do GitHub Actions;
- histórico de execução;
- Work/Chat pode continuar operando no GitHub, sem precisar controlar diretamente o PC.

Riscos/controles:

- self-hosted runner executa código com permissões do usuário/serviço local;
- não registrar o runner no fork público;
- usar repositório privado dedicado;
- workflows somente manuais/controlados;
- labels dedicadas;
- branch protegida;
- nenhuma execução automática de PR externo;
- menor privilégio possível;
- não imprimir secrets;
- limitar cada workflow por `timeout-minutes`;
- desligar/desregistrar runner quando não necessário, se o modelo operacional exigir.

Custo operacional:

- requer bootstrap inicial no Windows;
- depois reduz bastante a interação manual.

## Decisão atualizada

A execução em **Work/Local** foi testada pelo usuário e conseguiu operar o ambiente local, ao contrário da tentativa em Work/Cloud.

Portanto, a decisão operacional atual é usar **Opção A — Work/Local + preflight** para TASKs `LOCAL_DESKTOP`.

A Opção B (self-hosted runner privado) fica apenas como alternativa futura caso Work/Local se mostre inconsistente, indisponível ou insuficiente para automação recorrente.

## Não autorizado por este ADR

Este documento é apenas uma proposta.

Não criar repositório privado, instalar runner, alterar permissões do Windows ou registrar serviço sem aprovação explícita no Chat.

## Próximo passo

Após aprovação:

1. criar o repositório privado de operações;
2. documentar threat model mínimo;
3. criar TASK de bootstrap do runner;
4. fazer instalação única e controlada;
5. validar com workflow de preflight não destrutivo;
6. somente depois reabrir TASK-0001.
