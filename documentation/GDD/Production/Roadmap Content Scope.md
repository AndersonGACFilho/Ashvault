# Roadmap Content Scope

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Consolida fases, escopo, conteúdo e prioridades.

## High Concept

Roadmap protege o projeto contra expansão prematura. Cada fase deve provar um conjunto específico de loops antes de adicionar novos sistemas, biomas ou multiplayer amplo.

## Autoridade de Fase

Este documento é soberano sobre escopo de fase. Roadmaps internos de GDDs específicos descrevem a visão completa do sistema, mas não autorizam implementação antes dos gates deste arquivo.

Todo GDD sistêmico deve declarar:

- `fase_permitida`: MVP, Alpha, Beta, P&D ou Pós-Beta.
- `escopo_mvp`: o menor corte jogável, se existir.
- `bloqueadores`: contratos ou runtimes exigidos antes da implementação.
- `fora_do_mvp`: features explicitamente adiadas.
- `critério_de_corte`: condição que remove a feature da fase atual.

## MVP

- 1 bioma: mina.
- 3-5 andares.
- 1 boss.
- Mining, combate, inventário, economia e run loop.
- Loja do jogador básica.
- Notes e bestiário mínimos.
- Alchemy apenas como suporte de Hub, limitado a outputs básicos, se `UnifiedEffectsRuntime` e `KnowledgeRuntime` já existirem.

## Alpha

- Segundo bioma.
- Alquimia e cooking ampliados.
- NPC Explorer funcional.
- Mais inimigos e recursos.
- Persistência robusta.

## Beta

- Multiplayer mais completo.
- Cross-input refinado.
- Telemetria de balanceamento.
- Mais bosses, deuses e conteúdo econômico.

## P&D Paralelo

- Arcane Magic pode ter vertical slice técnico, mas não bloqueia MVP.
- Magia linguística completa só entra no backlog de produto depois de `UnifiedEffectsRuntime`, `KnowledgeRuntime`, `NoiseAuthority`, `LayerLockValidator` e CrossInput estarem validados.

## Prioridade

1. Player, input e interação.
2. Run, dungeon e extração.
3. Mining, inventário e economia.
4. Combate, IA e ruído.
5. Persistência e telemetria.
6. Conteúdo e polimento.

## Critérios de Aceite

- Cada fase tem loop jogável fechado.
- Sistemas futuros não bloqueiam MVP.
- Conteúdo novo só entra quando contratos base estão estáveis.

## MVP Exit Criteria

- Jogador completa run de 15-30 minutos.
- Minera, luta, carrega, extrai e vende.
- Morte gera corpse recuperável.
- 1 boss gate funciona.
- Save/load resiste a crash simples.
- VR e flatscreen passam fairness básico.

## Alpha Exit Criteria

- Dois biomas com diferença mecânica real.
- NPC explorador gera economia sem quebrar risco.
- Alquimia/cooking têm decisões relevantes.
- Telemetria local orienta balanceamento.
- Conteúdo repetível por seed semanal.

## Beta Exit Criteria

- Co-op estável em escopo definido.
- Cross-input refinado por dados.
- UI/UX final funcional.
- Economia com inflação controlada.
- Bosses e progressão sem softlock.

## Dependency Map

```text
Input -> Interaction -> Player -> Run
Run -> Persistence/EventBus -> Dungeon -> LayerLockValidator
Dungeon -> Mining -> Inventory -> Economy
Combat -> NoiseAuthority -> AI -> Enemy
UnifiedEffectsRuntime -> Combat/Hazards/Alchemy/Magic
KnowledgeRuntime -> Notes/Bestiary/Rumors/Alchemy/Magic
Telemetry -> Balance
```

## Non-Goals

- Adicionar biomas antes do loop MVP fechar.
- Multiplayer antes de save e economia estáveis.
- Sistema novo sem critério de aceite.

## Feature Gates

| Feature | Só começa quando |
| --- | --- |
| Segundo bioma | geração da mina + loop econômico estável |
| Alquimia completa | `UnifiedEffectsRuntime`, `KnowledgeRuntime`, harvesting e properties estão funcionais |
| Field alchemy/coatings complexos | Alchemy de Hub tem telemetry, falha e economia sem exploit |
| NPC Explorer | economia, contratos, `KnowledgeRuntime` e risco off-screen estão validados |
| Co-op expandido | save, corpse e economia não duplicam |
| Boss 2+ | primeiro boss tem telemetry e clear rate aceitável |
| Arcane Magic jogável | vertical slice P&D prova valor sem bypass, sem vantagem de input e sem efeitos paralelos |
| VRChat world | kit visual MVP existe |
| MORL avançado | telemetria básica está confiável |

## Definition of Done por Sistema

Um sistema só sai de "protótipo" quando possui:

- Dados versionados.
- Fase permitida declarada contra este roadmap.
- Runtime pipeline definido.
- Integração com save quando necessário.
- Critérios de aceite testáveis.
- Pelo menos um cenário de falha.
- Telemetria mínima se afeta balanceamento.

## Backlog Arquitetural

| Sistema | Motivo para adiar |
| --- | --- |
| Multiplayer amplo | multiplica bugs de economia e save |
| Pantheon completo | depende de stack de efeitos estável |
| Crafting avançado | depende de inventory/economia |
| Vários bosses | depende de telemetria de boss 1 |
| Biomas exóticos | dependem da linguagem da mina |
| NPC Explorer | depende de contratos, economia e risco auditável |
| Arcane Magic completa | depende de effects, knowledge, input parity, perception e anti-bypass |

## Planejamento de Conteúdo MVP

- 1 kit de dungeon.
- 4 inimigos.
- 1 boss.
- 8 recursos minerais.
- 5 itens vendáveis.
- 3 ferramentas.
- 10 notes.
- 1 loja.
- 1 corpse loop.

## Critérios de Corte

Se uma feature não ajuda o loop entrar-coletar-sair-vender, ela é candidata a corte do MVP. Se ajuda, mas exige outro sistema instável, vira mock controlado ou fica para Alpha.
