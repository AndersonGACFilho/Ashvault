# Skill Tree System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define progressão híbrida por uso e especialização emergente. Este documento é autoritativo para a decisão de **Faith no MVP**; [Pantheon System](<Pantheon System.md>) consome esse ramo mínimo quando shrine/Pantheon entra no MVP.

## High Concept

O jogador não escolhe uma classe fixa no início. A build emerge das ações repetidas: minerar melhora mineração, lutar melhora combate, negociar melhora trade e assim por diante.

## Ramos

- Mining
- Harvesting
- Utility
- Combat
- Trade
- Faith

## Regra de XP

```text
SkillXP =
  ActionDifficulty
  * RiskModifier
  * QualityOutcomeModifier
  * DiminishingReturnModifier
```

## Princípios

- Progressão vem de ações verificáveis.
- Exploits de repetição segura devem ter retorno decrescente.
- Skills desbloqueiam eficiência, opções e tolerância a risco, não invencibilidade.
- Ramos devem conversar com economia, dungeon, combate e deuses.

## Integrações

- **Mining/Harvesting:** XP por extração real e qualidade.
- **Combat:** XP por ameaça vencida, defesa, precisão e risco.
- **Economy:** trade evolui por venda, negociação e gestão de loja.
- **Pantheon:** faith evolui por ofertas, escolhas e custos aceitos.
- **Telemetry:** mede dominância de builds e loops exploráveis.

## MVP

- 5 ramos ativos: Mining, Combat, Utility, Trade e Faith.
- Mining, Combat, Utility e Trade têm 5 perks por ramo.
- Faith entra em escopo mínimo com apenas 2 perks: `Devoted` e `Ritual Efficiency`.
- Faith ganha XP por oferta com custo real em shrine/Pantheon.
- Faith não usa retorno decrescente agressivo no MVP, para não punir jogadores que explorarem shrine cedo.
- XP por ação com retorno decrescente.
- UI mínima diegética ou livro de progresso.

## Faith MVP

Faith existe no MVP apenas para sustentar shrine/Pantheon sem exigir a árvore completa. O objetivo é validar ofertas, afinidade e custo ritual, não abrir um build religioso completo.

| Perk | Efeito | Requisito | Limite |
| --- | --- | --- | --- |
| Devoted | aumenta levemente ganho de afinidade por oferta válida | Faith nível 1 ou primeira oferta registrada | não reduz custo da oferta |
| Ritual Efficiency | reduz levemente custo secundário de rituais simples | Faith nível 2 + número mínimo de ofertas reais | não permite ritual gratuito |

### XP de Faith no MVP

```text
FaithXP =
  OfferingValue
  * ShrineRiskModifier
  * RitualRelevanceModifier
```

### Regra de Retorno Decrescente

Faith não aplica retorno decrescente agressivo no MVP. Ofertas repetidas ainda precisam ter custo real, mas o sistema não deve punir cedo o jogador que testa shrine/Pantheon como parte do loop de descoberta.

Retorno decrescente só entra se houver abuso claro:

```text
FaithDiminishingReturn =
  1.0                  if OfferingHasMeaningfulCost
  0.5                  if repeated trivial offering
  0.0                  if free/no-cost ritual
```

## Cross-Reference de Pantheon

- Regras de afinidade, bênçãos, custos e shrine ficam em [Pantheon System](<Pantheon System.md>).
- Este documento define apenas progressão, XP e perks do ramo Faith.

## Critérios de Aceite

- O jogador entende por que evoluiu em um ramo.
- Não há melhor build universal sem trade-offs.
- Progressão respeita o comportamento real da run.

## Atributos Base

| Atributo | Impacto |
| --- | --- |
| Strength | carga, dano físico, estabilidade |
| Dexterity | precisão, harvesting, manipulação |
| Endurance | stamina, fadiga, resistência |
| Perception | detecção, bestiário, leitura de ameaça |
| Intellect | notes, magia, alquimia |
| Faith | afinidade divina e rituais |
| Trade | negociação, loja, contratos |

## Perk Definition

```json
{
  "perk_id": "mining_clean_break_01",
  "branch": "mining",
  "tier": 1,
  "cost": 1,
  "requirements": {
    "skill_level": 3,
    "actions": ["mine_quality_ore_10"]
  },
  "effects": [
    { "type": "yield_quality_multiplier", "value": 1.08 }
  ],
  "tradeoff": "slightly_higher_noise"
}
```

## Progressão por Uso

XP só é concedido quando a ação resolve uma decisão real. Swing em parede sem minério, negociação simulada sem transação ou bloquear inimigo trivial não deve gerar progressão útil.

## Retorno Decrescente

```text
DiminishingReturnModifier =
  1 / (1 + RepeatedSafeActionCount * RepetitionScale)
```

## Ramos Detalhados

- **Mining:** qualidade, eficiência, leitura de veio, ruído controlado.
- **Harvesting:** partes raras, redução de contaminação, uso de ferramenta fina.
- **Utility:** peso, inventário, descanso, exploração e cartografia.
- **Combat:** stamina, armas, defesa, stagger e recuperação.
- **Trade:** preço, solvência, demanda, NPCs e contratos.
- **Faith:** ofertas, afinidade, bênçãos e custo reduzido de rituais.

## Anti-Exploit

- XP exige risco, valor ou consumo de recurso.
- A mesma ação segura repetida reduz XP.
- Treino no hub pode ensinar input, mas não escalar poder indefinidamente.

## Non-Goals

- Classe inicial rígida.
- Reset gratuito de build sem custo.
- Perks que removem sistemas centrais.
- Progressão offline passiva.

## Skill XP Sources

| Ramo | Fonte válida | Fonte inválida |
| --- | --- | --- |
| Mining | extrair nó real | bater parede vazia |
| Harvesting | parte extraída | repetir corpse tutorial |
| Combat | ameaça real | atacar boneco sem limite |
| Trade | transação com mercado | comprar/vender mesmo item em loop |
| Utility | sobreviver com peso/risco | andar em círculo |
| Faith | oferta com custo real | ritual gratuito repetido |

## Perk Types

- Efficiency: reduz custo sem remover risco.
- Quality: aumenta teto ou consistência.
- Information: revela mais dados.
- Option Unlock: libera ação nova.
- Risk Trade: aumenta poder com custo.

## Respec Policy

Respec completo não entra no MVP. Ajustes parciais podem existir via custo econômico, ritual ou item raro, mas nunca devem permitir alternância perfeita antes de cada desafio.

## Skill Telemetry

- XP por fonte.
- Perks mais escolhidos.
- Dominância de ramo.
- Relação skill vs valor extraído.
- Relação skill vs morte.

## Critérios de Saída

- Cada perk tem trade-off ou limite.
- XP farming seguro tem retorno decrescente.
- UI explica fonte de progresso.
- Skill data salva separada da run.
