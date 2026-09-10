# Status Atual — 7Ps Marketing AI

**Data de referência:** 2026-09-10

## Estado

Planejamento arquitetural concluído e documentado. A implementação funcional específica da 7Ps ainda não foi iniciada.

## Ambiente local já preparado

- Windows 10.
- Docker Desktop operacional sobre WSL2.
- Imagem Debian 12 Bookworm testada.
- Container `debian-vps` criado como laboratório Linux genérico.
- Persistência do laboratório apontada para `D:\Docker\debian-vps`.

### Decisão posterior sobre o `debian-vps`

O `debian-vps` **não será o host pai** da 7Ps Marketing AI.

Ele pode permanecer como laboratório Linux para testes, scripts e experimentação.

A agência será executada como stack própria diretamente no Docker Desktop, evitando Docker-in-Docker.

## Repositório

Fork de trabalho:

`7psdigitaltech-eng/vibestack-openclaw`

Origem/upstream analisado:

`ericorenato/vibestack-openclaw`

No início do planejamento específico da 7Ps, o fork estava alinhado ao upstream analisado. As primeiras divergências introduzidas pela 7Ps são, neste momento, apenas documentação de planejamento.

## Arquitetura aprovada

- produto interno inicial: 7Ps Marketing AI;
- Hermes Agent como runtime principal;
- Ollama e modelo local como primeira estratégia de inferência;
- Telegram como canal humano principal;
- apenas o Diretor exposto ao Telegram inicialmente;
- sete profiles persistentes;
- skills como metodologia 7Ps;
- subagents como força temporária;
- Kanban como coordenação durável;
- knowledge base 7Ps separada de memória;
- Gestor de Tráfego presente desde a V1;
- Meta Ads preparado, mas sem write/configuração inicial;
- ferramentas especializadas de mídia/Ads preservadas;
- Docker Desktop como host das stacks;
- multi-clínica adiada para V2.

## Próxima etapa recomendada

Iniciar a **Fase 1 do roadmap**:

1. criar `D:\Docker\7ps-marketing-ai`;
2. clonar o fork nesse diretório;
3. revisar o Compose/Dockerfile pensando em Hermes-only;
4. definir mounts persistentes;
5. subir o menor runtime Hermes + Ollama possível;
6. validar tecnicamente antes de criar os sete profiles.

Nenhuma credencial Meta Ads deve ser necessária nessa etapa.
