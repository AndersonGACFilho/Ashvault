# M0 Core Contracts Backlog

> Backlog técnico do primeiro marco do [MVP Implementation Plan](<MVP Implementation Plan.md>). Este documento define entregas executáveis de fundação, sem alterar escopo de produto.

## Objetivo

Criar a base mínima para plugins trocarem dados, eventos e snapshots sem acessar internals uns dos outros.

## Entregáveis

| Item | Entrega | Critério de aceite |
| --- | --- | --- |
| M0-01 | `GameId` base e tipos fortes para IDs | IDs serializam, comparam e validam formato |
| M0-02 | `EntityId`, `PlayerId`, `RunId`, `FloorId`, `RoomId` | nenhum domínio usa string solta para entidade central |
| M0-03 | `ItemDefinitionId`, `ItemInstanceId`, `LootSourceId` | item instance sempre pode apontar origem |
| M0-04 | `GameEvent` base | todo evento tem `event_type`, `schema`, `timestamp`, `source` |
| M0-05 | `EventBus` síncrono inicial | listener recebe evento sem conhecer emissor concreto |
| M0-06 | `DefinitionRegistry` | definitions carregam por ID e falham em referência ausente |
| M0-07 | `TickOrder` documentado em código | ordem Input -> Player -> Interaction -> Combat -> Perception/AI -> Economy -> UI -> Persistence -> Telemetry |
| M0-08 | `SaveEnvelope` | save local tem `schema_version`, domínios separados e checksum placeholder |
| M0-09 | `TelemetryEvent` | evento persistido tem schema/version e pode ir para quarantine |
| M0-10 | `PluginBoundaryRules` | dependências proibidas são listadas e verificáveis por revisão/build script futuro |

## Definition Assets

Criar formato inicial para:

- `ItemDefinition`
- `InteractionDefinition`
- `EnemyDefinition`
- `StatusEffectDefinition`
- `LootTableDefinition`
- `LocalizationKey`

Esses assets podem ser `ScriptableObject` ou JSON/asset equivalente. A decisão final deve favorecer versionamento, validação e referência por ID.

## Build-Time Validation MVP

Checks mínimos:

- IDs únicos.
- Referências existentes.
- Nenhum item vendável sem `base_value`.
- Nenhuma interaction sem cancel policy.
- Nenhum status sem stack rule.
- Nenhum evento persistido sem schema/version.
- Nenhum texto visível sem loc key placeholder.

## Fora de Escopo M0

- UI final.
- Multiplayer authority.
- Dungeon procedural completa.
- Economia real.
- Save migration completa.
- Telemetry dashboard.
- Otimização de performance.

## Saída do Marco

M0 está completo quando um plugin de teste consegue:

1. carregar definitions por ID;
2. emitir um `GameEvent`;
3. receber esse evento em outro plugin;
4. criar um snapshot simples no `SaveEnvelope`;
5. persistir um `TelemetryEvent` versionado;
6. falhar o build/revisão quando uma referência obrigatória está ausente.
