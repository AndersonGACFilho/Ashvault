# Player System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define o jogador como entidade de simulação.

## High Concept

O player é o ponto de convergência de input, corpo, estado, inventário, dano, progressão e persistência. Todo sistema deve interagir com o jogador por contratos claros, não por acesso implícito a estado global.

## Dados Core

- Identidade e perfil.
- Modo de input atual.
- Atributos base.
- Vida, stamina, carga cognitiva e exposição.
- Inventário e equipamento.
- Estado de run.
- Skills, afinidades e modificadores ativos.

## State Machine

- Idle
- Exploring
- Interacting
- Combat
- Overloaded
- Resting
- Downed
- Dead
- Extracting

## Pipeline de Dano

```text
IncomingDamage
-> ValidateHit
-> ApplyArmorAndResistance
-> ApplyStatusModifiers
-> UpdateHealth
-> EmitDamageEvents
-> CheckDownedOrDeath
```

## Integrações

- **Combat:** consome estado, stamina e equipamento.
- **Inventory:** altera carga e mobilidade.
- **Run System:** controla entrada, morte e extração.
- **Persistence:** separa meta persistente de dados mutáveis da run.
- **Telemetry:** registra escolhas, falhas e thresholds.

## MVP

- State machine mínima.
- Vida, stamina, peso e morte.
- Contrato de input abstrato.
- Eventos de dano e estado.

## Critérios de Aceite

- Sistemas externos não alteram player sem evento ou API explícita.
- Mudanças de estado são auditáveis.
- VR e flatscreen compartilham o mesmo estado lógico.

## Player Runtime Data

```json
{
  "player_id": "player_01",
  "input_mode": "vr",
  "health": 78,
  "stamina": 42,
  "cognitive_load": 0.18,
  "encumbrance_ratio": 0.73,
  "current_state": "exploring",
  "active_status": ["minor_bleeding"],
  "active_modifiers": ["earth_carry_01"],
  "run_id": "run_042"
}
```

## Cognitive Load Contract

`cognitive_load` é estado runtime do `PlayerSystem`, clamped entre `0.0` e `1.0`. Ele representa tensão mental/foco operacional, não progressão permanente nem recurso econômico.

Fontes permitidas:

- aumenta por leitura sob pressão, medo, mutação, trauma, certos hazards, excesso de estímulo e magia arcana quando a trilha P&D estiver ativa;
- reduz por Breath, Camp, Hub Rest, alguns efeitos de foco e preparação no Hub;
- é salvo apenas dentro de `RunState` quando afeta decisões ativas.

Fórmula MVP:

```text
CognitiveLoadNext =
  Clamp01(
    CognitiveLoadCurrent
    + StimulusDelta
    + SustainedActionDelta
    + StatusDelta
    - RecoveryDelta
  )
```

Escritores permitidos no MVP:

| Fonte | Delta inicial | Observação |
| --- | --- | --- |
| leitura/anotação sob ameaça | `+0.03` a `+0.10` | depende de threat e duração |
| fear/trauma/hazard mental | `+0.05` a `+0.20` | via `UnifiedEffectsRuntime` |
| mutação/corrupção leve | `+0.02` a `+0.08` | se o sistema estiver ativo |
| Breath completo | `-0.05` a `-0.12` | não remove trauma severo |
| Camp válido | `-0.15` a `-0.30` | exige `CampEligibilityToken` |
| Hub Rest | reset parcial/total | conforme custo/tempo do Hub |

Arcane Magic não é escritor MVP obrigatório. Se estiver apenas em P&D, seus deltas não entram em builds de MVP.

Thresholds iniciais:

| Valor | Estado | Efeito MVP |
| --- | --- | --- |
| `0.00-0.39` | stable | sem penalidade |
| `0.40-0.69` | strained | feedback de foco e pequena penalidade de leitura/interações cognitivas |
| `0.70-0.89` | overloaded | interações longas, leitura e decisões sob pressão ficam mais arriscadas |
| `0.90-1.00` | breaking | risco alto de falha, backlash ou interrupção |

Se um sistema não usa esses thresholds, ele não deve escrever em `cognitive_load`.

## Autoridade de Alteração

| Domínio | Pode alterar | Via |
| --- | --- | --- |
| Combat | vida, stamina, stagger | damage pipeline |
| Inventory | peso, slots, equipamento | inventory transaction |
| Run | estado de run, extração, morte | run transition |
| Rest | cognitive_load, stamina, HP conforme tipo | rest recovery pipeline |
| Status Effects | modificadores temporários | UnifiedEffectsRuntime |
| Pantheon | modificadores ativos | effect stack |
| Persistence | snapshot/load | save contract |

## Invariantes

- Vida não pode ficar abaixo de zero sem estado `Dead` ou `Downed`.
- Stamina não pode ser negativa.
- Estado de input não altera dados persistentes por si só.
- Inventário de run e meta inventário são domínios separados.
- Toda morte emite causa e contexto.

## Estados e Transições

```text
Exploring -> Combat: enemy threat or attack action
Combat -> Exploring: no active threat and recovery complete
Exploring -> Interacting: valid interaction begin
Interacting -> Exploring: complete/cancel
Any -> Dead: health <= 0 and no downed override
Any -> Extracting: valid extraction condition
```

## Testes Obrigatórios

- Dano simultâneo não duplica morte.
- Troca de VR para flatscreen preserva estado lógico.
- Load em estado inválido recupera último checkpoint válido.
- Overload altera movimento sem quebrar interação essencial.

## Non-Goals

- Player controller com lógica de economia embutida.
- Estado global mutável por qualquer manager.
- Diferenças de atributo base por modo de input.

## Status Effects

| Status | Fonte | Efeito |
| --- | --- | --- |
| Bleeding | combate, trap | perda gradual de vida |
| Exhausted | stamina baixa | recovery pior |
| Encumbered | inventário | movimento/ruído |
| Poisoned | harvesting/alchemy | dano ou visão alterada |
| Focused | comida/magia | carga cognitiva menor |
| Mutated | dungeon/corrupção | bônus e custo |

## Input Mode Switching

Troca de modo deve ser tratada como mudança de interface, não de personagem. Ver [Cross Input Policy](<../Technical/Cross Input Policy.md>) — Input Mode Switching.

## Player Event Contract

```json
{
  "event_type": "PlayerStateChanged",
  "player_id": "player_01",
  "from": "exploring",
  "to": "combat",
  "reason": "enemy_engaged",
  "timestamp": 248.2
}
```

## Edge Cases

| Caso | Resposta |
| --- | --- |
| Dano fatal e extração simultânea | ordem de tick define vencedor |
| Load com estado Interacting | cancelar interação e voltar a Exploring |
| Stamina zero no ar | queda resolve antes de recovery |
| Peso acima do limite após pickup | entra Overloaded/Critical |

## Critérios de Saída

- State changes logadas em debug.
- Morte é idempotente.
- Status effects aplicam/removem determinísticamente.
- Player não conhece internals da economia.
