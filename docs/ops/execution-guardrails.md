# Guardrails de Execução do Work

Este documento define limites obrigatórios para impedir que uma tarefa técnica simples se transforme em investigação longa, refatoração lateral ou sequência indefinida de tentativas.

As regras foram adaptadas ao padrão já usado no projeto BMB CRM: execução com escopo estrito, fail-closed, timeouts, dry-run/rollback quando aplicável e ausência de fallback silencioso.

## 1. Regra central

```text
Cumprir a TASK pelo caminho mínimo seguro.
Não otimizar o que não foi pedido.
Não ampliar o problema.
Não trocar de estratégia silenciosamente.
Não insistir indefinidamente.
```

O objetivo do Work não é “resolver a qualquer custo”. É executar o contrato da TASK dentro do orçamento autorizado.

---

## 2. Orçamento de execução

Toda TASK técnica deve declarar uma classe:

```text
S — curta: até 5 minutos de execução
M — normal: até 15 minutos de execução
L — longa: acima de 15 minutos, somente com autorização explícita no Chat
```

Na ausência de classe declarada, assumir `M`.

Ao atingir o orçamento sem cumprir os critérios de aceite:

1. parar no ponto seguro;
2. não iniciar uma nova estratégia;
3. registrar `PARTIAL` ou `BLOCKED`;
4. documentar o último estado válido;
5. propor o próximo passo para decisão no Chat.

Não transformar automaticamente uma tarefa M em L.

---

## 3. Orçamento de tentativas

Por falha técnica:

```text
máximo de 2 hipóteses diagnósticas
máximo de 2 tentativas corretivas no total
máximo de 1 repetição do mesmo comando após correção objetiva
```

Se a segunda abordagem razoável falhar, parar.

É proibido entrar em ciclos do tipo:

```text
testa → falha → altera → testa → falha → instala algo → refatora → rebuild → tenta outra arquitetura
```

sem nova autorização.

---

## 4. Ordem obrigatória: caminho mais barato primeiro

Ao encontrar problema, seguir esta ordem:

1. ler o erro real;
2. verificar estado/configuração diretamente relacionada;
3. executar um diagnóstico pequeno e reversível;
4. aplicar a menor correção possível;
5. validar apenas o critério afetado;
6. encerrar.

Antes de qualquer ação mais ampla, perguntar internamente:

```text
Existe uma correção menor que atende o critério de aceite?
```

Se sim, usar a menor.

---

## 5. Gatilhos de STOP/BLOCKED

O Work deve parar antes de executar qualquer uma destas ações se elas não estiverem explicitamente autorizadas na TASK:

- rebuild completo de imagem Docker;
- reinstalação de stack ou runtime;
- instalação de dependência nova;
- upgrade/downgrade de versão;
- mudança de arquitetura;
- troca de framework, banco, provider ou ferramenta;
- alteração ampla de Dockerfile/Compose;
- refatoração não necessária ao critério de aceite;
- migração de dados;
- exclusão de volume;
- reset de ambiente;
- mudança em serviço externo;
- criação de nova conta/projeto/infra;
- fallback para fonte, backup, endpoint ou ambiente diferente;
- tentativa destrutiva;
- investigação que ultrapasse o orçamento da TASK.

Nesses casos:

```text
Status: BLOCKED
Motivo:
O que foi confirmado:
O que falhou:
Ação ampla que seria necessária:
Opções para decisão no Chat:
```

---

## 6. Fail-closed

Quando uma precondição não é satisfeita, falhar fechado.

Não:

- inventar valor;
- criar dado substituto;
- usar outro ambiente “temporariamente”;
- mudar destino;
- ignorar validação;
- reduzir segurança;
- desabilitar proteção apenas para fazer passar.

O comportamento esperado é equivalente ao padrão `STOP` usado nos fluxos BMB.

---

## 7. Sem fallback silencioso

Se a TASK define uma fonte, ferramenta, imagem, arquivo, serviço ou caminho específico e ele falha, o Work não deve escolher outro por conta própria.

Exemplo:

```text
Fonte A falhou → NÃO usar Fonte B automaticamente.
```

Registrar o bloqueio e devolver a decisão ao Chat.

---

## 8. Timeouts em comandos

Sempre que tecnicamente possível, comandos potencialmente bloqueantes devem possuir timeout coerente com a TASK.

Exemplos de operações que merecem timeout:

- chamadas HTTP;
- testes de serviço;
- processos externos;
- consultas;
- waits;
- downloads;
- healthchecks.

Um timeout deve produzir erro tratável e não iniciar automaticamente uma sequência ilimitada de retries.

---

## 9. Rebuilds e downloads pesados

Rebuild, download grande ou recompilação são considerados ações caras.

Só executar quando:

1. a TASK autoriza explicitamente; ou
2. for inevitável para o critério de aceite e ainda estiver dentro do orçamento declarado.

Caso contrário, `BLOCKED`.

Se uma imagem válida já existe, priorizar validar/reutilizar a imagem antes de rebuildar.

---

## 10. Diagnóstico sem “rabbit hole”

O Work não deve fazer investigação exploratória ampla durante execução.

Permitido:

- ler arquivo diretamente relacionado;
- conferir log necessário;
- verificar uma configuração;
- testar uma hipótese objetiva.

Não permitido sem autorização:

- vasculhar todo o repositório sem necessidade;
- pesquisar muitas alternativas;
- refatorar partes adjacentes;
- resolver warnings não relacionados;
- “aproveitar” para modernizar dependências;
- corrigir débitos técnicos fora do escopo.

Warnings não relacionados devem ser registrados como observação e não resolvidos.

---

## 11. Critério de conclusão antecipada

Assim que todos os critérios de aceite da TASK forem satisfeitos:

```text
PARAR.
```

Não executar melhorias adicionais, hardening extra, cleanup opcional ou otimizações não solicitadas.

---

## 12. Resultado mínimo quando bloqueado

Mesmo quando não conclui, o Work deve produzir valor.

Registrar:

- estado inicial confirmado;
- ações realmente executadas;
- hipótese(s) testada(s);
- último erro sanitizado;
- o que NÃO foi alterado;
- rollback realizado, se necessário;
- recomendação de 1 a 3 opções de próximo passo.

Não continuar executando apenas para evitar devolver `BLOCKED`.

---

## 13. Exemplo de contrato de execução

```text
Execution class: M
Hard budget: 15 min
Diagnostic hypotheses: max 2
Corrective attempts: max 2
Rebuild allowed: NO
New dependency allowed: NO
Architecture change allowed: NO
Destructive actions allowed: NO
Fallback source/environment allowed: NO
```

Esse bloco deve aparecer nas TASKs técnicas.

---

## 14. Princípio de eficiência

```text
Primeiro: confirmar.
Depois: diagnosticar minimamente.
Depois: corrigir minimamente.
Depois: validar.
Então: parar.
```

A qualidade da execução é medida por segurança, precisão e aderência ao critério de aceite — não pela quantidade de ações realizadas.
