# Hub, Shop and Preparation Layer

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define o hub como camada de preparação, retorno, loja, contratos, NPCs e upgrades.

## High Concept

O Hub é a respiração entre runs. Nele o jogador liquida risco, vende recursos, prepara equipamento, aceita contratos, melhora serviços, conversa com NPCs e decide a próxima entrada na dungeon.

## Funções do Hub

- Preparar loadout.
- Vender e comprar.
- Guardar recursos no stash.
- Forjar, cozinhar e alquimizar.
- Ler notes e bestiário.
- Aceitar contratos.
- Interagir com NPCs.
- Aplicar upgrades de loja e serviços.
- Iniciar co-op/party.

## Layout Funcional

| Estação | Função |
| --- | --- |
| Shop Counter | venda, preço, solvência |
| Stash | armazenamento persistente |
| Forge | crafting e reparo |
| Alchemy Table | poções, coatings, reagentes |
| Kitchen | comida e conservação |
| Contract Board | contratos semanais/NPC |
| Shrine | pantheon e ofertas |
| Map Table | preparação e conhecimento |
| Party Gate | formação de grupo |

## Pós-Run Loop

1. Retornar da dungeon.
2. Mostrar summary.
3. Separar vender, guardar, processar ou usar.
4. Atualizar mercado e reputação.
5. Resolver contratos.
6. Reparar/preparar equipamento.
7. Escolher próxima run.

## Hub State

```json
{
  "hub_level": 2,
  "shop_tier": 1,
  "unlocked_stations": ["shop", "stash", "forge", "contract_board"],
  "npc_states": {
    "blacksmith": { "reputation": 12, "available": true }
  },
  "active_upgrades": ["larger_stash_01"]
}
```

## Upgrades

Upgrades devem ser sinks econômicos com impacto claro:

- Stash maior.
- Shop solvency maior.
- Forge heat control.
- Alchemy stabilizer.
- Kitchen preservation.
- Contract board slots.
- NPC explorer access (Alpha ou posterior).

## Social Space

Em co-op, o hub é ponto de reunião e preparação. Decisões compartilhadas como iniciar run, entrar em boss, aceitar contrato party-wide e retornar devem usar regras de party.

## Integrações

- **Economy:** loja e mercado.
- **Contracts:** board e NPCs.
- **Crafting/Alchemy/Cooking:** estações.
- **Party:** formação e loadout.
- **Persistence:** estado permanente do hub.

## MVP

- Shop counter.
- Stash simples.
- Forge básica.
- Alchemy Table apenas para outputs básicos, se contracts de effects/knowledge existirem.
- Contract board.
- Entrada da dungeon.
- 2 NPCs funcionais.

## Critérios de Aceite

- Jogador entende o que fazer após extrair.
- Hub reforça preparo, não vira menu abstrato.
- Upgrades retiram moeda da economia.
- Preparação altera risco da próxima run.
