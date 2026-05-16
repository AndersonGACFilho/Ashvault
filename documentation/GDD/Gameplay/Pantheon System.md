# Pantheon System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define afinidade divina, bênçãos, custos e regras de runtime. [Skill Tree System](<Skill Tree System.md>) é autoritativo para a decisão de **Faith no MVP**: shrine/Pantheon no MVP usa um ramo Faith mínimo com 2 perks.

## High Concept

Deuses são modificadores sistêmicos com custo narrativo e mecânico. Eles não funcionam como árvores de passivos grátis; cada afinidade muda o modo como o jogador lida com risco, economia, combate e sobrevivência.

## Deuses Base

- Deus da Destruição
- Deus da Vida
- Deus da Terra
- Deus do Sacrifício

## Afinidade

```text
AffinityDelta =
  ActionAlignment
  * OfferingValue
  * RiskAccepted
  * ContradictionPenalty
```

## Regras

- Afinidade sobe por ações coerentes e ofertas.
- Afinidade contraditória pode reduzir bênçãos.
- Bênçãos entram no stack oficial de modificadores.
- Custos devem ser explícitos o suficiente para criar decisão.

## Integrações

- **Skill Tree:** ramo Faith mede dedicação e eficiência ritual. No MVP, esta dependência é limitada ao Faith mínimo definido em [Skill Tree System](<Skill Tree System.md>).
- **Economy:** ofertas retiram valor da economia.
- **Combat/Magic:** bênçãos alteram dano, backlash, cura ou resistência.
- **Mutation/Corruption:** certas afinidades podem acelerar ou mitigar exposição.

## MVP

- 2 deuses ativos.
- 3 bênçãos por deus.
- Sistema simples de oferta.
- Dependência explícita do ramo Faith mínimo do [Skill Tree System](<Skill Tree System.md>): perks `Devoted` e `Ritual Efficiency`.
- Penalidade por contradição.

## Critérios de Aceite

- Bênçãos nunca são estritamente gratuitas.
- Escolher um deus muda decisões de run.
- O stack de modificadores permanece determinístico.

## Afinidade por Deus

| Deus | Ações alinhadas | Custo típico | Bônus típico |
| --- | --- | --- | --- |
| Destruição | combate, fogo, risco agressivo | ruído, consumo, hostilidade | dano, stagger, quebra |
| Vida | cura, preservação, comida, proteção | menor lucro imediato | regen, resistência, purificação |
| Terra | mineração, peso, defesa, estabilidade | lentidão, rigidez | carga, armor, minério |
| Sacrifício | ofertas caras, sangue, perda voluntária | HP, recursos, corrupção | poder alto e instável |

## Stack de Modificadores

```text
FinalEffect =
  BaseEffect
  * SkillModifier
  * ItemModifier
  * PantheonModifier
  * BiomeModifier
  * StatusModifier
```

O modificador divino nunca deve ser aplicado fora da ordem oficial; isso evita resultados não determinísticos entre combate, magia, economia e multiplayer.

## Modelo de Dados

```json
{
  "deity_id": "earth",
  "affinity": 42,
  "active_blessings": ["stone_back", "ore_sense"],
  "last_offering_week": 7,
  "contradiction_debt": 4,
  "runtime_modifiers": [
    { "stat": "carry_capacity", "op": "mul", "value": 1.12 }
  ]
}
```

## Rituais

- Oferta material.
- Oferta de sangue/vida.
- Juramento temporário.
- Sacrifício econômico.
- Purificação de mutação.

## Falhas Divinas

Falhar ou contradizer afinidade pode gerar perda de bênção, custo aumentado, dívida ritual, backlash leve ou alteração de demanda econômica associada a templos/NPCs.

## Non-Goals

- Sistema moral binário.
- Bênçãos permanentes sem custo.
- Divindades como classes disfarçadas.
- Conteúdo obrigatório para completar MVP inteiro.

## Blessing Definition

```json
{
  "blessing_id": "earth_stone_back",
  "deity_id": "earth",
  "tier": 1,
  "affinity_required": 25,
  "cost": {
    "offering_tags": ["ore", "stone"],
    "value": 80
  },
  "effects": [
    { "stat": "carry_capacity", "op": "mul", "value": 1.12 },
    { "stat": "move_noise", "op": "mul", "value": 1.05 }
  ],
  "duration": "weekly"
}
```

## Contradiction Rules

| Ação | Contradição possível |
| --- | --- |
| Sacrificar criatura protegida | Vida perde afinidade |
| Fugir de combate ritual | Destruição perde afinidade |
| Vender relíquia sagrada | Faith debt |
| Usar mutação proibida | Vida/Terra podem reagir |

## Economy Sink

Ofertas são sink econômico importante. O valor sacrificado não deve retornar imediatamente em lucro equivalente; o benefício é poder, segurança ou opção estratégica.

## MVP Integration

No MVP, Pantheon pode existir como shrine simples com dois deuses, uma bênção cada e custo claro. Como shrine entra no MVP, Pantheon usa o ramo Faith mínimo definido em [Skill Tree System](<Skill Tree System.md>), com XP por oferta e sem retorno decrescente agressivo para ofertas com custo real.

Faith completo, mais perks religiosos e builds dedicadas continuam sendo expansão de Alpha. O MVP valida apenas:

- oferta com custo real;
- ganho de afinidade;
- duas perks de suporte (`Devoted`, `Ritual Efficiency`);
- aplicação de bênção no stack oficial;
- feedback de contradição.

## Cross-Reference de Skill Tree

- Progressão, XP de Faith e perks MVP são autoritativos em [Skill Tree System](<Skill Tree System.md>).
- Este documento define afinidade, bênçãos, custos, shrine e runtime do Pantheon.

## Critérios de Saída

- Afinidade muda por ação rastreável.
- Bênção entra no stack oficial.
- Oferta remove valor da economia.
- Contradição tem feedback legível.
- Pantheon MVP consome apenas o Faith mínimo definido no Skill Tree MVP.
