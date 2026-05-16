# Rest System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Este arquivo cobre **apenas descanso**: Breath, Camp, Shrine e Hub Rest. Morte, criação de corpse, recuperação de corpse, death snapshots, Global Pool e perda de loot são autoritativos em [Death, Corpse and Recovery System](<Death Corpse & Recovery System.md>).

## High Concept

Descanso em Ashvault é recuperação com custo de tempo, exposição e oportunidade. Ele não é pausa segura universal. Cada tipo de descanso responde a uma pergunta diferente:

- **Breath:** posso recuperar fôlego sem sair do perigo?
- **Camp:** posso transformar uma sala em alívio temporário?
- **Shrine:** aceito custo ritual por recuperação forte?
- **Hub Rest:** encerro a pressão da run e volto ao estado seguro?

## Objetivos de Design

- Criar alívio controlado sem remover tensão.
- Fazer tempo, ruído, recursos e risco de patrulha importarem.
- Diferenciar recuperação curta, tática, ritual e total.
- Integrar descanso com stamina, HP, status effects, mutação, deuses, dungeon state e run state.
- Evitar qualquer lógica de morte/corpse neste domínio.

## Tipos de Descanso

| Tipo | Local | Recupera | Custo | Risco |
| --- | --- | --- | --- | --- |
| Breath | canto temporariamente seguro, atrás de cobertura, fora de combate direto | stamina, foco/cognitive load leve | tempo curto, imóvel | inimigo pode investigar |
| Camp | sala validada, camp spot, área temporariamente limpa | HP parcial, stamina, alguns status leves | tempo longo, comida/kit, ruído baixo | patrulha, invasão, mundo avança |
| Shrine | shrine ativo ou altar raro | HP/status/mutação/bênção conforme shrine | oferta, afinidade, corrupção ou recurso ritual | dívida divina, custo econômico, evento |
| Hub Rest | hub/overworld | recuperação total ou quase total | encerra/resolve ciclo de run | nenhum risco de combate, mas avança economia/tempo |

## Breath

Breath é descanso curto e tático. Ele não salva progresso, não cria checkpoint e não remove estados graves. Serve para permitir micro-recuperação sem quebrar o fluxo de exploração.

```text
BreathRecovery =
  BaseStaminaRegen
  * SafetyModifier
  * EncumbrancePenalty
  * StatusModifier
```

### Regras

- Requer não estar recebendo ataque direto.
- Pode ocorrer em dungeon ativa.
- Player fica imóvel ou com movimento mínimo.
- Gera pouco ou nenhum ruído.
- É interrompido por dano, ataque, interação ou ameaça imediata.

## Camp

Camp é descanso médio/forte dentro da dungeon. Ele exige uma sala validada e representa uma aposta: recuperar recursos agora em troca de tempo e risco.

### Validação de Camp

```text
CanCamp =
  PlayerNotInCombat
  && RoomAllowsCamp
  && NoImmediateThreat
  && CriticalPathNotBlocked
  && HasCampResourceOrSite
  && RunStateAllowsTimeAdvance
```

`RoomAllowsCamp` não é decidido localmente pelo Rest System. Ele deve vir de um `CampEligibilityToken` emitido pelo `DungeonFloorValidator` ou por um `RestSiteDefinition` autorizado na sala. `sleep_zone` da geração é dado de layout; só vira camp válido quando a bridge Dungeon -> Rest declarar custo, risco, ameaça, acesso e bloqueio de softlock.

### Efeitos

- Recupera HP parcial.
- Recupera stamina total ou quase total.
- Pode reduzir status leves.
- Pode reduzir carga cognitiva.
- Pode avançar timers de decay, patrol e world state.
- Não manipula corpse, death snapshot ou loot perdido.

## Shrine

Shrine é descanso ritual. Ele pode curar mais que um Camp, mas sempre deve cobrar algo: oferta, afinidade, risco de corrupção, dívida divina, mudança de status ou custo econômico.

### Tipos de Shrine

| Shrine | Recuperação | Custo |
| --- | --- | --- |
| Vida | HP, bleed, poison leve | oferta orgânica, afinidade |
| Terra | armor break, stagger trauma, carga | minério, lentidão temporária |
| Destruição | remove fear, concede poder curto | ruído, hostilidade, recurso queimado |
| Sacrifício | cura forte ou mutação controlada | HP máximo temporário, item raro, corrupção |

### Regras

- Shrine nunca é cura grátis.
- Shrine pode interagir com Pantheon, mas não substitui o Pantheon System.
- Shrine pode aplicar status effects positivos ou negativos.
- Shrine deve emitir evento para telemetria e economia se consumir recurso.

## Hub Rest

Hub Rest é recuperação fora da dungeon. Ele representa encerramento ou intervalo seguro do ciclo de risco.

### Regras

- Só ocorre no Hub.
- Pode recuperar HP, stamina, carga cognitiva e status comuns.
- Pode avançar tempo econômico, contratos e NPC schedules.
- Pode encerrar estado de run conforme Run System.
- Não recupera automaticamente itens perdidos em morte; isso pertence ao [Death, Corpse and Recovery System](<Death Corpse & Recovery System.md>).

## Custos de Descanso

| Custo | Aplicação |
| --- | --- |
| Tempo | Breath, Camp, Hub Rest |
| Comida | Camp, Hub Rest opcional |
| Kit de descanso | Camp |
| Oferta ritual | Shrine |
| Afinidade/dívida divina | Shrine |
| Ruído | Camp setup, Shrine activation |
| Avanço de mundo | Camp, Hub Rest |
| Vulnerabilidade | Breath e Camp durante a animação/canalização |

## Rest Validation

```text
CanRest =
  PlayerAlive
  && PlayerNotInHardCC
  && RestTypeAvailableInContext
  && RequiredCostsAvailable
  && ThreatLevel <= RestTypeThreatTolerance
  && RunStateAllowsRest
```

## Rest Runtime Pipeline

1. `InteractionManager` inicia interação de descanso.
2. `RestManager` valida contexto, custos e ameaça.
3. `PlayerSystem` entra em estado `Resting`.
4. `Noise & Perception` recebe evento se o tipo emitir som.
5. `AI Systems` pode reagir a tempo avançado, patrulha ou ruído.
6. `Status Effects System` aplica recuperação, remoção ou conversão de status.
7. `Pantheon System` resolve custos/afinidade se for Shrine.
8. `Run System` atualiza timers, estado e logs.
9. `Telemetry Balance System` registra uso, interrupção e resultado.

## Modelo de Dados

```json
{
  "rest_site_id": "camp_room_02_07",
  "rest_type": "camp",
  "allowed_contexts": ["dungeon_room_cleared"],
  "base_duration": 30,
  "threat_tolerance": "low",
  "costs": [
    { "type": "food", "amount": 1 }
  ],
  "recovery": {
    "health_percent": 0.35,
    "stamina_percent": 1.0,
    "cognitive_load_delta": -0.25
  },
  "interruptible": true,
  "noise_profile": "camp_setup_low"
}
```

## Interrupção

| Interrupção | Resultado |
| --- | --- |
| Dano recebido | descanso cancela, recuperação parcial ou nenhuma |
| Inimigo entra em ameaça imediata | player acorda/levanta, estado de alerta |
| Player cancela manualmente | custos parciais conforme tipo |
| Shrine backlash | descanso vira falha ritual/status |
| Co-op pull/transition | cancela ou aguarda conforme Party System |

## Integrações

- **Player System:** estado `Resting`, HP, stamina, cognitive load.
- **Status Effects System:** cura, remoção ou conversão de status.
- **Pantheon System:** Shrine, ofertas, afinidade e dívida.
- **Economy System:** custo de comida, kits, ofertas e serviços do hub.
- **Run System:** avanço de tempo, logs e restrições de run.
- **Dungeon Generation System:** ownership de `sleep_zone`, `RestSiteDefinition` e `CampEligibilityToken`.
- **AI Systems:** patrulha, invasão e reação a ruído/tempo.
- **Noise & Perception System:** ruído de camp setup e shrine activation.
- **UI UX Flow:** feedback de recuperação, custo e risco.
- **Death, Corpse and Recovery System:** sistema separado para morte e corpse; este documento não define regras de corpse.

## Multiplayer

- Breath é individual.
- Camp pode ser individual ou party-wide se o site permitir.
- Shrine é individual por padrão, mas pode aceitar ritual cooperativo explícito.
- Hub Rest pode ser individual; início de nova run exige regras de party/ready check.
- Descanso party-wide não pode teleportar, reviver ou recuperar corpse sem passar pelos sistemas apropriados.

## Telemetry

- Tipo de descanso usado.
- Local e andar.
- Duração planejada vs duração real.
- Interrompido ou completo.
- Custos consumidos.
- HP/stamina/status recuperados.
- Ameaças geradas durante descanso.
- Diferença de uso VR/flatscreen.

## MVP

- Breath para stamina/foco curto.
- Camp em sala validada com custo simples.
- Hub Rest completo.
- Shrine simples opcional ligado a um deus.
- Interrupção por ameaça.
- Telemetria básica de uso.

## Critérios de Aceite

- Descanso não funciona durante combate direto.
- Camp tem custo real e risco legível.
- Shrine nunca é cura gratuita.
- Hub Rest não recupera itens perdidos por morte.
- Nenhuma lógica de corpse lifecycle existe neste documento.

## Non-Goals

- Checkpoint gratuito em qualquer lugar.
- Pausa segura universal.
- Recuperação automática de corpse.
- Regras de morte, death snapshot, corpse placement ou Global Pool.
