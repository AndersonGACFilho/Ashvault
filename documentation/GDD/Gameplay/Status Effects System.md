# Status Effects and Unified Effect Runtime

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define efeitos temporários, stacks, duração, fontes, imunidades e ordem de aplicação.

## High Concept

Status effects são a linguagem comum entre combate, magia, comida, alquimia, ambiente, deuses e corrupção. Todo efeito deve ser data-driven, previsível e integrado ao stack unificado.

## Autoridade

`UnifiedEffectsRuntime` é a autoridade de aplicação, stack, duração, counter, remoção e persistência de efeitos. Combat, Hazards, Alchemy, Cooking, Arcane Magic e Pantheon não aplicam buff/debuff por lógica paralela; eles solicitam ou emitem efeitos para este runtime.

## Categorias

| Categoria | Exemplos |
| --- | --- |
| Damage over Time | burn, bleed, poison |
| Control | stun, slow, fear, silence |
| Defense | shield, armor break, wet |
| Mobility | haste, root, heavy |
| Perception | blind, stealth boost, reveal |
| Arcane | overload, backlash, silence |
| Biological | toxin, sickness, regen |
| Divine | blessing, curse, sacrifice debt |
| Corruption | mutation, instability |

## Effect Definition

```json
{
  "effect_id": "bleed_minor",
  "category": "damage_over_time",
  "duration": 8,
  "tick_rate": 1,
  "stack_rule": "refresh_duration",
  "max_stacks": 1,
  "source_tags": ["slash", "organic"],
  "modifiers": [
    { "stat": "health", "op": "add_per_tick", "value": -2 }
  ],
  "counters": ["bandage", "life_blessing"]
}
```

## Stack Rules

- Refresh duration.
- Add stack up to cap.
- Replace weaker.
- Strongest only.
- Independent by source.
- Mutually exclusive tags.

## Ordem de Aplicação

```text
BaseStat
-> Equipment
-> Skill
-> Food/Alchemy
-> Magic
-> Divine
-> Biome/Hazard
-> Injury/Status
-> Final Clamp
```

## Status List MVP

- Burn.
- Bleed.
- Poison.
- Slow.
- Stagger.
- Stun.
- Wet.
- Armor Break.
- Regen.
- Shield.
- Arcane Overload.
- Corruption Minor.

## Resistência e Imunidade

Resistência reduz potência, duração ou chance. Imunidade total deve ser rara e telegráfica. Bosses podem converter hard CC em stagger/poise damage.

## Integrações

- **Combat:** bleed, stagger, armor break.
- **Alchemy:** poison, coatings, antidotes.
- **Cooking:** buffs e resistências.
- **Magic:** overload, silence, shield.
- **Pantheon:** blessing/curse.
- **Hazards:** burn, wet, toxin, frost.

## Validação

- Todo status tem fonte e counter ou duração razoável.
- Nenhum status empilha infinitamente.
- Status crítico tem feedback visual/sonoro.
- Save/load preserva duração restante.

## Non-Goals

- Lista infinita de debuffs redundantes.
- CC permanente.
- Efeitos invisíveis que matam sem feedback.
