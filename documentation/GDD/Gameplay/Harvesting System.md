# Harvesting System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Detalha a coleta orgânica de criaturas e sua integração com bestiário, alquimia, culinária, economia e risco sistêmico.

## High Concept

Harvesting é o equivalente orgânico da mineração: o jogador transforma encontros com criaturas em matéria-prima, informação e decisões econômicas. A extração nunca deve ser um loot automático sem contexto; ela depende de ferramenta, conhecimento, condição do corpo, tempo investido e segurança da área.

## Objetivos de Design

- Recompensar observação e domínio do bestiário.
- Criar tensão pós-combate: coletar agora, fugir ou voltar depois.
- Alimentar alquimia, culinária, forja especial e economia sem gerar recursos passivos.
- Diferenciar partes por utilidade, qualidade, perecibilidade e risco.

## Loop

1. Encontrar ou derrotar criatura.
2. Identificar partes possíveis via bestiário ou tentativa.
3. Escolher ferramenta e técnica.
4. Executar interação com tempo vulnerável.
5. Gerar parte orgânica com qualidade, contaminação e integridade.
6. Consumir, vender, cozinhar, alquimizar ou registrar descoberta.

## Parâmetros Core

| Campo | Função |
| --- | --- |
| `species_id` | Referência da criatura. |
| `part_id` | Parte extraível. |
| `required_tool` | Faca, serra, frasco, pinça, luva ou recipiente arcano. |
| `knowledge_required` | Nível de bestiário necessário para extração confiável. |
| `base_yield` | Quantidade base. |
| `decay_rate` | Velocidade de perda de qualidade. |
| `contamination_risk` | Risco de toxicidade, corrupção ou doença. |
| `noise_profile` | Ruído produzido pela extração. |

## Fórmula de Qualidade

```text
HarvestQuality =
  BasePartQuality
  * ToolMultiplier
  * BestiaryKnowledgeMultiplier
  * CorpseIntegrity
  * TimeSinceDeathModifier
  * PlayerSkillModifier
  * EnvironmentModifier
```

## Integrações

- **Bestiário:** desbloqueia partes conhecidas, fraquezas anatômicas e técnicas limpas.
- **Alchemy:** define propriedades, toxicidade e estabilidade de reagentes orgânicos.
- **Cooking:** usa carne, gordura, sangue e órgãos como ingredientes perecíveis.
- **Economy:** preço aumenta com raridade, qualidade, demanda semanal e risco de obtenção.
- **Notes:** registra observações, falhas e propriedades descobertas.
- **Noise & Perception:** harvesting produz som e deixa o jogador vulnerável.

## MVP

- 5 criaturas com 2 partes extraíveis cada.
- 3 ferramentas de harvesting.
- Qualidade simples: baixa, comum, alta, perfeita.
- Perecibilidade básica.
- Integração mínima com bestiário, cooking e economy.

## Critérios de Aceite

- Nenhuma parte orgânica entra na economia sem origem rastreável.
- Extração durante perigo real expõe o jogador.
- Conhecimento de bestiário melhora qualidade e reduz falha.
- Partes perecíveis têm decisão clara: usar, preservar ou vender rápido.

## Taxonomia de Partes

| Categoria | Exemplos | Uso primário | Risco |
| --- | --- | --- | --- |
| Carne | músculo, gordura, tendão | cooking, isca, venda comum | perecibilidade |
| Órgão | coração, fígado, glândula | alquimia, quests, buffs raros | contaminação |
| Fluido | sangue, bile, veneno | alchemy, coatings, bombas | vazamento/toxicidade |
| Estrutura | osso, chifre, garra, escama | crafting, talismãs, venda | peso/fragilidade |
| Essência | cinza anímica, resíduo arcano | magia, deuses, alta economia | corrupção |

## Estados do Corpo

| Estado | Condição | Efeito |
| --- | --- | --- |
| Fresh | criatura morreu recentemente | qualidade integral, baixo decay |
| Damaged | dano excessivo no combate | reduz partes frágeis |
| Burned | dano de fogo/ácido | carne ruim, essência instável |
| Contaminated | veneno, corrupção ou bioma hostil | aumenta toxicidade |
| Decayed | tempo alto desde morte | reduz valor e aumenta falha |

## Runtime Pipeline

1. `EnemyDeathEvent` cria `CorpseEntity`.
2. `BestiaryManager` calcula partes conhecidas e ocultas.
3. `InteractionManager` valida ferramenta, postura e segurança.
4. `HarvestingManager` executa skill check e quality roll.
5. `InventoryManager` cria item com origem, qualidade, decay e tags.
6. `NoteManager` registra descoberta se houve propriedade nova.
7. `EconomyManager` atualiza oferta potencial apenas após extração válida.
8. `TelemetryManager` registra tempo, risco, falha e valor gerado.

## Modelo de Dados

```json
{
  "harvest_part_id": "gloomrat_venom_sac",
  "species_id": "gloomrat",
  "display_name_loc": "item.part.gloomrat_venom_sac",
  "category": "fluid",
  "required_tool": "fine_knife",
  "knowledge_required": 2,
  "base_yield": 1,
  "base_value": 32,
  "decay_rate_per_hour": 0.18,
  "contamination_risk": 0.25,
  "alchemy_properties": ["toxin", "numbing"],
  "cooking_tags": ["unsafe_raw"],
  "noise_profile": "wet_cut_medium"
}
```

## Fórmula de Falha

```text
FailureChance =
  BaseDifficulty
  + ToolMismatchPenalty
  + CorpseDamagePenalty
  + ThreatPressurePenalty
  - HarvestingSkill
  - BestiaryKnowledge
  - StabilizingConsumable
```

Falha não deve destruir tudo por padrão. Resultados válidos: qualidade menor, quantidade menor, contaminação, ruído alto, dano leve ao jogador ou descoberta parcial.

## Balanceamento

- Partes de alto valor devem vir de inimigos com custo de combate, stealth ou preparação.
- Partes perecíveis podem valer muito no curto prazo, mas não podem virar estoque infinito.
- Conhecimento deve reduzir variância sem remover totalmente o risco.
- Ferramentas melhores aumentam teto de qualidade, não apenas velocidade.

## Non-Goals

- Loot automático universal.
- Desmembramento gráfico como foco.
- Farm passivo de partes sem risco.
- Mini-game obrigatório complexo para cada corpo.
