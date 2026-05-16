# UI UX Flow

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define telas, fluxo e princípios de UI.

## High Concept

UI em Ashvault deve priorizar legibilidade, simulação ativa e baixo atrito. Sempre que possível, informações aparecem em objetos diegéticos ou overlays mínimos justificados.

## Telas Necessárias

- Main menu.
- Perfil/save.
- Configurações.
- Hub/shop.
- Inventário.
- Livro de notes/bestiário.
- Crafting/forja.
- Alquimia/cooking.
- Morte/corpse.
- Extração/liquidação.

## Regras

- UI crítica não depende só de cor.
- Texto deve respeitar VR readability.
- Menus de run não pausam perigos sem regra explícita.
- Feedback de peso, ruído e risco deve ser legível.

## MVP

- Main menu.
- Inventário.
- Shop.
- Death/extraction summary.
- Notes simples.

## Critérios de Aceite

- Jogador entende estado atual sem HUD poluído.
- Interfaces longas são evitadas durante perigo.
- Fluxo de venda e extração é claro.

## Fluxos Principais

### Extração

1. Jogador alcança saída válida.
2. UI mostra valor carregado, peso e riscos pendentes.
3. Confirmação curta, sem menu complexo.
4. Run encerra.
5. Tela de liquidação mostra valor, perdas, notes e progressão.

### Venda

1. Abrir loja no hub.
2. Listar itens extraídos com origem e qualidade.
3. Mostrar preço, demanda e saturação.
4. Confirmar venda.
5. Atualizar mercado e solvência.

### Morte

1. Feedback claro de causa.
2. Mostrar valor perdido/recuperável.
3. Indicar localização aproximada do corpse.
4. Oferecer retorno ao hub ou retry conforme regra.

## UI State Data

```json
{
  "screen_id": "extraction_summary",
  "blocking": true,
  "runtime_context": "post_run",
  "required_data": ["run_result", "extracted_items", "skill_xp", "market_delta"]
}
```

## Regras de Legibilidade

- Números críticos sempre com unidade ou comparação.
- Preço deve mostrar por que mudou quando relevante.
- Peso deve mostrar threshold atual.
- Estados de perigo não podem ficar escondidos em submenu.

## Non-Goals

- HUD MMO.
- Menus aninhados durante combate.
- UI puramente decorativa sem função.

## Arquitetura de Telas

| Tela | Domínio | Pausa? | Dados mínimos |
| --- | --- | --- | --- |
| Main Menu | meta | sim | perfil, settings |
| Hub Shop | economia | sim/no hub | estoque, mercado, moeda |
| Inventory | gameplay | não na dungeon | peso, slots, itens |
| Notes Book | conhecimento | depende do contexto | entries, tags, descoberta |
| Death Summary | run | sim | causa, corpse, perdas |
| Extraction Summary | run/economia | sim | recursos, XP, mercado |
| Crafting/Forge | crafting | no hub | receita, material, risco |
| Settings | sistema | sim | input, conforto, áudio |

## Estados Visuais Obrigatórios

- Normal.
- Hover/focus.
- Disabled com motivo.
- Loading/processing.
- Warning.
- Confirmed.
- Error recoverable.
- Error fatal.

## Informação Crítica em Run

Informação crítica deve caber em leitura rápida:

- Vida.
- Stamina.
- Peso/carga.
- Ruído ou alerta quando relevante.
- Valor carregado aproximado.
- Objetivo de extração/corpse.

## Regras de Confirmação

Confirmação só é obrigatória para ações irreversíveis ou de alto custo: vender item raro, abandonar run, sacrificar recurso, sobrescrever save, iniciar boss ou resetar configuração.

## Wireflow Textual

```text
Hub -> Prepare Inventory -> Enter Dungeon
Dungeon -> Active Run -> Extract
Extract -> Summary -> Shop -> Upgrade/Prepare
Death -> Corpse Info -> Hub -> Recovery Run
```

## Critérios de Saída

- Fluxo de primeira venda sem instrução externa.
- Death summary explica causa e recuperação.
- Inventário mostra peso e consequência.
- UI crítica testada em VR e flatscreen.
