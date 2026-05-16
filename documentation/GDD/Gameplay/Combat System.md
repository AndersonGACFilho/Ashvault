# Combat System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define o contrato de combate embodied-first com equivalência sistêmica entre VR e flatscreen.

## High Concept

Combate em Ashvault é físico, barulhento e perigoso. O objetivo não é maximizar DPS abstrato, mas criar uma troca de risco: atacar exige exposição, gera ruído, consome stamina e altera a percepção dos inimigos.

## Regras Centrais

- VR é gesto físico com colisão, alcance, velocidade e precisão.
- Flatscreen usa charge, timing, direção e vulnerabilidade equivalente.
- Toda ação ofensiva relevante gera ruído.
- Bloqueio, esquiva e ataque competem por stamina.
- Combate nunca pausa a simulação da dungeon.

## Pipeline de Ataque

1. Receber input abstrato.
2. Resolver modo de input.
3. Validar stamina, estado e arma.
4. Calcular intenção, direção e janela ativa.
5. Resolver colisão ou alvo.
6. Aplicar dano, stagger, ruído e desgaste.
7. Emitir eventos para IA, telemetria e progressão.

## Fórmula de Dano

```text
Damage =
  WeaponBaseDamage
  * AttackCommitment
  * HitQuality
  * MaterialModifier
  * SkillModifier
  * EnemyWeaknessModifier
  * StatusModifier
```

## Ruído de Combate

```text
CombatNoise =
  WeaponNoise
  + ImpactNoise
  + ArmorNoise
  + EnvironmentEcho
  - StealthMitigation
```

## Estados do Combatente

- Idle
- Windup
- Active
- Recovery
- Block
- Parry
- Dodge
- Stagger
- Exhausted

## Integrações

- **AI Systems:** recebe ruído, ameaça, dano e suspeita.
- **Noise & Perception:** propaga eventos acústicos.
- **Skill Tree:** concede XP por uso real de arma, defesa e evasão.
- **Crafting & Forging:** materiais e geometria da arma alteram dano, peso e ruído.
- **Bestiary:** conhecimento revela pontos fracos e resistências.

## MVP

- 3 armas corpo a corpo.
- Ataque leve, pesado, bloqueio e esquiva.
- Ruído por impacto.
- Stamina funcional.
- 3 tipos de inimigo com reações distintas.

## Critérios de Aceite

- Atacar sem pensar pode atrair inimigos.
- Flatscreen não é mais eficiente que VR.
- Toda arma comunica peso, alcance e risco.
- A IA reage de forma legível a dano e ruído.

## Tipos de Ataque

| Tipo | VR | Flatscreen | Custo sistêmico |
| --- | --- | --- | --- |
| Light | gesto curto, baixa inércia | clique/tap com recovery curto | baixo dano, baixo ruído |
| Heavy | gesto amplo e comprometido | charge com direção travada | alto dano, alto ruído, recovery |
| Thrust | avanço linear | input direcional + timing | bom alcance, ruim contra grupos |
| Bash | impacto com escudo/cabo | comando defensivo ofensivo | stagger, pouco dano |
| Throw | soltar/arremessar objeto | aim + commit | perda temporária do item |

## Stamina

```text
StaminaCost =
  BaseActionCost
  * WeaponWeightModifier
  * EncumbranceModifier
  * InjuryModifier
  * InputModeNormalization
```

Stamina baixa não deve apenas impedir ações. Ela deve degradar qualidade: ataques ficam mais lentos, bloqueios perdem estabilidade e recovery aumenta.

## Hit Quality

| Qualidade | Condição | Resultado |
| --- | --- | --- |
| Graze | contato parcial | dano baixo, pouco stagger |
| Clean | contato no arco correto | dano normal |
| Weakpoint | acerto em parte vulnerável | multiplicador por bestiário |
| Glancing | ângulo ruim ou armadura | dano reduzido, ruído ainda existe |
| Overcommit | ataque forte sem controle | dano alto, recovery alto |

## Modelo de Dados

```json
{
  "weapon_id": "iron_pickaxe",
  "weapon_class": "tool_weapon",
  "base_damage": 18,
  "stamina_cost_light": 8,
  "stamina_cost_heavy": 18,
  "noise_light": 14,
  "noise_heavy": 28,
  "reach_meters": 0.9,
  "weight": 3.4,
  "material_tags": ["iron", "blunt", "mining_tool"],
  "durability": 100
}
```

## Runtime Events

- `CombatActionStarted`
- `WeaponHitConfirmed`
- `DamageApplied`
- `StaminaSpent`
- `NoiseEmitted`
- `EnemyStaggered`
- `PlayerOvercommitted`

## Tuning

- Time-to-kill deve ser menor quando o jogador conhece fraquezas, não quando ignora defesa.
- Armas de mineração podem funcionar em combate, mas devem ser barulhentas e cansativas.
- Combate em grupo deve pressionar posicionamento; não apenas multiplicar HP.
- Ferramentas pesadas devem parecer úteis, mas perigosas em espaços apertados.

## Non-Goals

- Sistema de combos arcade extenso.
- Pausa tática durante combate.
- DPS como métrica dominante.
- Parry perfeito obrigatório para progresso.
