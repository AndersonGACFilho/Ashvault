# MVP Implementation Plan

> Documento de produção derivado de [Roadmap Content Scope](<Roadmap Content Scope.md>) e [Ashvault Product Hub](<../Ashvault v1.6.md>). Converte o escopo MVP em marcos técnicos executáveis.

## Regra de Autoridade

Este plano organiza implementação. Ele não altera escopo de fase. Em conflito de escopo, corte ou gate, [Roadmap Content Scope](<Roadmap Content Scope.md>) vence.

## Objetivo MVP

Entregar um loop jogável fechado:

```text
Hub -> Run -> Dungeon 3-5 floors -> Mining/Combat/Carry/Notes -> Extraction/Death -> Economy/Upgrade -> Boss Gate
```

## Marcos

| Marco | Objetivo | Entregas mínimas | Saída |
| --- | --- | --- | --- |
| M0 - Core Contracts + Runtime Shell | Criar base modular | IDs, EventBus, tick order, definitions, save envelope, telemetry schema | sistemas conseguem trocar eventos sem acesso interno |
| M1 - Run + Dungeon Floor Skeleton | Provar entrada e floor básico | RunStarted, seed semanal, floor gerado, spawn, extraction placeholder, anchors | jogador entra e sai de um floor sem economia |
| M2 - Interaction + Inventory + Mining | Provar coleta física/abstrata | Interaction contract, pickup, carry, mining node, item instance com origin | minério entra no inventário com origem válida |
| M3 - Extraction + Economy + Hub Shop | Fechar valor | extraction validada, venda, shop counter, stash simples, market delta | item só entra na economia após extração |
| M4 - Combat + Noise + 2 inimigos | Criar pressão mínima | dano, stamina, stagger, NoiseAuthority MVP, enemy alert/investigate/attack | combate e ruído alteram risco de run |
| M5 - Corpse + Save/Load + Legacy Run | Proteger falhas S0/S1 | morte, corpse, recovery, crash recovery, legacy_run policy | save/load não duplica nem perde economia validada |
| M6 - Boss Gate MVP | Validar progressão | 1 boss, eligibility, LayerLockValidator, reward idempotente | boss abre gate sem bypass e sem reward duplicado |
| M7 - QA/Fairness/Telemetry | Fechar MVP testável | 100 seeds, event schemas, VR/Flat parity checks, run summary, death causes | build passa critérios mínimos de saída MVP |

## Dependências Críticas

| Contrato | Deve existir antes de |
| --- | --- |
| EventBus + schema/version | Telemetry, Persistence, Run |
| Save envelope + migration | Economy, Corpse, Legacy Run |
| LayerLockValidator | Dungeon verticality, rope/fall, boss gate |
| NoiseAuthority | AI Detection, stealth, enemy alert |
| UnifiedEffectsRuntime | Combat status, hazards, alchemy MVP |
| KnowledgeRuntime | Notes, Bestiary, rumors, alchemy formulas |
| CampEligibilityToken | Camp dentro da dungeon |

## Backlogs Técnicos

| Marco | Backlog |
| --- | --- |
| M0 | [M0 Core Contracts Backlog](<M0 Core Contracts Backlog.md>) |

## Não Entra no MVP

- Segundo bioma.
- NPC Explorer.
- Arcane Magic em produção.
- Field alchemy, centrifugação e coatings complexos.
- GOAP/HTN/MORL.
- Multiplayer amplo.
- Economia autônoma ou simulação social profunda.

## Critérios de Saída

- Run de 15-30 minutos com extração, morte e venda.
- 3-5 floors da mina sem softlock conhecido.
- 1 boss gate com unlock persistente e reward idempotente.
- Nenhum item vendável sem `origin_id` e `item_instance_id`.
- Save/load passa crash tests de venda, morte, transição e boss.
- `legacy_run` impede reset semanal de duplicar economia.
- VR e flatscreen ficam dentro da tolerância de fairness definida.
- Telemetry local registra eventos críticos com schema/version.
