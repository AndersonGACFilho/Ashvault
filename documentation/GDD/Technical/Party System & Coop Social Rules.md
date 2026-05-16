# Party System and Co-op Social Rules

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define formação de party, loot sharing, shared discovery, queda, escadas, rescue, comunicação e boss gates.

## High Concept

Party é o contrato social da run. Co-op precisa permitir colaboração, resgate e divisão de funções sem quebrar risco individual, economia fechada ou progressão por elegibilidade.

## Formação de Party

- Convite no hub.
- Validação de versão/save.
- Definição de líder/host.
- Escolha de contrato compartilhado.
- Pronto por jogador.
- Entrada conjunta ou join limitado por regra.

## Party Data

```json
{
  "party_id": "party_042",
  "leader": "player_01",
  "members": [
    { "player_id": "player_01", "role": "miner", "ready": true },
    { "player_id": "player_02", "role": "guard", "ready": true }
  ],
  "loot_policy": "source_owner_with_trade",
  "shared_contracts": ["weekly_ore_order_007"]
}
```

## Roles

Roles são sociais/operacionais, não classes rígidas:

- Miner.
- Guard.
- Scout.
- Harvester.
- Support.
- Trader/Carrier.

## Loot Sharing

| Política | Uso |
| --- | --- |
| Source Owner | dono é quem extrai/pega |
| Party Pool | loot vai para pool até extração |
| Contract Shared | item conta para contrato coletivo |
| Boss Eligibility | reward por elegibilidade individual |

## Shared Discovery

Notes, bestiary e mapa podem ser compartilhados durante a run, mas persistência individual precisa declarar o que cada jogador realmente descobriu, recebeu ou copiou.

## Queda e Separação por Andar

Queda vertical pode separar party. Regras:

- Jogador que cai muda de andar fisicamente.
- Party recebe ping/sinal de separação.
- Loading por escada não teleporta automaticamente quem caiu, salvo regra de quorum.
- Corda, atalho ou rescue podem reunificar.
- Jogador isolado mantém risco e loot próprio.

## Escada e Common Load

Transição por escada exige quorum, timer ou voto. Jogador distante pode ser puxado apenas se não estiver em combate crítico e se a regra de coesão permitir.

## Rescue e Revive

Revive completo fica fora do MVP se ameaçar corpse system. Rescue pode existir como arrastar aliado downed, recuperar corpse, soltar corda ou abrir rota.

## Comunicação

- Ping contextual.
- Marcação de recurso/perigo.
- Voto para retorno.
- Pedido de ajuda.
- Sinal de queda/separação.

## Boss Gate Sync

Boss gates validam elegibilidade por jogador. Party pode entrar com membros inelegíveis apenas se a regra do boss permitir suporte sem reward estrutural.

## Integrações

- **Multiplayer Save & Coop:** sincronização e autoridade.
- **Contracts:** objetivos compartilhados.
- **Death/Corpse:** recovery de aliado.
- **Dungeon:** escadas, quedas, locks.
- **Economy:** loot ownership e divisão.

## MVP

- Party de 2.
- Ready check.
- Loot owner.
- Ping simples.
- Extração conjunta.
- Boss eligibility individual.

## Critérios de Aceite

- Co-op não duplica loot.
- Jogador que cai separado entende onde está e como voltar.
- Party pode coordenar retorno sem pausar dungeon.
- Boss gate não permite bypass de progressão.

