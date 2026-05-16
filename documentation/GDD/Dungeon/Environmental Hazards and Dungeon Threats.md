# Environmental Hazards and Dungeon Threats

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define quedas, gás, fogo, colapso, armadilhas, zonas de aether e ameaças ambientais.

## High Concept

A dungeon é um inimigo sistêmico. Perigos ambientais devem criar decisões de rota, peso, preparo, leitura e timing, não apenas dano aleatório.

## Tipos de Perigo

| Perigo | Efeito | Counterplay |
| --- | --- | --- |
| Queda/abismo | dano, separação de andar | corda, rota, peso menor |
| Gás | poison, visão reduzida | máscara, ventilação, fogo perigoso |
| Fogo/calor | burn, área bloqueada | água, proteção, timing |
| Frio | slow, stamina drain | comida, roupa, magia |
| Colapso | bloqueio, dano, ruído | leitura, suporte, fuga |
| Armadilha | dano/status/ruído | percepção, tool, notes |
| Terreno instável | queda, stagger | peso menor, cuidado |
| Sala inundada | wet, movimento lento | rota alternativa |
| Aether zone | magia instável | foco, resistência, evitar |
| Área selada | gate/lore | boss key, contrato, ritual |

## Hazard Definition

```json
{
  "hazard_id": "toxic_gas_pocket",
  "type": "gas",
  "biome_tags": ["mine", "fungal"],
  "trigger": "proximity_or_impact",
  "effects": ["poison_minor", "vision_haze"],
  "duration": 18,
  "counterplay": ["vent", "mask", "ignite_with_risk"],
  "noise_on_trigger": 8
}
```

## Queda Vertical

Queda é identidade central. Dano considera altura, peso, landing surface e estado do jogador.

```text
FallDamage =
  HeightSeverity
  * EncumbranceModifier
  * LandingMaterialModifier
  - Mitigation
```

Queda pode separar party, mover jogador para andar inferior e criar corpse em contexto complexo.

## Boss Floor Lock

Boss floors podem selar retorno temporário para impedir cheese. O lock precisa ser diegético, legível e validado para não criar softlock.

## Integrações

- **Dungeon Generation:** posicionamento e validação anti-softlock.
- **Status Effects:** burn, poison, slow, wet, corruption.
- **Party:** separação por queda e rescue.
- **Noise:** colapso e armadilhas emitem som.
- **Notes/Bestiary:** conhecimento revela counterplay.

## Validação

- Perigo obrigatório deve ter counterplay conhecido ou ensinável.
- Hazard não pode bloquear caminho crítico sem rota alternativa.
- Spawn de corpse não pode ficar em morte instantânea contínua.
- Hazard tem feedback antes ou durante ativação.

## MVP

- Queda.
- Armadilha simples.
- Gás tóxico.
- Terreno instável.
- Sala com colapso leve.

## Critérios de Aceite

- Jogador entende por que tomou dano.
- Hazard altera decisão de rota ou carga.
- Procedural generation valida softlock.
- Party separation por queda é recuperável.

