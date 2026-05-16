# Itemization and Equipment System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define equipamentos, instâncias de item, raridade, qualidade, durabilidade, traits, modificadores e origem.

## High Concept

Itemização em Ashvault deve conectar risco, origem e uso. Um item não é apenas uma estatística: ele carrega material, qualidade de extração, método de fabricação, corrupção, bênção, desgaste, modificadores e histórico econômico.

## Objetivos

- Fazer loot, crafting, forja, alquimia e economia usarem o mesmo modelo de item.
- Permitir itens únicos sem quebrar o balanceamento data-driven.
- Preservar origem para anti-dupe, economia fechada e telemetria.
- Criar trade-offs claros entre poder, peso, ruído, durabilidade e risco.

## Tipos de Equipamento

| Tipo | Exemplos | Slots | Função |
| --- | --- | --- | --- |
| Weapon | espada, lança, picareta-arma | mãos/costas | dano, ruído, alcance |
| Tool | picareta, faca, frasco | mãos/cinto | mineração, harvesting, alchemy |
| Armor | peito, luvas, botas | corpo | defesa, peso, som |
| Focus | talismã, runestone | acessório | magia, deuses, efeitos |
| Container | bolsa, mochila, caixa | corpo/mão | capacidade, organização |
| Consumable Gear | coating, charm, kit | temporário | modificador limitado |

## Raridade

Raridade indica disponibilidade e complexidade, não poder bruto automático.

| Raridade | Papel |
| --- | --- |
| Common | base econômica e reparo |
| Uncommon | material ou trait útil |
| Rare | origem difícil, sinergia forte |
| Relic | regra especial, lore, risco |
| Divine | afinidade/pantheon |
| Corrupted | poder com custo |

## Qualidade

```text
ItemQuality =
  SourceQuality
  * CraftingSkillModifier
  * MaterialIntegrity
  * ToolPrecision
  * FailurePenalty
```

Qualidade afeta teto, durabilidade, valor e chance de receber trait. Ela não deve transformar item comum em item lendário sem fonte e processo compatíveis.

## Durabilidade

```text
DurabilityLoss =
  BaseUseWear
  * MaterialFragility
  * ImpactSeverity
  * EnvironmentModifier
  - MaintenanceModifier
```

Durabilidade baixa reduz performance antes de quebrar: arma perde dano/estabilidade, ferramenta perde qualidade de extração e armadura perde mitigação.

## Item Instance

```json
{
  "item_instance_id": "run_042_blade_iron_003",
  "definition_id": "short_blade",
  "origin": {
    "source_type": "forged",
    "run_id": "run_042",
    "materials": ["iron_ore_017", "stalker_bone_002"]
  },
  "quality": 0.78,
  "rarity": "uncommon",
  "durability": 84,
  "traits": ["balanced_edge"],
  "modifiers": [
    { "stat": "stamina_cost", "op": "mul", "value": 0.96 }
  ],
  "flags": ["sellable", "repairable"]
}
```

## Traits

| Trait | Efeito | Custo |
| --- | --- | --- |
| Balanced | stamina menor | valor alto |
| Dense | stagger maior | peso e ruído |
| Keen | critical chance maior | durabilidade menor |
| Quiet | ruído menor | dano menor |
| Blessed | bônus divino | exige afinidade |
| Corrupted | poder alto | mutação/toxicidade |

## Sockets e Coatings

Sockets recebem gemas, runas ou essências. Coatings são temporários e vêm de alchemy. Ambos entram no stack oficial de efeitos e precisam declarar duração, fonte e regra de remoção.

## Drop Rules

- Inimigos dropam partes ou itens coerentes com corpo/equipamento.
- Bosses podem dropar relics, mas reward precisa ser idempotente.
- Baús usam seed semanal e constraints de economia.
- Mining/harvesting geram recursos, não armas prontas por padrão.

## Integrações

- **Loot System:** cria instâncias e aplica origem.
- **Crafting & Forging:** modifica material, qualidade e traits.
- **Economy:** preço usa origem, qualidade, demanda e raridade.
- **Inventory:** peso, volume, slots e containers.
- **Combat:** arma e armadura alteram dano, stamina, poise e ruído.
- **Persistence:** item instance precisa sobreviver a save/load sem duplicação.

## Validação

- Todo item vendável precisa de `base_value`.
- Todo item de run precisa de `origin`.
- Traits não podem aplicar stats inexistentes.
- Modificadores entram em ordem determinística.
- Item sem definition válida entra em quarantine, não no mercado.

## MVP

- 6 armas/ferramentas.
- 3 armaduras ou peças defensivas.
- Qualidade, durabilidade e origem.
- 8 traits simples.
- Sem sockets complexos no MVP.

## Non-Goals

- Gear score como substituto de decisão.
- Loot colorido sem origem sistêmica.
- Itens infinitamente reparáveis sem custo.
- Raridade sempre significando maior DPS.

