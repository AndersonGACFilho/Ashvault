# Run System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define ciclo de vida, regras e resultados de uma run.

## High Concept

A run é a unidade jogável de risco. Ela começa com entrada na dungeon, acumula recursos e conhecimento, e termina por extração, morte, abandono ou transição estrutural.

## Ciclo de Vida

1. Preparação no hub.
2. Entrada na dungeon.
3. Exploração e coleta.
4. Escalada de risco por profundidade, peso e tempo.
5. Extração, morte ou avanço.
6. Liquidação econômica e persistência.

## Estados

- Preparing
- Entering
- Active
- Resting
- Extracting
- Failed
- Completed
- Recovered

## Regras Invioláveis

- Recursos da run têm origem rastreável.
- Morte não apaga progresso meta.
- Extração exige condição ou ponto válido.
- Reset semanal altera dungeon, mercado e oportunidades.

## Integrações

- **Dungeon Generation:** define seed, andar, bioma e gates.
- **Economy:** liquida recursos extraídos.
- **Corpse System:** transforma falha em objetivo recuperável.
- **Telemetry:** mede duração, risco, valor e causa de saída.

## MVP

- Entrada e extração.
- Morte e corpse.
- Liquidação de loot.
- Registro de seed e andar.

## Critérios de Aceite

- Toda run possui começo, fim e resultado inequívoco.
- Valor extraído só entra na economia após extração válida.
- Falhas geram dados úteis para balanceamento.

## Run Data

```json
{
  "run_id": "run_042",
  "player_id": "player_01",
  "week_seed": 7712,
  "start_time": "runtime_clock_18_20",
  "current_floor": 3,
  "biome": "mine",
  "state": "active",
  "extracted_value": 0,
  "carried_value": 486,
  "death_count": 0,
  "flags": ["boss_gate_seen"]
}
```

## Resultado da Run

| Resultado | Condição | Efeito |
| --- | --- | --- |
| Extracted | saiu por ponto válido | recursos entram na economia |
| Dead | vida chegou a zero | corpse e perda parcial |
| Abandoned | saída forçada/meta | liquidação limitada |
| Completed | objetivo estrutural concluído | flags persistentes |
| Crashed | interrupção técnica | crash recovery |

## Liquidação

```text
FinalRunValue =
  ExtractedResourceValue
  + KnowledgeValue
  + QuestValue
  - RepairCosts
  - DebtOrOfferingCosts
```

## Reset Semanal

Reset semanal altera seed, demandas, micro-world states e oportunidades. Ele não apaga progressão meta, mas pode invalidar corpses e contratos temporários conforme regra.

## Legacy Run Policy

Se o reset semanal ocorrer com uma run ativa, a run não é migrada para a nova seed. Ela vira `legacy_run` e mantém o `week_seed`, floor state, loot origin e contratos temporários do momento em que começou.

Regras:

- Run ativa pode ser retomada uma vez no estado legado, se o save for válido.
- Nova semana só cria novas runs depois que a legacy run terminar ou for abandonada.
- Economia e contratos da nova semana não recebem itens da legacy run antes de liquidação válida.
- Corpses e contratos temporários da semana antiga usam expiry explícito; se expirarem, o sistema registra motivo e aplica fallback sem duplicar recurso.
- Depois de `Extracted`, `Dead`, `Abandoned`, `Completed` ou `Crashed` resolvido, a run legado não pode voltar a `Active`.

### Ownership e Pipeline

`RunManager` decide estado da run; `PersistenceManager` preserva snapshots; `Economy`, `Corpse`, `Contracts` e `Multiplayer` apenas reagem aos eventos confirmados.

```text
WeekResetRequested
-> ActiveRunDetected
-> LegacyRunMarked(original_week_seed, run_id)
-> NewWeekPreparedWithoutNewRunStart
-> LegacyRunResolved(Extracted/Dead/Abandoned/Completed/Crashed)
-> WeekRunLockReleased
```

Invariantes:

- Uma legacy run mantém `original_week_seed` até o estado terminal.
- Party não pode iniciar nova run enquanto existir legacy run compartilhada ativa.
- Item de legacy run só entra na economia por `RunLiquidated`.
- Corpse legado preserva owner, floor e expiry; se o floor não existir mais, Death/Corpse resolve fallback.
- Multiplayer reconnect carrega snapshot da legacy run ou força abandono controlado, nunca cria segunda cópia da run.

## Autoridade

RunManager é autoridade para transições de estado. Economy, Dungeon, Corpse e Persistence reagem por eventos, não decidem independentemente se a run terminou.

## Non-Goals

- Sessão infinita sem liquidação.
- Entrada de recurso no mercado antes de extração.
- Reset semanal como wipe de perfil.
- Pausa global de simulação durante decisões de run.

## Run Invariants

```text
RunStarted -> exactly one terminal state
ExtractedValue <= CarriedValue + QuestRewardValue
MarketDelta only after Extracted or Completed
CorpseCreated only after Dead
BossFlagGranted only after eligibility validation
```

## Run Event Log

| Evento | Payload mínimo |
| --- | --- |
| `RunStarted` | player, seed, loadout |
| `FloorEntered` | floor, biome, difficulty |
| `ResourceAcquired` | item, source, quality |
| `ExtractionStarted` | location, carried_value |
| `RunEnded` | reason, duration, value |
| `RunLiquidated` | market_delta, XP, unlocks |

## Preparation Rules

Antes da run, o jogador pode escolher equipamento, consumíveis e contrato. O sistema deve mostrar peso inicial, custo de oportunidade e risco estimado sem revelar informação procedural específica.

## Extraction Rules

Extração deve validar posição, estado de combate, carga, co-op quorum e eventuais gates. Extração sob pressão pode existir, mas precisa ser regra explícita, não fuga de UI.

## Edge Cases

| Caso | Regra |
| --- | --- |
| Sair do jogo em run | save de run ou fallback para checkpoint |
| Cair entre andares | Run permanece ativa, floor atualiza |
| Morrer durante extração | morte vence se confirmada antes do commit |
| Co-op com jogador morto | liquidação individual |
| Reset semanal com run aberta | aplicar `Legacy Run Policy` |
| Troca de modo em run ativa | ver [Cross Input Policy](<../Technical/Cross Input Policy.md>) |

## Critérios de Saída Técnica

- Run sempre termina com exatamente um motivo.
- Liquidação não duplica itens.
- Crash durante extração recupera estado válido.
- Telemetria de run fecha mesmo em morte.
