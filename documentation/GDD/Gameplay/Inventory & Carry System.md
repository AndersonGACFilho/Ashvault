# Inventory & Carry System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define peso, carga, inventário físico/grid e penalidades de transporte.

## High Concept

Inventário é uma decisão de sobrevivência, não apenas armazenamento. Levar mais recursos aumenta valor potencial, mas reduz mobilidade, stealth, stamina e segurança da extração.

## Regras Centrais

- Peso total afeta velocidade, stamina, ruído e risco de queda.
- VR usa inventário físico em slots corporais e mochila.
- Flatscreen usa grid sem pausa e com tempo de manipulação.
- Itens volumosos e pesados não devem desaparecer em abstrações sem custo.
- Overload extraction é permitido como decisão extrema.

## Fórmula de Penalidade

```text
EncumbranceRatio = CurrentWeight / CarryCapacity

MovePenalty = max(0, EncumbranceRatio - 0.6) * MovePenaltyScale
NoisePenalty = EncumbranceRatio * ArmorAndPackNoise
StaminaPenalty = EncumbranceRatio * StaminaDrainScale
```

## Categorias

- Equipado
- Slot rápido
- Mochila
- Bolsa de recursos
- Contêiner especial
- Item carregado nas mãos

## Integrações

- **Mining:** minérios pesados tensionam retorno versus exploração profunda.
- **Harvesting:** partes orgânicas podem ser volumosas e perecíveis.
- **Economy:** valor transportado define risco econômico da run.
- **Corpse System:** itens da run podem ser perdidos e recuperáveis no corpse.
- **Noise & Perception:** carga altera assinatura acústica.

## MVP

- Peso por item.
- Capacidade base e modificadores.
- Penalidade de movimento e stamina.
- Slots rápidos.
- Overload extraction simples.

## Critérios de Aceite

- O jogador sente a diferença entre uma run leve e uma run carregada.
- Gerenciar inventário em combate ou perigo tem custo.
- Nenhum modo de input permite manipulação instantânea sem vulnerabilidade.

## Classes de Item

| Classe | Exemplos | Stack | Peso | Regra especial |
| --- | --- | --- | --- | --- |
| Resource | minério, osso, erva | sim | variável | origem rastreável |
| Tool | picareta, faca, frasco | não | médio | durabilidade |
| Consumable | comida, poção | limitado | baixo | uso durante perigo |
| Equipment | arma, armadura, bolsa | não | alto | altera stats |
| Knowledge | note, mapa, bestiário | não | baixo | pode persistir meta |
| Quest/Key | chave, relíquia | não | variável | regras de perda próprias |

## Estados de Carga

| Estado | Ratio | Efeito |
| --- | --- | --- |
| Light | 0.0-0.4 | sem penalidade |
| Packed | 0.4-0.7 | ruído leve, stamina normal |
| Heavy | 0.7-1.0 | velocidade e stamina reduzidas |
| Overloaded | 1.0-1.3 | sem sprint, ruído alto, queda pior |
| Critical | >1.3 | só movimento lento, interação limitada |

## Manipulação por Modo

VR usa zonas corporais: cintura, ombro, peito, mochila e mãos. Flatscreen usa grid em tempo real com cursor/foco, sem pausa grátis. Itens de acesso rápido precisam de slots equivalentes em ambos os modos.

## Modelo de Dados

```json
{
  "item_instance_id": "run_042_iron_ore_017",
  "definition_id": "iron_ore",
  "origin": {
    "run_id": "run_042",
    "floor": 2,
    "node_id": "ore_node_2_14"
  },
  "quantity": 3,
  "quality": 0.72,
  "weight": 4.5,
  "decay": 0.0,
  "flags": ["sellable", "forgeable"]
}
```

## Pipeline

1. Item é criado por fonte válida.
2. Capacidade e slots são avaliados.
3. Se aceitar, item entra em container.
4. Peso recalcula penalidades.
5. Eventos de UI, áudio e telemetria são emitidos.
6. Em morte, domínio da run decide perda/retention.

## Anti-Exploit

- Não há venda de item sem origem.
- Drop/pickup não reseta decay, origem ou qualidade.
- Troca multiplayer preserva owner, source e transaction log.
- Containers não podem reduzir peso sem definição explícita.

## Non-Goals

- Inventário infinito.
- Pausa segura para reorganização em dungeon.
- Sistema de Tetris complexo demais no MVP.
- Teleporte de item entre hub e run sem regra.
