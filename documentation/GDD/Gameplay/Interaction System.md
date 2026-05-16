# Interaction System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define o contrato unificado de interação.

## High Concept

Interações são verbos sistêmicos que funcionam em VR e flatscreen com custos equivalentes. Abrir, minerar, colher, pegar, empurrar, ler, ativar e usar devem passar por uma interface comum.

## Contrato

```text
CanInteract(actor, target, tool, context) -> InteractionResult
BeginInteraction(...)
UpdateInteraction(deltaTime)
CompleteInteraction(...)
CancelInteraction(...)
```

## Campos de Interação

- `interaction_id`
- `verb`
- `duration`
- `required_tool`
- `input_mode_rules`
- `vulnerability_level`
- `noise_profile`
- `failure_result`
- `success_events`

## Regras

- Interações longas expõem o jogador.
- O modo de input muda execução, não resultado sistêmico.
- Falha deve ser legível.
- Interações críticas em multiplayer são authoritative.

## Integrações

- **Mining/Harvesting:** ações canalizadas e vulneráveis.
- **Notes/Bestiary:** leitura e registro de conhecimento.
- **Dungeon:** portas, alavancas, escadas e objetos.
- **Inventory:** pegar, mover, equipar e descartar.

## MVP

- Interface única.
- 8 verbos base.
- Tempo de interação.
- Cancelamento e falha.

## Critérios de Aceite

- Novas interações não exigem lógica duplicada por modo de input.
- Toda interação relevante pode emitir eventos de ruído, telemetria e progresso.

## Verbos Base

| Verbo | Exemplos | Custo |
| --- | --- | --- |
| Take | pegar item, recolher recurso | tempo curto, slot/carga |
| Use | beber poção, acionar ferramenta | animação/commit |
| Mine | quebrar veio | tempo, ruído, stamina |
| Harvest | extrair parte | tempo, ferramenta, risco |
| Read | note, inscrição, bestiário | vulnerabilidade cognitiva |
| Open | porta, baú, passagem | ruído ou lock |
| Push/Pull | alavanca, objeto físico | stamina/posição |
| Activate | shrine, mecanismo, portal | validação de estado |

## Interaction Definition

```json
{
  "interaction_id": "mine_ore_node",
  "verb": "Mine",
  "duration": 2.4,
  "required_tool_tags": ["pickaxe"],
  "vulnerability_level": "high",
  "noise_profile": "stone_impact_heavy",
  "cancel_policy": "partial_progress_lost",
  "success_events": ["ResourceExtracted", "NoiseEmitted"],
  "failure_events": ["ToolDamaged", "NoiseEmitted"]
}
```

## Paridade VR/Flatscreen

VR pode exigir aproximação física, mão livre e gesto. Flatscreen traduz isso como foco, tempo de canalização, travamento parcial de câmera ou vulnerabilidade. O resultado e o risco devem ser equivalentes.

## Estados

- Available
- Preview
- Committed
- Channeling
- Completing
- Failed
- Cancelled
- Cooldown

## Validação

```text
CanInteract =
  ActorInRange
  && HasRequiredState
  && HasRequiredTool
  && TargetAvailable
  && ModeRulesSatisfied
  && AuthorityValidated
```

## Non-Goals

- Sistema separado por cada tipo de objeto.
- Interações críticas instantâneas por conveniência.
- Prompt de UI substituindo feedback físico sempre.

## Authority Rules

| Interação | Autoridade |
| --- | --- |
| Pegar item comum | host/servidor confirma em co-op |
| Ler note | cliente pode abrir, descoberta persiste via evento |
| Minerar | servidor valida recurso gerado |
| Harvesting | servidor valida parte e qualidade |
| Porta/alavanca | estado de dungeon authoritative |
| Shrine | pantheon/economy authoritative |

## Failure Results

Falha de interação deve produzir resultado consistente:

- Cancelamento sem custo.
- Progresso parcial perdido.
- Ferramenta danificada.
- Ruído emitido.
- Item quebrado.
- Status aplicado.
- Requisito ausente comunicado.

## Interaction Telemetry

- Tempo médio por verbo.
- Cancelamentos por perigo.
- Falhas por ferramenta ausente.
- Diferença VR/flatscreen.
- Interações críticas abandonadas.

## Designer Checklist

Antes de criar uma interação:

1. Qual verbo base ela usa?
2. Qual custo de tempo/vulnerabilidade?
3. Qual feedback de sucesso?
4. Qual falha interessante?
5. Que evento ela emite?
6. Que sistema persiste o resultado?

## Critérios de Saída

- Toda interação MVP usa definition.
- Cancelamento não deixa estado preso.
- Multiplayer não duplica output.
- UI mostra motivo de interação bloqueada.
