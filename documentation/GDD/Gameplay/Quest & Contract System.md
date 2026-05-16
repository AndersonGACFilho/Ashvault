# Quest and Contract System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define contratos, objetivos, falha, recompensa, reputação e integração com economia/NPCs.

## High Concept

Contratos dão direção sem transformar Ashvault em RPG linear. Eles são demandas econômicas, sociais ou rituais que orientam runs, reforçam a loja e criam decisões de risco.

## Tipos de Contrato

| Tipo | Exemplo | Validação |
| --- | --- | --- |
| Collection | entregar minério raro | item origin + quantidade |
| Harvest | glândula de criatura | species + part + qualidade |
| Crafting | forjar lâmina específica | recipe + quality |
| Alchemy | preparar elixir | properties + toxicity |
| Boss | derrotar gate boss | eligibility + proof |
| Rescue | recuperar corpse/NPC | localização + estado |
| Scout | mapear sala/bioma | discovery events |
| Weekly | demanda do mercado | seed semanal |

## Contract Definition

```json
{
  "contract_id": "weekly_ore_order_007",
  "type": "collection",
  "issuer": "blacksmith_npc",
  "expires_on_week": 7,
  "requirements": [
    { "item_tag": "ore.iron", "quantity": 8, "min_quality": 0.5 }
  ],
  "rewards": {
    "currency": 240,
    "reputation": 6,
    "unlock": "forge_upgrade_01"
  },
  "failure": {
    "reputation_delta": -2
  }
}
```

## Lifecycle

1. Contrato é gerado por hub, NPC, mercado, shrine ou evento semanal.
2. Jogador aceita, ignora ou negocia.
3. Run produz progresso validável.
4. Entrega ocorre no hub ou no ponto de contrato.
5. Sistema valida origem, qualidade e prazo.
6. Recompensa, reputação e mercado são atualizados.

## Falha

Falha pode ocorrer por expiração, item errado, qualidade insuficiente, morte de NPC, boss não elegível ou contrato abandonado. Falha deve gerar consequência proporcional, não necessariamente punição pesada.

## Reputação

Reputação altera preço, disponibilidade, confiança de NPC, contratos raros e serviços desbloqueáveis. Não substitui moeda; é acesso e relação.

## Party Rules

Contratos podem ser individuais, compartilhados ou party-wide. Recompensa deve declarar se divide, duplica por elegibilidade ou paga apenas ao dono.

## Integrações

- **Economy:** contratos são fonte controlada de moeda e sinks.
- **NPC System:** emissores têm preferências e reputação.
- **Loot:** valida origem dos itens.
- **Notes/Bestiary:** contratos de conhecimento.
- **Party System:** entrega e recompensa compartilhada.

## Anti-Exploit

- Não aceitar entrega de item comprado do próprio mercado sem exceção.
- Contrato semanal não pode ser re-rolled infinitamente.
- Recompensa não duplica em reconnect.
- Item entregue é consumido atomicamente.

## MVP

- 3 contratos semanais.
- 2 contratos de NPC.
- Validação por item origin.
- Reputação simples por emissor.

## Critérios de Aceite

- Contrato explica objetivo, prazo, risco e recompensa.
- Entrega inválida tem motivo claro.
- Recompensa é transação idempotente.
- Contratos orientam runs sem obrigar caminho único.

