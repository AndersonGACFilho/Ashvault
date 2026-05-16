# VR UX Accessibility System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define conforto VR, acessibilidade e UX diegética.

## High Concept

Ashvault é embodied-first, mas não deve confundir fisicalidade com desconforto. O jogo deve oferecer locomoção, leitura e interação robustas sem quebrar a fantasia diegética.

## Locomoção

- Smooth locomotion.
- Snap turn.
- Teleport opcional quando compatível.
- Vignette configurável.
- Altura e reach calibration.

## Acessibilidade

- Dominância de mão.
- Redução de esforço físico.
- Assistência de grip.
- Contraste de leitura.
- Subtitles e cues visuais para áudio crítico.
- Remapeamento de input.

## UX Diegética

Interfaces devem nascer de objetos, livros, placas, ferramentas, inventário físico ou equivalentes flatscreen que mantenham a simulação ativa.

## MVP

- Opções de snap/smooth turn.
- Calibração de altura.
- Subtitles para sons críticos.
- Texto legível em VR.

## Critérios de Aceite

- Jogadores sensíveis a movimento têm opções básicas.
- UI crítica é legível sem pausar indevidamente.
- Acessibilidade não altera economia de risco sem compensação.

## Comfort Matrix

| Opção | Valores | Impacto sistêmico |
| --- | --- | --- |
| Turn mode | snap, smooth | nenhum |
| Vignette | off, low, high | nenhum |
| Dominant hand | left, right | nenhum |
| Grip assist | hold, toggle | nenhum se tempo igual |
| Seated mode | on/off | recalibra alcance |
| Physical effort assist | low/medium | deve preservar vulnerability cost |

## Regras de Paridade

Assistências podem reduzir desconforto, mas não podem reduzir tempo, ruído, stamina ou exposição de ações críticas sem compensação equivalente.

## VR Readability

- Texto a distância confortável.
- Contraste alto.
- Evitar blocos longos flutuantes.
- Usar livros, placas e objetos quando possível.
- Substituir tooltip pequeno por painel legível em VR.

## Locomoção e Queda

Quedas são parte do design, mas a apresentação deve evitar enjoo: feedback visual, redução opcional de impacto de câmera e som forte de consequência.

## Testes Obrigatórios

- Jogar 20 minutos sem abrir menu de debug.
- Ler note em VR sem aproximar rosto excessivamente.
- Alternar seated/standing sem quebrar interação.
- Snap turn não altera combate ou stealth.

## Non-Goals

- Remover risco físico do design.
- Transformar VR em modo invulnerável por conforto.
- HUD não diegético pesado como solução padrão.

## Interaction Comfort Rules

| Interação | Risco de conforto | Regra |
| --- | --- | --- |
| Mineração repetida | fadiga de braço | permitir amplitude reduzida com mesmo tempo/custo |
| Combate pesado | esforço excessivo | limitar necessidade de força real |
| Queda vertical | enjoo | estabilização opcional de câmera |
| Inventário corporal | alcance desconfortável | calibração e zonas ajustáveis |
| Leitura | eye strain | distância mínima e tamanho de fonte |

## Accessibility Data

```json
{
  "comfort": {
    "turn_mode": "snap",
    "snap_degrees": 30,
    "vignette": "medium",
    "seated_mode": false,
    "dominant_hand": "right",
    "grip_mode": "toggle"
  },
  "readability": {
    "subtitle_enabled": true,
    "text_scale": 1.1,
    "high_contrast": false
  }
}
```

## Test Protocol

1. Sessão de 10 minutos no hub.
2. Sessão de 20 minutos na dungeon.
3. Mineração repetida por 2 minutos.
4. Combate curto com 2 inimigos.
5. Leitura de note em ambiente escuro.
6. Extração com peso alto.

Cada teste registra desconforto, legibilidade, fadiga e se o jogador entendeu a consequência sistêmica.

## Fadiga Física

Fadiga do personagem e fadiga do jogador não são a mesma coisa. O sistema pode simular cansaço sem exigir esforço real excessivo. Quando houver conflito, preservar saúde do jogador e compensar custo via tempo, stamina ou vulnerabilidade.

## Critérios de Saída

- Opções de conforto salvam e recarregam.
- Seated mode completa a primeira run.
- Grip assist não acelera interação.
- Texto crítico passa teste de leitura em headset.
