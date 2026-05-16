# Localization System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define arquitetura de localização e regras de texto.

## High Concept

Localização deve ser data-driven desde o início. Ashvault usa muitos textos sistêmicos, notes, UI diegética e termos técnicos; strings hardcoded criariam dívida cedo.

## Estrutura

```text
loc_id
source_text
context
speaker
variables
gender_rules
plural_rules
max_length
```

## Regras

- Todo texto visível usa ID.
- Variáveis devem ter contexto.
- Notes e bestiário preservam tom de descoberta.
- UI deve declarar limites de comprimento.
- Termos de sistema têm glossário.

## MVP

- Português BR como fonte.
- IDs para UI, items, enemies e notes.
- Export CSV/JSON.
- Glossário mínimo.

## Critérios de Aceite

- Não há texto gameplay-critical hardcoded.
- Tradução tem contexto suficiente.
- UI suporta expansão moderada de texto.

## ID Convention

```text
ui.inventory.weight
item.iron_ore.name
item.iron_ore.desc
enemy.mine_stalker.name
note.alchemy.toxin_01.body
system.death.fall_damage
```

## Localization Entry

```json
{
  "loc_id": "ui.inventory.weight",
  "pt_br": "Peso",
  "context": "Inventory label for carried weight",
  "max_length": 14,
  "variables": [],
  "status": "approved"
}
```

## Variáveis

Variáveis precisam declarar tipo, unidade e fallback:

```text
Você carrega {current_weight:number:kg} / {max_weight:number:kg}.
```

## Glossário Inicial

| Termo | Regra |
| --- | --- |
| Run | manter "run" se o resto do documento usar |
| Dungeon | manter "dungeon" |
| Corpse | usar "corpse" para o sistema, "corpo" para objeto físico |
| Harvesting | manter como sistema, traduzir ação como extração/coleta |
| Flatscreen | manter termo técnico |

## Pipeline

1. Designers criam loc IDs.
2. Texto fonte em PT-BR.
3. Validação de IDs órfãos.
4. Export para CSV/JSON.
5. Import em build.
6. Teste de overflow por idioma.

## Non-Goals

- String hardcoded para UI final.
- Tradução sem contexto.
- Ajustar layout manualmente por frase individual.

## Categorias de Texto

| Categoria | Exemplo | Exigência |
| --- | --- | --- |
| UI sistêmica | peso, vender, extrair | curta, max_length obrigatório |
| Item | minério, poção, arma | nome e descrição separados |
| Notes | páginas de descoberta | contexto narrativo e tags |
| Bestiário | criatura e comportamento | tom observacional |
| Tutorial | prompt contextual | frase curta e ação clara |
| Erro/falha | save inválido, morte | causa e próximo passo |

## Regras para Variáveis

- Variáveis numéricas declaram unidade.
- Variáveis de item usam loc ID, não nome cru.
- Plural deve ser resolvido pela camada de localização.
- Textos com gênero gramatical precisam de metadata.
- Nenhum texto deve concatenar frases parciais.

## Export Schema

```json
{
  "loc_id": "system.death.fall_damage",
  "namespace": "system",
  "pt_br": "Você morreu pela queda.",
  "description": "Death summary shown after fatal fall damage.",
  "max_length": 80,
  "variables": [],
  "tags": ["death", "summary"],
  "review_status": "draft"
}
```

## Validação Automatizada

- IDs duplicados falham build.
- IDs ausentes em idioma primário falham build.
- Variável usada no texto mas não declarada falha build.
- `max_length` excedido gera warning.
- Namespace inválido falha validação.

## Glossário Técnico Vivo

O glossário deve ser versionado junto do GDD. Quando um termo de sistema muda, notes, UI, bestiário e tutorial precisam ser verificados para não criar vocabulários paralelos.

## Critérios de Saída

- Todas as telas MVP usam loc ID.
- Todos os itens MVP têm nome e descrição.
- Tutorial não contém texto hardcoded.
- Pelo menos um pseudo-locale foi testado para overflow.
