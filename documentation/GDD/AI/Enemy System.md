# Enemy System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>) e complementar a [AI Systems](<AI Systems.md>).

## High Concept

Inimigos são agentes de pressão sistêmica. Eles protegem recursos, reagem a ruído, ocupam biomas e forçam decisões de combate, stealth, fuga ou manipulação ambiental.

## Enemy Definition

- `enemy_id`
- `role`
- `biome_tags`
- `health`
- `stamina`
- `perception_profile`
- `attack_profile`
- `loot_profile`
- `harvest_profile`
- `spawn_weight`

## Roles

- Grunt
- Stalker
- Brute
- Swarmer
- Sentinel
- Caster
- Ambusher

## State Machine

- Idle
- Patrol
- Investigate
- Alert
- Engage
- Flee
- Search
- Return
- Dead

## Spawn

Spawn deve respeitar bioma, profundidade, ruído recente, orçamento de dificuldade e constraints anti-softlock.

## Integrações

- **Bestiary:** revela comportamento, fraquezas e partes extraíveis.
- **Harvesting:** corpse e integridade definem recursos orgânicos.
- **Noise & Perception:** alimenta investigação e alerta.
- **Dungeon Generation:** posiciona inimigos por sala, bioma e pacing.

## MVP

- 4 inimigos base.
- 3 roles.
- Spawn por sala.
- Percepção simples.

## Critérios de Aceite

- Cada inimigo tem função de design clara.
- Spawn não bloqueia caminho obrigatório sem alternativa.
- Morte e corpse integram loot e harvesting.

## Role Matrix

| Role     | Pressão criada         | Counterplay                  | Integração         |
|----------|------------------------|------------------------------|--------------------|
| Grunt    | combate direto simples | bloqueio, dodge, alcance     | tutorial de ameaça |
| Stalker  | perseguição e tensão   | som, luz, portas             | stealth/perception |
| Brute    | controle de espaço     | mobilidade, fraqueza exposta | combate pesado     |
| Swarmer  | pressão numérica       | área, posicionamento         | ruído e stamina    |
| Sentinel | guarda recurso/gate    | stealth, distração           | dungeon pacing     |
| Caster   | status e zona          | interrupção, linha de visão  | magia/status       |
| Ambusher | surpresa               | bestiário, percepção         | notes/bioma        |

## Enemy Runtime Data

```json
{
  "enemy_instance_id": "enemy_floor2_014",
  "definition_id": "mine_stalker",
  "role": "stalker",
  "state": "investigate",
  "health": 42,
  "alert_score": 63,
  "last_known_player_position": [12.2, -4.0, 33.8],
  "spawn_room_id": "room_2_07",
  "loot_seed": 91221
}
```

## Spawn Budget

```text
RoomThreatBudget =
  BaseFloorDifficulty
  * BiomeThreatModifier
  * RoomTypeModifier
  * MicroWorldStateModifier
  - RecentPlayerFailureRelief
```

Spawn deve preencher orçamento por função, não apenas por custo numérico. Uma sala com três inimigos iguais é válida só se a função de design for pressão numérica.

## Percepção e Memória

Inimigos consomem eventos do `Noise & Perception System` e convertem score em intenção. Memória curta mantém investigação após perda de linha de visão; memória compartilhada propaga alerta apenas quando há comunicação válida.

## Loot e Harvest

Loot automático deve ser mínimo. O valor orgânico real vem do corpse e do `Harvesting System`, preservando qualidade, dano recebido e conhecimento do bestiário.

## Testes Obrigatórios

- Inimigo não spawna dentro de área inacessível.
- Inimigo não detecta através de oclusão inválida.
- State machine sempre tem transição de fallback para Return ou Idle.
- Corpse gerado tem `species_id` e `harvest_profile`.

## Non-Goals

- Inimigos como sacos de HP.
- Spawn puramente aleatório sem pacing.
- Loot table desconectada do corpo.
- IA com onisciência para compensar tuning fraco.

## Combat Profiles

| Perfil         | Padrão                 | Uso                       |
|----------------|------------------------|---------------------------|
| Melee Simple   | aproxima, ataca, recua | inimigo inicial           |
| Melee Pressure | mantém distância curta | força stamina e defesa    |
| Ranged Harass  | mantém linha de visão  | força cobertura/movimento |
| Pack Hunter    | coordena flancos       | pune barulho e isolamento |
| Guardian       | não persegue longe     | protege recurso ou porta  |

## Enemy Definition Example

```json
{
  "enemy_id": "mine_stalker",
  "role": "stalker",
  "biome_tags": ["mine", "dark"],
  "base_threat": 8,
  "health": 55,
  "stamina": 60,
  "armor_tags": ["hide"],
  "weakness_tags": ["bright_light", "piercing"],
  "perception_profile": "mine_stalker_perception",
  "attack_profile": "melee_pressure",
  "loot_profile": "stalker_basic",
  "harvest_profile": "stalker_organs",
  "spawn_constraints": {
    "min_floor": 2,
    "requires_dark_zone": true,
    "max_per_room": 2
  }
}
```

## Difficulty Scaling

Escalar inimigos deve priorizar composição, comportamento e contexto antes de inflar stats. Mais HP é último recurso. Um andar mais difícil pode usar patrulhas, ruído ambiental, sentinelas perto de recursos e rotas com cobertura limitada.

## Bestiary Integration

| Conhecimento | Desbloqueio                        |
|--------------|------------------------------------|
| Observed     | nome, silhueta, ameaça geral       |
| Fought       | ataques comuns, resistência básica |
| Harvested    | partes extraíveis                  |
| Studied      | fraquezas, comportamento, valor    |
| Mastered     | counterplay avançado e previsão    |

## Spawn Validation

```text
ValidSpawn =
  RoomHasNavmesh
  && NotBlockingCriticalPath
  && WithinThreatBudget
  && BiomeTagsMatch
  && NoDuplicateUniqueEnemy
  && PlayerHasCounterplay
```

## Telemetry

- Kills por inimigo.
- Mortes causadas por inimigo.
- Alertas gerados por inimigo.
- Dano médio causado.
- Tempo até primeiro hit.
- Harvest rate por espécie.

## Critérios de Saída Técnica

- Cada enemy definition passa validação de dados.
- Cada role aparece em pelo menos um cenário testado.
- Bestiário recebe eventos corretos de observação/luta/harvest.
- Spawn budget impede salas impossíveis no MVP.
