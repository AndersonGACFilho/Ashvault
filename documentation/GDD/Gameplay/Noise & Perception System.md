# Noise & Perception System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Consolida ruído, visão, audição, suspeita e stealth multicanal.

## High Concept

Stealth em Ashvault não é invisibilidade binária. É uma leitura contínua de visão, som, contexto, iluminação, peso, ação e memória curta dos inimigos.

## Autoridade

Este documento define o `NoiseAuthority`: a fonte única de fatos perceptivos. Combat, Mining, Inventory, Dungeon, Alchemy e Magic emitem estímulos; `NoiseAuthority` calcula propagação, oclusão, suspeita e memória curta; AI consome fatos e decide ação. AI não recalcula percepção por conta própria.

## AcousticEvent

```text
AcousticEvent {
  source_id
  position
  base_loudness
  frequency_profile
  material_context
  duration
  suspicion_tag
}
```

## Propagação

```text
PerceivedNoise =
  BaseLoudness
  * EnvironmentEcho
  * MaterialTransmission
  / DistanceFalloff
  - Occlusion
  - AmbientMasking
```

## Canais

- Visão
- Áudio
- Suspeita contextual
- Memória curta
- Alerta compartilhado

## Integrações

- **Combat:** impactos e armas geram eventos altos.
- **Inventory:** peso e equipamento aumentam ruído.
- **AI Systems:** consome percepção para GOAP/state machine.
- **Dungeon/Biome:** materiais e espaços alteram eco e oclusão.
- **Magic/Alchemy:** efeitos podem mascarar, amplificar ou distrair.

## MVP

- Eventos de ruído por combate, mineração, queda e interação.
- Detecção por distância e linha de visão.
- Estado de suspeita.
- Debug view para tuning.

## Critérios de Aceite

- O jogador consegue inferir por que foi detectado.
- Ruído tem consequência sistêmica real.
- Stealth não depende de regra binária opaca.

## Detection Score

```text
DetectionScore =
  VisionScore
  + AudioScore
  + ContextSuspicion
  + SharedAlert
  - Concealment
  - Distraction
```

| Score | Estado IA     |
|-------|---------------|
| 0-20  | unaware       |
| 21-45 | suspicious    |
| 46-70 | investigating |
| 71-90 | alerted       |
| 91+   | engaged       |

## Vision Score

```text
VisionScore =
  LineOfSight
  * LightExposure
  * MovementVisibility
  * DistanceClarity
  * EnemyPerception
```

## Audio Sources

| Fonte           | Loudness base | Tags                  |
|-----------------|---------------|-----------------------|
| passo leve      | 4             | footstep              |
| passo pesado    | 10            | footstep, encumbered  |
| mineração       | 30            | tool, stone, repeated |
| impacto de arma | 20            | combat                |
| queda           | 35            | impact, body          |
| magia           | variável      | arcane                |

## Memory

Inimigos mantêm memória curta de última posição, tipo de evento e ameaça. Memória expira com tempo, distância ou evento contraditório.

## Debug/Tuning

O MVP deve incluir visualização de cones, raio auditivo aproximado, score atual e último evento percebido. Isso é ferramenta de desenvolvimento, não UI final obrigatória.

## Anti-Exploit

- Repetir ruído no mesmo local pode atrair investigação coordenada.
- Inimigos não devem esquecer instantaneamente ao perder linha de visão.
- Stealth pesado deve competir com inventário e economia.

## Non-Goals

- Furtividade binária.
- Inimigos oniscientes.
- Cone visual como única percepção.
- Silenciador universal sem custo.

## Perception Profile

```json
{
  "profile_id": "mine_stalker_perception",
  "vision_range": 18,
  "vision_angle": 120,
  "hearing_sensitivity": 1.35,
  "memory_duration": 12,
  "investigation_speed": 1.1,
  "shares_alert": true
}
```

## Material Modifiers

| Material | Footstep | Impact     | Occlusion |
|----------|----------|------------|-----------|
| stone    | médio    | alto       | médio     |
| metal    | alto     | alto       | baixo     |
| wet_soil | baixo    | baixo      | médio     |
| wood     | médio    | médio      | baixo     |
| crystal  | baixo    | alto tonal | baixo     |

## Suspicion Context

Suspeita aumenta quando o mundo não está normal: porta aberta, corpse recente, recurso extraído, luz apagada, item movido ou som repetido. Isso permite stealth emergente sem depender apenas do player visível.

## AI Contract

O sistema de percepção não decide ação final. Ele entrega fatos: `heard_noise`, `saw_target`, `found_evidence`, `lost_target`. A IA decide investigar, atacar, chamar ajuda ou retornar.

## Critérios de Saída Técnica

- Debug mostra último evento percebido.
- Oclusão altera som de forma mensurável.
- Inimigo investiga ruído sem saber automaticamente a posição exata.
- Context suspicion funciona sem ver o jogador.
