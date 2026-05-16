# Cross Input Policy

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define equivalência sistêmica entre VR e flatscreen.

## High Concept

VR é o modo primário e flatscreen é uma tradução de custo equivalente. A política não exige gestos idênticos, exige que risco, tempo, vulnerabilidade e resultado sistêmico sejam comparáveis.

## Princípios

- Mesma simulação.
- Mesmos resultados possíveis.
- Custos equivalentes.
- Nenhum modo com eficiência dominante.
- Testes automatizados de fairness para ações críticas.

## Ação Abstrata

```text
Action {
  action_id
  intent
  vr_execution
  flatscreen_execution
  time_cost
  vulnerability_cost
  noise_cost
  stamina_cost
}
```

## Exemplos

- Golpe VR: movimento físico, alcance e recovery.
- Golpe flatscreen: charge, direção, janela de compromisso e recovery.
- Inventário VR: manipulação corporal.
- Inventário flatscreen: grid sem pausa e tempo de ação.

## MVP

- Mapeamento de ações principais.
- Testes de tempo médio por ação.
- Tuning de vulnerabilidade.

## Critérios de Aceite

- Flatscreen não permite ações instantâneas que em VR são vulneráveis.
- VR não exige esforço excessivo para resultado básico.
- Ambos os modos alimentam os mesmos sistemas de runtime.

## Fairness Metrics

| Métrica | Comparação |
| --- | --- |
| TimeToCompleteAction | VR vs flatscreen dentro de tolerância |
| VulnerabilityWindow | exposição equivalente |
| NoiseProduced | mesmo resultado sistêmico |
| StaminaCost | normalizado por ação |
| FailureChance | mesmo risco lógico |
| Throughput | sem dominância sustentada |

## Action Mapping Example

```json
{
  "action_id": "mine_node",
  "intent": "extract_resource",
  "vr_execution": "physical_swing_repeated",
  "flatscreen_execution": "timed_hold_with_aim_commit",
  "time_cost": 2.4,
  "vulnerability_cost": "high",
  "noise_cost": 30,
  "stamina_cost": 14
}
```

## Test Harness

Testes automatizados devem simular tempo médio de ação, custo de stamina e output. Playtests validam conforto e legibilidade. Se um modo é mais rápido, outro custo precisa compensar ou a ação deve ser retunada.

## Categorias de Ação

- Movimento.
- Combate.
- Inventário.
- Interação canalizada.
- Leitura/UI.
- Crafting/forja.
- Mineração/harvesting.

## Non-Goals

- Reproduzir gesto VR literalmente no flatscreen.
- Dar bônus por modo.
- Balancear só por sensação sem métrica.

## Tolerâncias Iniciais

| Ação | Tolerância alvo |
| --- | --- |
| Mineração de nó comum | ±10% tempo médio |
| Ataque leve | ±8% recovery total |
| Ataque pesado | ±10% commitment window |
| Pegar item | ±15% em contexto seguro |
| Usar consumível | ±10% vulnerabilidade |
| Organizar inventário | flatscreen não pode pausar perigo |

## Auditoria de Ação

Cada ação crítica deve responder:

1. Qual é a intenção sistêmica?
2. Qual é o custo em VR?
3. Qual é o custo equivalente em flatscreen?
4. Que evento de runtime é emitido?
5. Como medir dominância de modo?
6. Qual ajuste é permitido sem quebrar fantasia?

## Edge Cases

| Caso | Regra |
| --- | --- |
| Jogador VR sentado | recalibrar alcance, manter tempo/custo |
| Controle flatscreen com gamepad | mesma ação abstrata |
| Troca de modo fora de run | permitido |
| Troca de modo durante run ativa | proibida. Só permitida em pontos de checkpoint explícitos: Hub, descanso validado ou extração. Tentar trocar fora desses pontos exibe motivo e não executa. |
| Accessibility assist | preserva custo sistêmico |

## Critérios de Saída

- Todas as ações MVP têm mapping.
- Teste registra tempo e custo por modo.
- Nenhuma ação crítica existe em apenas um modo.
- Playtest confirma que diferenças parecem justas.
