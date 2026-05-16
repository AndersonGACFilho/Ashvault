# Telemetry Balance System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define telemetria, MORL e balanceamento orientado por dados.

## High Concept

Telemetria deve medir decisões, riscos e resultados. O objetivo não é vigiar cliques; é entender quando os sistemas produzem decisões fortes, exploits ou fricção indevida.

## Métricas de Run

- Duração.
- Andar máximo.
- Valor extraído.
- Valor perdido.
- Causa de morte.
- Peso médio.
- Ruído gerado.
- Inimigos alertados.
- Recursos por fonte.
- Uso de skill, magia, comida e alquimia.

## Decision Hooks

```text
if DeathRate(floor) > threshold:
  lower spawn pressure or add readable counterplay

if EconomyInflation > threshold:
  increase sinks or reduce high-volume sale value
```

## MORL

Successor features devem representar vetores como risco, lucro, combate, stealth, exploração e preparação.

## MVP

- Eventos locais em arquivo.
- Dashboard simples externo ou CSV.
- Thresholds manuais.

## Critérios de Aceite

- Cada métrica tem decisão de design associada.
- Dados não substituem playtest qualitativo.
- Telemetria não coleta dados pessoais desnecessários.

## Telemetry Event

```json
{
  "event": "run_ended",
  "schema": 1,
  "run_id": "run_042",
  "floor_max": 3,
  "duration_seconds": 1840,
  "end_reason": "death",
  "carried_value": 486,
  "extracted_value": 0,
  "death_cause": "fall_damage",
  "input_mode": "vr"
}
```

## Event Schema Migration

Migração de eventos é política de telemetria, não de save. Um evento antigo pode ser transformado para análise, mas nunca deve alterar gameplay, economia, run state ou recovery.

Regras:

- `schema` é obrigatório em todo evento persistido.
- Cada schema aceito precisa de parser ou migrator explícito.
- Evento desconhecido entra em quarantine e é excluído de métricas derivadas.
- Mudança de semântica exige novo schema.
- Dashboards devem registrar quais versões de schema alimentaram cada métrica.

## Métricas com Ação

| Métrica | Sinal ruim | Ação possível |
| --- | --- | --- |
| DeathRate floor 1 | alto | tutorial, inimigo, healing |
| ExtractedValue median | baixo | valor, peso, extração |
| CorpseRecoveryRate | muito baixo | readability, spawn pressure |
| ModeEfficiencyDelta | alto | cross-input tuning |
| InflationIndex | alto | sinks, demanda, preço |
| BuildDominance | alto | perks, counters, custo |

## Privacidade

No MVP, telemetria pode ser local. Qualquer coleta remota futura deve evitar dados pessoais, IDs externos e texto livre do jogador.

## Pipeline de Análise

1. Emitir evento.
2. Validar schema.
3. Agregar por run.
4. Comparar thresholds.
5. Gerar hipótese de balanceamento.
6. Confirmar em playtest.

## Non-Goals

- Ajuste automático invisível sem revisão.
- Coleta de dados pessoais.
- Métrica sem dono ou decisão.

## Schemas Prioritários

| Evento | Quando emite | Uso |
| --- | --- | --- |
| `run_started` | entrada na dungeon | funil |
| `floor_entered` | troca de andar | pacing |
| `resource_extracted` | mineração/harvest | economia |
| `enemy_alerted` | detecção | stealth/ruído |
| `player_died` | morte | dificuldade |
| `corpse_recovered` | recuperação | risco recuperável |
| `item_sold` | venda | inflação |
| `boss_attempted` | início de boss | gate balance |
| `boss_defeated` | vitória | progressão |

## Derived Metrics

```text
ExtractionRate = ExtractedRuns / StartedRuns
AverageCarriedValueAtDeath = Sum(CarriedValueOnDeath) / DeathCount
ModeEfficiencyDelta = Abs(VRMedianValuePerMinute - FlatMedianValuePerMinute)
InflationIndex = TotalCurrencyGenerated / TotalCurrencySinks
```

## Threshold Policy

Thresholds devem ser versionados. Um ajuste de balanceamento precisa registrar qual métrica motivou a mudança, qual hipótese foi testada e qual resultado esperado.

## Dashboard MVP

- Runs por resultado.
- Morte por andar.
- Valor extraído por minuto.
- Valor perdido por causa de morte.
- Comparativo VR/flatscreen.
- Top fontes de moeda.
- Top perks usadas.

## Critérios de Saída

- Eventos gerados em build local.
- CSV/JSON analisável sem ferramenta proprietária.
- Pelo menos 5 hooks de decisão documentados.
- Nenhuma métrica exige dado pessoal.
