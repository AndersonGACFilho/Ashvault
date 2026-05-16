# Boss System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define bosses, fases, gates e anti-carry.

## High Concept

Bosses são testes de domínio sistêmico, não apenas barras de vida maiores. Cada boss valida aprendizado de bioma, combate, recursos, movimento e preparação econômica.

## Boss Template

- `boss_id`
- `gate_floor`
- `biome_context`
- `phase_count`
- `attack_sets`
- `weakness_rules`
- `anti_carry_rules`
- `reward_table`
- `failure_consequence`

## Fases

1. Leitura e reconhecimento.
2. Escalada com nova ameaça.
3. Execução sob pressão máxima.

## Anti-Carry

- Instância de boss considera progresso individual.
- Recompensas e flags são atribuídas por elegibilidade.
- Jogadores fora da faixa de progressão não devem trivializar o gate.

## Integrações

- **Multiplayer Save & Coop:** instanciamento e elegibilidade.
- **Bestiary:** conhecimento obtido em tentativas ou notas.
- **Economy:** preparação consome recursos.
- **Pantheon/Magic:** vantagens com custo e limites.

## MVP

- 1 boss de gate.
- 3 fases simples.
- Reward table.
- Registro de vitória por jogador.

## Critérios de Aceite

- Boss testa sistemas já ensinados.
- Co-op não permite bypass de progressão.
- Vitória libera progressão de forma persistente e auditável.

## Boss Runtime Data

```json
{
  "boss_instance_id": "boss_mine_gate_run_042",
  "boss_id": "ash_wrought_keeper",
  "eligible_players": ["player_01"],
  "phase": 2,
  "health_ratio": 0.58,
  "arena_state": "venting_ash",
  "reward_claimed_by": [],
  "attempt_seed": 42091
}
```

## Estrutura de Fase

| Fase | Objetivo  | Mecânica                               | Teste                 |
|------|-----------|----------------------------------------|-----------------------|
| 1    | leitura   | ataques telegráficos, fraqueza visível | observação e defesa   |
| 2    | adaptação | arena muda, adds ou hazard             | mobilidade e controle |
| 3    | execução  | janelas curtas, alto custo de erro     | domínio do loop       |

## Anti-Carry Detalhado

```text
RewardEligibility =
  HasUnlockedGate
  && ParticipatedAboveThreshold
  && SurvivedOrPaidCorpseCost
```

Jogador inelegível pode ajudar com dano reduzido ou suporte limitado, mas não recebe flag estrutural. Isso permite co-op sem destruir progressão.

## Eligibility Thresholds

Valores iniciais ajustáveis por balanceamento. Estes thresholds definem quem recebe reward estrutural, flag de gate e progressão persistente após a derrota do boss.

| Termo | Definição inicial |
| --- | --- |
| `HasUnlockedGate` | O jogador tem acesso ao gate do boss: possui o `required_flag` do `gate_id` ou cumpriu a condição equivalente antes de entrar na arena. |
| `ParticipatedAboveThreshold` | O jogador causou ao menos 10% do dano total ao boss **OU** esteve na arena por pelo menos 60% da duração da luta. |
| Suporte sem dano direto | Conta para `ParticipatedAboveThreshold` se o jogador esteve presente na arena por pelo menos 60% da luta. Exemplo: healer, buffer, tank defensivo ou suporte de controle. |
| `SurvivedOrPaidCorpseCost` | O jogador sobreviveu até a resolução do boss ou pagou o custo definido pelo sistema de corpse/recovery para validar participação pós-morte. |

Não existe regra de "overleveled" por stats. O anti-carry é exclusivamente por progressão de gate: jogador sem `required_flag` não recebe reward estrutural, mesmo que esteja forte o suficiente em atributos, equipamento ou skills.

A fórmula usa apenas `HasUnlockedGate` para cobrir anti-carry por progressão de gate.

## Reward Table

- Gate unlock persistente.
- Recurso raro com origem de boss.
- Note/bestiary entry.
- Material de crafting.
- Afinidade divina ou mutação especial, se aplicável.

## Failure Handling

Falhar contra boss deve preservar aprendizado: bestiário, notes de padrões, dano causado, causa de morte e custo econômico. O boss não deve resetar aprendizado informacional sem motivo.

## Testes Obrigatórios

- Boss não nasce sem arena válida.
- Phase transition é determinística em multiplayer.
- Reward não duplica em reconexão.
- Jogador inelegível não recebe gate flag.

## Non-Goals

- Boss como DPS race puro.
- One-shot ilegível.
- Carry irrestrito.
- Boss obrigatório antes de ensinar os sistemas cobrados.

## Arena Requirements

| Requisito                       | Motivo                         |
|---------------------------------|--------------------------------|
| Entrada clara                   | reduz morte confusa            |
| Espaço de leitura               | fase 1 precisa ensinar padrões |
| Cobertura/rotas                 | suporte a builds diferentes    |
| Saída bloqueada justificada     | evita cheese sem parecer bug   |
| Pontos de recuperação limitados | pacing                         |
| Debug bounds                    | impedir queda/softlock         |

## Boss Telemetry

- Tentativas por jogador.
- Morte por fase.
- Dano recebido por ataque.
- Tempo por fase.
- Uso de consumíveis.
- Modo de input.
- Coop eligibility.
- Clear rate após 1, 3 e 5 tentativas.

## Reward Validation

```text
GrantReward =
  BossDefeated
  && PlayerEligible
  && RewardNotPreviouslyClaimed
  && SaveTransactionCommitted
```

## Failure Learnings

Após uma derrota, o jogador pode receber note/bestiary parcial se observou ou sobreviveu a uma fase. Isso reforça o loop de conhecimento sem baratear a vitória.

## Critérios de Saída

- Boss tem pelo menos 3 ataques legíveis.
- Cada fase muda decisão, não só número.
- Reward é idempotente.
- Arena não produz softlock em queda ou morte.
