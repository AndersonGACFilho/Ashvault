# Animation, Physics and Interaction Feel

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Define feel físico, grabbing, melee, haptics, IK, recoil, crafting gestures e regras de cancelamento.

## High Concept

Ashvault depende de presença física. Interações precisam parecer táteis em VR e legíveis em flatscreen, sem deixar física instável decidir resultado sistêmico sozinha.

## Princípios

- Física dá feedback, sistemas dão autoridade.
- VR privilegia mão, peso, contato e escala.
- Flatscreen traduz feel em timing, camera feedback e commitment.
- Haptics reforçam leitura, não escondem regra.
- Animation cancel é regra de design, não bug.

## Grabbing

| Objeto | Regra |
| --- | --- |
| Item leve | snap suave para mão |
| Ferramenta | grip estável e pose dedicada |
| Arma pesada | inércia simulada, custo de stamina |
| Resource chunk | peso e duas mãos se necessário |
| Corpse/ally | arrastar, não carregar livremente no MVP |

## Weapon Swing

VR usa velocidade, arco e colisão filtrada. Flatscreen usa ataques com windup, active frames e recovery. Ambos emitem o mesmo evento lógico de combate.

## Haptics

- Impacto leve.
- Impacto pesado.
- Ferramenta em material válido.
- Bloqueio.
- Dano recebido.
- Item encaixado.
- Alerta de stamina baixa.

## Physics Constraints

- Objetos críticos não podem atravessar chão e sumir.
- Interações econômicas precisam de confirmação sistêmica.
- Ferramentas têm poses estáveis.
- IK não pode quebrar leitura de arma.
- Ropes/climbing precisam de limites de conforto.

## Crafting Gestures

- Forja: aquecer, martelar, resfriar.
- Alchemy: misturar, dosar, filtrar.
- Cooking: cortar, assar, preservar.
- Mining: bater, extrair, recolher.

Gestos podem ser simplificados, mas cada um precisa de custo de tempo, feedback e resultado data-driven.

## Animation Cancel Rules

```text
CanCancel =
  ActionAllowsCancel
  && NotInCriticalCommitWindow
  && CancelCostApplied
```

Cancelamento pode consumir stamina, perder progresso, gerar ruído ou danificar ferramenta.

## Fall Landing

Landing precisa comunicar dano, peso e superfície com câmera, som, haptic e estado do player. Redução de enjoo não remove consequência.

## MVP

- Grab/release robusto.
- 3 ferramentas com pose.
- Melee básico.
- Haptics para impacto.
- IK estável de mãos.
- Cancel rules para mineração/interação.

## Critérios de Aceite

- Interação física não duplica item.
- Ferramenta parece pesada sem exigir força real.
- Hit feedback diferencia material e inimigo.
- Flatscreen preserva commitment.

