# Guardrails de Implementação — 7Ps Marketing AI V1

## 1. Objetivo

Evitar que a implementação técnica se desvie das decisões aprovadas ou libere autonomia antes da validação adequada.

## 2. Regras obrigatórias

### 2.1 Não introduzir Docker-in-Docker

A stack deve subir diretamente no Docker Desktop. O container `debian-vps` não é dependência da aplicação.

### 2.2 Não armazenar segredos no Git

Nunca versionar:

- tokens Telegram;
- API keys;
- credenciais Meta/Google;
- senhas;
- cookies/sessões;
- arquivos `.env` reais.

Somente `.env.example` com placeholders.

### 2.3 Não habilitar write em Ads por acidente

Enquanto a fase Meta write não for aprovada:

- credenciais podem permanecer vazias;
- toolsets de write devem estar indisponíveis ou bloqueados;
- qualquer tentativa deve falhar de forma segura.

### 2.4 Não misturar fatos oficiais com memória

Informação oficial da empresa deve residir na knowledge base ou em fonte explicitamente aprovada.

### 2.5 Não dar todas as tools a todos os profiles

Aplicar menor privilégio possível.

Exemplos:

- Analista: Ads read, sem write;
- Criativo: media tools, sem Ads write;
- Gestor: futuro Ads write;
- Diretor: orquestração, sem execução direta de Ads.

### 2.6 Não aumentar concorrência antes de benchmark

Subagents simultâneos começam em 1–2.

### 2.7 Não criar multi-clínica prematuramente

Nenhuma abstração multi-tenant deve ser introduzida apenas por possibilidade futura, salvo quando necessária para evitar bloqueio arquitetural concreto.

### 2.8 Não remover ferramenta especializada apenas por não estar configurada

Antes de remover Higgsfield, AtlasCloud, Meta/Google ou Media Editor, avaliar:

- valor para a função do agente;
- custo;
- alternativa local;
- impacto na qualidade;
- necessidade futura.

### 2.9 Não tratar `Running` como validação funcional

Cada fase precisa de teste de comportamento e critérios de aceite.

## 3. Princípio de autonomia

Autonomia cresce por níveis:

### Nível 0 — leitura e planejamento

Permitido automaticamente.

### Nível 1 — produção de rascunhos/ativos locais

Permitido, mantendo outputs rastreáveis.

### Nível 2 — ações externas reversíveis

Somente após integração e teste.

### Nível 3 — ações com impacto financeiro/reputacional

Exigem alçada e/ou aprovação humana explícita.

## 4. Princípio de falha segura

Quando faltar:

- credencial;
- contexto;
- autorização;
- informação oficial;
- capacidade de ferramenta;

...o sistema deve bloquear, registrar e pedir ação/decisão em vez de inventar ou executar por aproximação.

## 5. Revisão antes de cada fase

Antes de iniciar uma fase do roadmap, confirmar:

1. fase anterior aprovada;
2. dados persistentes protegidos;
3. rollback/retorno conhecido;
4. segredos fora do Git;
5. critério de aceite definido;
6. nenhuma dependência externa desnecessária ativada.
