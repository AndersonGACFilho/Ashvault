# Art Audio Direction

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define direção visual low poly dark fantasy e áudio sistêmico.

## High Concept

Ashvault usa low poly dark fantasy para clareza, performance e identidade. A direção visual deve comunicar função sistêmica: minério, perigo, bioma, ruído, peso e corrupção precisam ser reconhecíveis.

## Princípios Visuais

- Silhuetas fortes.
- Materiais legíveis.
- Paleta por bioma.
- Pouco ruído visual.
- Props modulares.
- Feedback visual para risco e valor.

## Áudio

Áudio é sistema de gameplay. Sons comunicam ameaça, distância, material, peso, criatura, ferramenta e estado de alerta.

## Camadas

- Ambiência de bioma.
- Foley de player.
- Ferramentas e impactos.
- Criaturas.
- Eventos mágicos.
- UI diegética.

## MVP

- Kit visual da mina.
- 1 set de props modulares.
- Cues de mineração, combate, inimigo e extração.
- Ambiência de dungeon.

## Critérios de Aceite

- O jogador identifica minério, ameaça e saída por leitura audiovisual.
- Assets suportam VR sem excesso de detalhe.
- Áudio crítico tem alternativa visual/acessível.

## Visual Language

| Elemento | Sinal visual |
| --- | --- |
| Minério comum | brilho baixo, veios grandes |
| Minério raro | cor distinta, partículas discretas |
| Saída | silhueta vertical, luz estável |
| Perigo | contraste, movimento, som direcional |
| Corrupção | deformação, cor não natural, pulsação |
| Interativo | forma reconhecível, affordance física |

## Bioma Mina MVP

- Rocha escura com cortes claros.
- Madeira e metal gasto para estruturas.
- Cristais/minérios como pontos de leitura.
- Névoa leve apenas quando não prejudica VR.
- Props modulares: parede, piso, escora, trilho, baú, veio, porta.

## Audio Event Data

```json
{
  "audio_event_id": "mine_pickaxe_hit",
  "category": "gameplay_noise",
  "loudness": 30,
  "occlusion": true,
  "subtitle_key": null,
  "ai_hears": true
}
```

## Mix Priorities

1. Ameaça imediata.
2. Feedback de player.
3. Interação crítica.
4. Ambiência.
5. Música ou camada emocional.

## Non-Goals

- Realismo visual caro.
- Ambiência que mascara informação crítica.
- Bokeh, blur ou escuridão que atrapalha leitura.

## Asset Budget MVP

| Categoria | Quantidade alvo | Observação |
| --- | --- | --- |
| Módulos de parede | 8-12 | variações por dano/umidade |
| Pisos/rampas | 6-8 | suportar verticalidade |
| Props estruturais | 10-15 | escoras, trilhos, correntes |
| Recursos/minérios | 8 | leitura por cor/silhueta |
| Ferramentas | 3-5 | escala correta em VR |
| Inimigos | 4 | silhuetas distintas |
| Boss | 1 | modular se possível |

## Regras de Material

- Materiais devem ser legíveis em baixa luz.
- Metal valioso não pode parecer igual a metal decorativo.
- Elementos interativos usam contraste de forma, não apenas brilho.
- Corrupção e magia devem usar linguagem consistente.

## Áudio Sistêmico

| Evento | Deve comunicar |
| --- | --- |
| Passos | peso, superfície, proximidade |
| Picareta | material, sucesso, ruído |
| Inimigo | tipo, distância, alerta |
| Inventário | peso, slot, erro |
| Extração | segurança, transição |
| Morte | causa e gravidade |

## Mix e Acessibilidade

Todo som crítico precisa de redundância visual ou háptica quando possível. Isso inclui inimigo alertado, item raro, dano pesado, baixa stamina e início de boss.

## Critérios de Saída

- Tester distingue minério comum/raro sem tooltip.
- Tester localiza ameaça por áudio estéreo/espacial.
- VR mantém performance com assets MVP.
- UI diegética é legível no ambiente.
