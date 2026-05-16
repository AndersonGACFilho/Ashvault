# Loot, Drops and Reward System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define loot tables, drops, rewards, raridade, qualidade, origem e anti-farm.

## High Concept

Loot em Ashvault é consequência de risco e fonte. Tudo que entra na economia precisa de origem: mineração, harvesting, boss, baú, contrato, corpse ou recompensa validada.

## Dungeon Anchor Contract

Dungeon Generation não instancia item econômico final. Quando a dungeon precisa representar loot, chest, node, reward ou recurso de sala, ela emite anchor, tag, constraint e contexto. Loot/Reward resolve tabela, raridade, item instance, origem, qualidade e valor.

```text
DungeonAnchor
-> LootTableRoll
-> ItemInstanceCreated(origin_id, source_type, rarity, quality)
-> EconomyEligible somente após extração ou regra de reward válida
```

## Fontes de Loot

| Fonte | Exemplo | Regra |
| --- | --- | --- |
| Mining | minério, cristal | depende de nó, ferramenta e qualidade |
| Harvesting | órgão, osso, veneno | depende de corpse e bestiário |
| Enemy Gear | arma quebrada, trinket | raro e coerente com inimigo |
| Boss | relic, gate material | reward idempotente |
| Chest | consumível, moeda, recipe | seed semanal e sala |
| Contract | pagamento, reputação | validado por objetivo |
| Corpse | itens perdidos | origem preservada |
| Shrine | bênção/item divino | custo ritual |

## Loot Table

```json
{
  "loot_table_id": "mine_chest_tier_02",
  "weekly_seeded": true,
  "entries": [
    { "definition_id": "iron_ingot", "weight": 40, "min": 1, "max": 3 },
    { "definition_id": "minor_healing_food", "weight": 20, "min": 1, "max": 1 },
    { "definition_id": "forge_recipe_basic_guard", "weight": 5, "min": 1, "max": 1 }
  ],
  "constraints": {
    "max_value": 180,
    "requires_floor_min": 2
  }
}
```

## Reward Resolution

```text
RewardValue =
  BaseTableValue
  * FloorDepthModifier
  * RoomRiskModifier
  * WeeklyDemandModifier
  * PlayerKnowledgeModifier
  * AntiFarmModifier
```

## Raridade e Qualidade

Raridade vem da fonte e da tabela. Qualidade vem do processo: extração, combate, crafting, preservação e estado do mundo.

## Anti-Farm

- Seed semanal limita repetição perfeita.
- Baús não respawnam dentro da mesma run.
- Inimigos triviais têm diminishing returns.
- Harvesting depende de corpse real.
- Boss rewards são idempotentes por elegibilidade.
- Contratos não podem ser entregues com item sem origem compatível.

## Weekly Seed Influence

Seed semanal pode alterar demanda, distribuição de salas, baús, nodes raros e contratos. Não deve alterar reward já confirmado ou save persistente.

## Loot Events

- `LootTableRolled`
- `ItemInstanceCreated`
- `RewardGranted`
- `RewardClaimRejected`
- `LootSourceDepleted`
- `AntiFarmModifierApplied`

## Integrações

- **Itemization:** cria instâncias com origin e traits.
- **Economy:** calcula valor e controla inflação.
- **Dungeon Generation:** posiciona fontes.
- **Contracts:** valida itens entregues.
- **Telemetry:** mede geração de valor por fonte.

## Edge Cases

| Caso | Regra |
| --- | --- |
| Jogador desconecta após reward | confirmar por transaction_id |
| Baú aberto antes de crash | estado do baú persiste |
| Item gerado sem origem | quarantine |
| Boss morto duas vezes | reward único por elegibilidade |
| Loot cai em abismo | reposicionar ou marcar inacessível conforme regra |

## MVP

- Loot de mining, harvesting, chest e boss.
- Origem por item instance.
- Loot tables seedadas.
- Anti-dupe básico.

## Critérios de Aceite

- Todo item vendável tem fonte rastreável.
- Loot não duplica por reload.
- Reward de boss é idempotente.
- Valor por fonte aparece na telemetria.
