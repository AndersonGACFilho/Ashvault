# Boss Gates, Vertical Locks and Floor Progression

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define gates, locks verticais, camadas seladas, queda entre andares e progressão estrutural.

## High Concept

Progressão em Ashvault é vertical e selada. O jogador pode cair, explorar, extrair e retornar, mas avanço estrutural profundo exige vencer gates, entender camadas e respeitar locks da dungeon.

`LayerLockValidator` é a autoridade técnica para essa regra. Boss Gates define a intenção de progressão; Dungeon, rope, fall, dig, hazards, alchemy, magic e AI traversal devem consumir o mesmo validator.

## Floor Bands

| Banda | Função |
| --- | --- |
| Surface/Hub | preparação e retorno |
| Floors 1-3 | onboarding sistêmico e mining base |
| Gate Floor | boss ou lock estrutural |
| Sealed Layer | novo bioma/regra após gate |
| Deep Layer | risco alto, corrupção e recursos raros |

## Boss Gate

Boss gate é uma barreira persistente ligada a:

- Elegibilidade individual.
- Boss derrotado.
- Chave, ritual ou contrato.
- Estado semanal.
- Save persistente.

## Gate Data

```json
{
  "gate_id": "mine_gate_01",
  "required_flags": ["boss_ash_wrought_keeper_defeated"],
  "required_items": [],
  "party_policy": "individual_eligibility",
  "opens_for_week": true,
  "persists_unlock": true,
  "fallback_if_invalid": "sealed_with_lore_feedback"
}
```

## Vertical Locks

Locks verticais impedem bypass por queda, co-op ou física. Eles precisam ser diegéticos e validados:

- Shaft colapsado.
- Barreira arcana.
- Porta de pressão.
- Elevador quebrado.
- Escada selada.
- Aether turbulence.

## Queda Entre Andares

Queda pode mover jogador para andar inferior, mas não deve atravessar gate estrutural sem regra explícita.

```text
CanFallBypass =
  TargetFloorGenerated
  && NoStructuralGateBetween
  && LandingZoneValid
  && DungeonAllowsVerticalDrop
```

## Party and Gate Sync

Party pode ter membros com progressão diferente. Gate deve declarar:

- quem pode entrar;
- quem pode ajudar;
- quem recebe reward;
- quem recebe unlock;
- como escada/common load trata membros inelegíveis.

## Weekly Reset

Reset semanal pode mudar rotas até gates, mas não deve apagar unlock persistente. Gates podem fechar fisicamente na nova seed até serem alcançados novamente, mantendo o direito estrutural do jogador.

## Integrações

- **Boss System:** derrota e rewards.
- **Party System:** elegibilidade co-op.
- **Dungeon Generation:** valida rotas e locks.
- **Persistence:** flags estruturais.
- **Environmental Hazards:** quedas e shafts.
- **Narrative:** camadas seladas e lore dos gates.

## Edge Cases

| Caso | Regra |
| --- | --- |
| Player cai atrás de gate | reposicionar ou bloquear por vertical lock |
| Co-op com membro sem unlock | entrada limitada ou suporte sem reward |
| Boss derrotado e crash antes de save | reward transaction idempotente |
| Gate em seed inválida | fallback para layout validado |
| Corpse atrás de gate | só se owner tem acesso |

## MVP

- 1 boss gate.
- 1 vertical lock.
- Unlock persistente.
- Validação anti-bypass por queda.
- Regras de co-op simples.

## Critérios de Aceite

- Gate não é bypassado por física.
- Unlock persiste após reset semanal.
- Party entende elegibilidade.
- Queda vertical continua perigosa sem quebrar progressão.
