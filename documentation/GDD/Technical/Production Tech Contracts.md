# Production Tech Contracts

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define contratos técnicos, managers, event bus, tick e runtime.

## High Concept

Ashvault tem muitos sistemas acopláveis. A arquitetura deve forçar comunicação por eventos e contratos claros para evitar dependências circulares e estado global implícito.

## Autoridades Core

- RunManager
- DungeonManager
- EconomyManager
- InventoryManager
- CombatManager
- AIManager
- InteractionManager
- KnowledgeRuntime
- UnifiedEffectsRuntime
- NoiseAuthority
- LayerLockValidator
- PersistenceManager
- TelemetryManager
- LocalizationManager

## Runtimes Soberanos

Estes contratos são autoridade única. Sistemas consumidores podem solicitar validação, emitir eventos ou aplicar resultados aprovados, mas não devem recriar a mesma regra localmente.

| Runtime | Autoridade | Consumidores principais |
| --- | --- | --- |
| `KnowledgeRuntime` | estado de descoberta, hipótese, confirmação, falsidade e obsolescência | Notes, Bestiary, NPC rumors, Alchemy, Arcane Magic |
| `UnifiedEffectsRuntime` | buff, debuff, status, duração, stack, counter e remoção | Combat, Hazards, Alchemy, Cooking, Arcane Magic, Pantheon |
| `NoiseAuthority` | fatos perceptivos derivados de ruído, visão, contexto, oclusão e memória curta | AI, Enemy, Boss, Hive, stealth |
| `LayerLockValidator` | regra única de anti-bypass de camada, boss gate e progressão vertical | Dungeon, rope, fall, dig, hazards, alchemy utility, spell cast, AI traversal |

### KnowledgeRuntime Contract

```json
{
  "knowledge_id": "monster_bat_metal_weakness",
  "source": "player_note",
  "confidence": 0.62,
  "state": "hypothesis",
  "scope": "world",
  "loss_policy": "unconfirmed_can_drop"
}
```

Estados permitidos: `unknown`, `hypothesis`, `confirmed`, `false`, `obsolete`. Escopos permitidos: `run`, `world`, `meta`.

### LayerLockValidator Contract

Qualquer ação que crie acesso, dano estrutural, movimento vertical, passagem, scouting ou automação entre camadas deve chamar o mesmo validator antes de aplicar resultado:

```text
LayerLockValidator.Validate(action, source_floor, target_floor, actor, context)
```

Se falhar, o sistema consumidor deve bloquear organicamente, gerar feedback legível e registrar evento de tentativa. Nenhum GDD de feature pode criar exceção própria para boss gate.

### CampEligibilityToken Contract

Dungeon Generation é dona de elegibilidade espacial; Rest é dono de custo, recuperação e risco. A bridge entre os dois sistemas é um token validado, não leitura direta de sala pelo Rest.

```json
{
  "token_id": "camp_floor03_room07",
  "floor_id": "week_007_floor_03",
  "room_id": "room_07",
  "source": "DungeonFloorValidator",
  "allows_camp": true,
  "threat_ceiling": "low",
  "critical_path_blocked": false,
  "escape_route_valid": true
}
```

`sleep_zone` não equivale a `Camp`. `RoomAllowsCamp` só pode ser verdadeiro quando esse token ou uma `RestSiteDefinition` autorizada existir.

## Event Bus

Eventos devem ser pequenos, versionáveis e orientados a fatos:

- `RunStarted`
- `ResourceExtracted`
- `ItemSold`
- `NoiseEmitted`
- `EnemyAlerted`
- `PlayerDied`
- `CorpseCreated`
- `FloorChanged`

## Ordem de Tick

1. Input
2. Player state
3. Interactions
4. Combat
5. AI/perception
6. Economy/runtime timers
7. UI feedback
8. Persistence checkpoints
9. Telemetry

### Nota arquitetural - lag intencional de percepcao

`NoiseEmitted` gerado em Combat (tick 4) e consumido por AI/perception no tick 5 do mesmo frame. Isso significa que a IA nunca reage a ruido de combate no mesmo frame em que ele e gerado, sempre no frame seguinte.

Nesse contexto, "consumido" significa registrado/enfileirado pela perception; decisoes ou acoes resultantes so podem ser aplicadas no proximo frame.

Esse comportamento e intencional: evita race conditions entre resolucao de dano e decisao de IA, e cria uma janela minima de reacao que beneficia o jogador. Em testes de QA, uma deteccao com zero frames de lag e um bug, nao uma melhoria.

## MVP

- Event bus.
- Managers isolados.
- Object pooling para entidades frequentes.
- Debug overlay de eventos.

## Critérios de Aceite

- Sistemas não chamam internamente managers alheios sem contrato.
- Ordem de tick é documentada e testável.
- Eventos críticos são rastreáveis.

## Manager Contract

Cada manager deve declarar:

- Domínio de autoridade.
- Eventos que consome.
- Eventos que emite.
- Dados que persiste.
- Dependências permitidas.
- Ordem de tick, se aplicável.

## Event Contract

```json
{
  "event_type": "ResourceExtracted",
  "version": 1,
  "run_id": "run_042",
  "source_id": "ore_node_2_14",
  "item_instance_id": "run_042_iron_ore_017",
  "quality": 0.72,
  "timestamp": 182.4
}
```

## Event Schema Migration

Eventos têm política própria de schema, separada de saves. `version` ou `schema` no payload identifica o formato do evento emitido; analytics pode migrar eventos antigos para leitura, mas gameplay não deve depender de migração retroativa de telemetria.

Regras:

- Todo evento versionado deve ter parser explícito.
- Evento desconhecido vai para quarantine, não quebra load nem run.
- Mudança de significado exige novo schema, não reaproveitamento silencioso.
- Métricas derivadas registram quais schemas aceitaram.
- Saves podem descartar `TelemetryBuffer`; não podem descartar domínios econômicos para preservar evento.

## Runtime Limits

| Limite | Target MVP |
| --- | --- |
| Inimigos ativos | 12-20 |
| Corpses persistentes | 1 por jogador |
| Resource nodes ativos | por sala carregada |
| Eventos por frame | budgetado e agrupado |
| Saves automáticos | fora de frame crítico |

## Object Pooling

Obrigatório para efeitos de impacto, partículas, eventos visuais frequentes, projéteis, áudio one-shot e markers de debug. Pooling não deve esconder leak de estado.

## Testes Obrigatórios

- Evento crítico tem listener esperado.
- Manager não acessa domínio proibido.
- Ordem de tick reproduz resultado determinístico.
- Pool limpa estado antes de reutilização.

## Non-Goals

- Singleton global sem contrato.
- Update loop arbitrário em cada sistema.
- Eventos com payload gigante.

## Dependency Rules

| Sistema | Pode depender de | Não pode depender de |
| --- | --- | --- |
| Combat | Player, Inventory, EventBus | Economy direto |
| Economy | Inventory transaction, Run result | Combat internals |
| AI | Perception, Dungeon, EventBus | UI |
| Persistence | serializable domain snapshots | scene objects crus |
| UI | view models/events | mutação direta de domínio |

## Naming e Organização

```text
*Definition  -> dados estáticos
*State       -> dados runtime mutáveis
*Manager     -> autoridade de domínio
*System      -> lógica operacional isolada
*Event       -> fato emitido
*View        -> apresentação/UI
```

## Definition Validation

Dados content-driven devem validar:

- IDs únicos.
- Referências existentes.
- Ranges numéricos.
- Tags conhecidas.
- Loc IDs existentes.
- Ausência de ciclos proibidos.

## Build-Time Checks

- Nenhum resource sellable sem `base_value`.
- Nenhum enemy sem role.
- Nenhuma interaction sem cancel policy.
- Nenhum texto visível sem loc ID.
- Nenhum item de run sem origem.
- Nenhum GDD sistêmico sem `fase_permitida`.
- Nenhum evento crítico sem schema/version e parser.
- Nenhuma ação vertical/progressiva sem `LayerLockValidator`.
- Nenhum dungeon anchor instancia item econômico final.
- Nenhum item econômico entra em Economy sem `origin_id` e `item_instance_id`.

## Critérios de Saída

- Um novo sistema consegue ser adicionado por eventos.
- Content validation roda antes de build jogável.
- Debug overlay mostra eventos críticos.
- Managers têm ownership documentado.
