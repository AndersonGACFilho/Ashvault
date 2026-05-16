# Gameplay Audio and Acoustic Simulation

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Trata áudio como sistema de gameplay: percepção inimiga, stealth, telegraph, VR spatial audio e música dinâmica.

## High Concept

Som em Ashvault não é decoração. Ele informa risco, gera detecção, denuncia peso, comunica material e orienta o jogador em VR e flatscreen.

## Acoustic Event

```json
{
  "audio_event_id": "pickaxe_stone_hit_heavy",
  "position": [12.4, -2.0, 18.8],
  "loudness": 32,
  "material": "stone",
  "duration": 0.6,
  "ai_hears": true,
  "player_feedback": true,
  "tags": ["mining", "impact", "repeated"]
}
```

## Propagação

```text
HeardLoudness =
  SourceLoudness
  * MaterialEcho
  * RoomReverb
  - DistanceFalloff
  - Occlusion
  - AmbientMasking
```

## Material Echo

| Material | Assinatura |
| --- | --- |
| stone | seco, médio alcance |
| metal | alto, brilhante, denuncia |
| wood | curto, oco |
| wet soil | abafado |
| crystal | tonal, reconhecível |

## Gameplay Feedback

- Inimigo ouviu algo.
- Boss vai atacar.
- Recurso raro exposto.
- Equipamento quebrou.
- Peso alto alterou passos.
- Área selada ativou.
- Corpse ou party ping próximo.

## Enemy Hearing

IA consome eventos acústicos com posição aproximada, intensidade e tags. O som deve gerar investigação, não conhecimento perfeito.

## VR Spatial Audio

Áudio espacial é obrigatório para ameaça, ping, queda, boss e inimigos fora de visão. Mix precisa preservar conforto: som alto comunica perigo sem machucar.

## Música Dinâmica

Música deve reagir a exploração, suspeita, combate, boss e extração. Ela não pode mascarar eventos acústicos críticos.

## UI Audio

UI diegética usa sons de objetos: livro, moeda, baú, metal, pedra. Sons de confirmação devem diferenciar sucesso, erro e transação econômica.

## Integrações

- **Noise & Perception:** acoustic events.
- **AI:** hearing.
- **Combat:** impacto, bloqueio, dano.
- **Mining/Forge:** material.
- **UX Accessibility:** subtitles/cues visuais para sons críticos.

## MVP

- Acoustic event system.
- Propagação simples por distância/oclusão.
- Spatial audio para inimigos e impactos.
- Boss telegraph sonoro.
- UI audio de venda/erro.

## Critérios de Aceite

- Jogador localiza ameaça por som.
- IA reage a ruído sem onisciência.
- Música não cobre som crítico.
- Sons críticos têm alternativa visual quando necessário.

