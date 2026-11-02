---
name: 7ps-reels-editor
description: Produza e edite Reels verticais para a 7PS Digital Tech usando roteiro, gravação ou avatar autorizado, cortes, motion graphics, legendas sincronizadas e revisão humana. Use quando houver pedido de Reel, vídeo institucional, edição de vídeo, criativo de social media ou teste de produção agentic da 7PS. Não publica nem cria anúncios automaticamente.
---

# 7PS Reels Editor

## Objetivo

Transformar um briefing corporativo aprovado ou experimental em um pacote de Reel reproduzível e auditável, sem presumir nicho, oferta, case, ROI ou resultado comercial não verificados.

Esta skill foi escrita para **Codex/edição assistida**. O projeto de agentes Hermes usa skills em outro contexto operacional; a integração ao Hermes exige testes próprios de descoberta/invocação, permissões e persistência. Não declarar que ela já funciona no Telegram.

## Fontes de verdade e limites

1. Antes de criar, consulte o briefing e a política/decisão vigente em `7psdigitaltech-eng/7ps-documenta-o`; branch de pesquisa: `research/reposicionamento-multissetorial-2026-10-31`.
2. O Instagram observado em 01/11/2026 é `@7ps.digital`; perfil/nome/categoria estão sujeitos a revisão. A identidade visual usada no primeiro piloto é **provisória**, não substitui arquivo de marca aprovado.
3. Não citar Santa Luzia, BMB, seus dados, marcas, trechos de telas, resultados ou códigos como cases sem autorização separada.
4. Não inventar números, resultados, funcionalidades em produção, clientes, contratos ou preços.
5. Não imitar visual, voz, rosto nem vídeos de outros criadores. Referências de estilo só permitem técnicas genéricas de edição; respeitar direitos autorais.
6. Criar avatares/clones somente do próprio apresentador ou de pessoa que tenha consentido explicitamente e completado a verificação exigida pelo serviço. Não use imagem/áudio de terceiro de rede social como fonte de clonagem.
7. Não publicar, agendar, comentar, enviar DM, configurar anúncios, usar API com escrita nem assumir orçamento sem autorização específica da Central e credenciais/permissões apropriadas.

## Entrada obrigatória

Use `references/BRIEFING_TEMPLATE.md`; se faltarem gravações ou avatar, ofereça um **motion Reel sem rosto**, como primeiro protótipo. Sempre rotule vídeos silenciosos como silenciosos, e roteiro de narração como não gravado. Se faltar consentimento de clone, não faça clone; prossiga com gravação real ou vídeo faceless.

## Fluxo de edição

1. **Inventário:** inspecione diretório de trabalho, gravações, dimensões, fps, áudio e direitos dos assets. Use `ffprobe` para áudio/vídeo real.
2. **Roteiro e decupagem:** entregue gancho (primeiros 2–3 s), problema, explicação, aplicação e CTA; cada cena precisa de texto, áudio, duração e visual.
3. **Revisão factual:** consulte documentos da 7PS e marque `N/D` ou `hipótese` para afirmações sem prova. Evite garantias absolutas.
4. **Selecione estilo:** (A) talking head com gravação original; (B) avatar autorizado com roteiro e áudio; (C) motion infographic. Não confunda Codex/Claude Code (edição por código) com avatar/clone (serviço separado).
5. **Produção:** prefira projeto reproduzível Remotion/React para motion e edição de longo prazo; FFmpeg/Python para protótipos rápidos ou manipulação determinística. Guarde os fontes, não só o MP4.
6. **Legendagem:** somente apresente legendas *sincronizadas* após derivar timecodes de áudio real. Se o vídeo não tiver áudio, trate textos como lettering, nunca como legenda sincronizada.
7. **Prévia:** renderize amostra e obtenha revisão humana; faça ajustes por cena, sem refazer o trabalho aprovado.
8. **Exportação:** video vertical 1080×1920 (9:16), H.264 MP4, 24/30 fps, yuv420p, `+faststart`; AAC quando houver áudio. Verifique duração, stream, tamanho e legibilidade em celular.
9. **Entrega:** MP4; thumbnail; roteiro/narração; cena-a-cena; legenda do post; arquivos de projeto; lista de materiais/direitos; custos externos (se houver); checklist e estado de aprovação.
10. **Publicação:** `PENDENTE_APROVACAO` é status padrão. Só executar qualquer postagem externa se houver integração testada, acesso autorizado e aprovação específica.

## Padrão visual inicial experimental

- Paleta de referência (não oficial): fundo `#06121C`, turquesa `#48DDD6`, branco `#F5FBFD`, apoio `#BACCD5`.
- Estética: escura, tecnológica, corporativa, ritmo firme sem edição excessivamente frenética.
- Keep text inside safe margins: 6–8% laterais; reservar áreas superiores/inferiores para elementos sobrepostos do Instagram. Validar no app.
- Prefira cenas que **expliquem** algo: fluxo de atendimento, integração entre sistemas, diagnóstico antes da automação; evite B-roll decorativo sem significado.

## Handoff com os sete agentes da 7PS Marketing AI

- **Diretor:** recebe pedido, registra aprovação e controla publicação.
- **Estrategista:** hipótese/objetivo e claims permitidos.
- **Conteúdo:** calendário e storyboard.
- **Copywriter:** roteiro, legenda e CTA.
- **Criativo:** geração/edição, projeto e arquivos.
- **Analista:** métricas reais depois da publicação autorizada.
- **Gestor de Tráfego:** apenas planejamento; anúncios pagos bloqueados no estágio atual.

O piloto deve poder funcionar **manualmente antes de o Hermes estar operacional**. O registro do fork de 10/09/2026 não prova implementação funcional. A presença de Meta Ads MCP não implica Instagram Content Publishing API.

## Gate de qualidade

Execute `references/QUALITY_GATE.md` antes de marcar `PRONTO_PARA_REVISAO`.
