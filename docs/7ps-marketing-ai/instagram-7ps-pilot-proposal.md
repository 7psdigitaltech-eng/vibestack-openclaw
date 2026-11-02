# Piloto 7Ps Marketing AI — Produção de Conteúdo Orgânico para Instagram

**Data:** 2026-11-01  
**Status:** PROPOSTA DE CENÁRIO OPERACIONAL — ainda não implementado, aprovado para publicação nem testado.  
**Aplicação:** marketing institucional da própria 7Ps Digital Tech; uso interno da V1.  
**Perfil público observado:** `@7ps.digital` (não confundir com conta/ID fictício).  
**Governança:** frente 04 Marketing e Comercial + frente 00 Central. A fonte das decisões de posicionamento permanece na documentação corporativa, não neste fork.

## Objetivo
Usar um trabalho real da 7PS — produzir e revisar conteúdo orgânico para o Instagram — como primeiro experimento ponta a ponta da arquitetura agentic Hermes. A V1 não precisa publicar automaticamente: deve **entregar um pacote editorial consistente e rastreável, pronto para revisão humana**.

## Contexto de posicionamento
Em novembro/2026, a 7PS investiga atuar de forma multissetorial com tecnologia, sistemas, integrações, automações e IA aplicados a processos empresariais. Nichos, ofertas, preços, cases e claims **não estão aprovados**. O Instagram existente utiliza categoria `Agência de marketing`; a mudança pública de narrativa depende de aprovação da Central. Não usar dados de Santa Luzia/BMB como cases ou prova social.

## Fluxo de tarefas — piloto
1. **Diretor (Telegram):** recebe `Prepare um carrossel educativo para o Instagram da 7PS: diagnóstico antes de automação`; verifica que ação solicitada é rascunho, não publicação; cria projeto e dependências no Kanban.
2. **Estrategista:** consulta fonte oficial do posicionamento (incluindo status/pendências), define promessa segura, público e critérios de aceite; bloqueia claim sem evidência.
3. **Conteúdo:** gera pauta, distribuição por slides, calendário e CTA consultivo.
4. **Copywriter:** redige os slides, legenda, variações de abertura e hashtags; sinaliza informações que requerem confirmação.
5. **Criativo:** recebe copy/briefing e gera direção visual e arquivos no formato adequado; usa ferramentas específicas apenas quando instaladas/configuradas/autorizadas e com custos conhecidos.
6. **Diretor:** consolida um pacote com briefs, legendas, assets, status, fontes e limites; devolve pelo Telegram para aprovação humana; qualquer lacuna vira `blocked`.
7. **Humano:** revisa texto, identidade, marca e uso de imagem; aprova ou pede ajuste. Inicialmente publica manualmente, se houver autorização da Central. Não automatizar postagem, DMs ou comentários.
8. **Analista:** utiliza métricas manuais/exports de Insights após publicação autorizada, sem inferir conversões não rastreadas; recomenda melhorias junto ao Estrategista.

## Primeiro briefing-piloto
- **Título:** `Automação não começa pela ferramenta.`
- **Objetivo:** apresentar o princípio de diagnóstico de processos sem prometer ganho financeiro.
- **Público:** gestores de empresas com rotinas operacionais e comerciais, sem escolher vertical.
- **Formato:** carrossel de 6–7 slides; apoio opcional em roteiro curto de Reel.
- **Linguagem:** B2B, simples, orientada a processo; não presumir dor de empresa específica.
- **CTA:** `Qual tarefa da sua operação ainda exige retrabalho?`
- **Proibido:** clientes nomeados, números de resultado sem comprovação, preços/serviços não aprovados, alegação de ferramenta pronta, marca de terceiros sem permissão.
- **Entregáveis:** `brief.md`, `copy.md`, `creative-brief.md`, `assets/`, `review-checklist.md`, `metrics-template.md`, trilha Kanban, fontes e status.

## Critérios de aceite técnico-operacional
- Build/containers funcionando **não** equivalem a fluxo validado.
- Especialistas conseguem consultar fatos oficiais e diferenciar hipótese de decisão.
- Copy e Criativo produzem materiais coerentes sem inventar resultado ou case.
- Kanban preserva dependências e estado `blocked`, `review`, `done`.
- Diretor apresenta material final ao humano e solicita revisão.
- Nenhuma chamada de publicação orgânica, DM, comentário ou Ads e nenhum gasto ocorre durante o piloto de rascunhos.
- Tempo, número de refações, falhas de contexto e custo real são medidos.
- As ferramentas são classificadas separadamente em `instaladas`, `configuradas`, `autorizadas` e `testadas`.

## Lacuna técnica: Instagram orgânico vs Meta Ads
O fork herdou `middleware/meta_ads_cli_mcp.py`, relacionado à gestão de anúncios Meta Ads. Isso **não comprova postagem orgânica nem leitura de Insights de Instagram**. A inclusão de integração Instagram Content Publishing/Insights exige nova avaliação de permissões, conta profissional, autenticação, hospedagem temporária segura de mídia, limites, testes, idempotência, logs e aprovação humana para publicar. Até lá, postagem será manual e analytics por export autorizado.

## Sequência proposta
- **Etapa A:** organizar briefing, knowledge e checklist de revisão; produção de uma peça manual em paralelo à implementação da stack.
- **Etapa B:** quando Hermes + Telegram + Knowledge + profiles + Kanban estiverem prontos, executar produção do mesmo conteúdo pela cadeia de agentes e comparar qualidade, tempo e retrabalho.
- **Etapa C:** após teste de rotina e aprovação separada, avaliar conexão read-only de métricas; não antecipar integração de publicação ou campanhas.

**Não altera** as ADRs existentes (Hermes, Ollama, Telegram, V1 monoempresa, Meta Ads write bloqueado), nem habilita APIs ou credenciais.


## Registro de produção — 01/11/2026
A frente 04 preparou manualmente um primeiro carrossel com sete imagens PNG 1080×1350, legenda e proposta de bio para `@7ps.digital`. **Esse material foi gerado fora do runtime Hermes** e não demonstra implantação nem capacidade dos agentes. Foi entregue como pacote para revisão na conversa de Marketing e Comercial; os arquivos binários não estão versionados no fork.

O roteiro, o conceito visual, o checklist de publicação e as premissas podem ser consultados no documento corporativo: https://github.com/7psdigitaltech-eng/7ps-documenta-o/blob/research/reposicionamento-multissetorial-2026-10-31/docs/04-marketing/2026-11-01-piloto-ig01-carrossel-e-aprovacao.md

**Próximo teste técnico real:** repetir a produção desse mesmo conteúdo via Diretor → Estrategista → Conteúdo → Copywriter → Criativo, comparando qualidade, tempo e revisões. Não cadastrar resultados fictícios e não publicar automaticamente.
