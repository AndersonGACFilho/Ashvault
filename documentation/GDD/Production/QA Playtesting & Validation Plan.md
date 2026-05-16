# QA, Playtesting and Validation Plan

> Documento de produção derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define matriz de QA, objetivos de playtest, validação de VR, multiplayer, economia, dungeon, save e performance.

## High Concept

Ashvault é sistêmico demais para validar só por feeling. Cada loop crítico precisa de teste funcional, playtest qualitativo e telemetria mínima.

## Playtest Goals

| Fase | Pergunta |
| --- | --- |
| Prototype | o loop físico funciona? |
| MVP | jogador entende entrar, coletar, sair, vender? |
| Alpha | sistemas geram decisões diferentes? |
| Beta | co-op, save e economia aguentam uso real? |

## Test Matrix

| Área | Teste |
| --- | --- |
| VR Comfort | 20 min dungeon, queda, mineração repetida |
| Cross Input | tempo/custo por ação VR vs flatscreen |
| Dungeon Generation | 100 seeds sem softlock crítico |
| Economy | anti-dupe, inflação, venda/recompra |
| Combat | stamina, stagger, grupos, boss |
| AI Detection | visão, som, suspeita, oclusão |
| KnowledgeRuntime | notes, bestiário e rumores convergem no mesmo estado |
| UnifiedEffectsRuntime | stacks, counters e remoção sem lógica paralela |
| LayerLockValidator | queda, corda, dig, magia e alchemy não atravessam gate |
| Save/Load | crash em venda, morte, boss, transição |
| Multiplayer | desync, disconnect, reward, corpse |
| Performance | FPS target, entity budget, pooling |

## Exploit Tests

- Comprar e revender para lucro infinito.
- Duplicar item por reconnect.
- Matar boss com player inelegível.
- Pegar loot de baú após reload.
- Farmar XP com ação segura.
- Evitar custo de flatscreen trocando modo.
- Corpse inacessível atrás de gate.

## VR Comfort Protocol

Registrar enjoo, fadiga, leitura, altura, alcance, turn mode, queda e mineração. Sessão falha se desconforto impede completar loop básico.

## Dungeon Validation

```text
ValidSeed =
  CriticalPathExists
  && BossGateReachable
  && ExtractionReachable
  && LayerLockValidatorPasses
  && CampEligibilityDoesNotBlockCriticalPath
  && NoRequiredHazardWithoutCounterplay
  && ResourceBudgetWithinRange
```

## Economy Validation

- Nenhum item sem origin é vendável.
- Sinks compensam fontes.
- Contratos consomem itens.
- NPC explorer não imprime valor sem risco.
- InflationIndex dentro do target.

## Multiplayer Validation

- Host disconnect.
- Client reconnect.
- Dois jogadores pegando mesmo item.
- Morte simultânea.
- Queda separando party.
- Boss reward individual.

## Performance Budgets

| Plataforma | Target |
| --- | --- |
| PC VR | 90 FPS ideal, reprojection controlada |
| PC Flatscreen | 60 FPS mínimo |
| Dungeon active enemies | budgetado por sala |
| Save writes | fora de frame crítico |
| Audio events | pooling e culling |

## Bug Severity

- S0: corrupção de save, softlock, crash frequente.
- S1: duplicação econômica, boss bypass, corpse inacessível.
- S2: sistema central confuso ou injusto.
- S3: UI, tuning, feedback.
- S4: polimento.

## Critérios de Saída MVP

- 100 seeds passam validação básica.
- Crash tests críticos recuperam save.
- Nenhum dupe conhecido em economia MVP.
- Jogadores completam primeira extração.
- Eventos críticos possuem schema/version e parser.
- VR comfort aceitável em sessão de 20 minutos.
