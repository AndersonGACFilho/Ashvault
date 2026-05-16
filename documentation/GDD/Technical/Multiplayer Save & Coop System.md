# Multiplayer Save & Coop System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Consolida co-op, pilares de save, boss instanciado e sincronização.

## High Concept

Multiplayer deve preservar risco individual, progresso persistente e equivalência cross-input. Co-op existe para colaboração tática, não para carregar progresso sem custo.

## Pilares

- Save individual autoritativo.
- Run compartilhada com estados sincronizados.
- Boss instanciado por elegibilidade.
- Teleporte coesivo por escada e transição.
- Cross-mode co-op permitido com fairness.

## Dados Sincronizados

- Player transform e estado.
- Inventário relevante da run.
- Eventos de interação.
- Dano, status e morte.
- Estado de sala, inimigos e boss.
- Transições de andar.

## Regras

- Recursos econômicos são validados pelo host/servidor.
- Progresso permanente só é concedido por critérios individuais.
- Desconexão deve preservar integridade de run e save.

## MVP

- 2 jogadores.
- Sincronização de posição, dano e loot.
- Extração conjunta.
- Boss single eligibility flag.

## Critérios de Aceite

- Não há duplicação de recursos por lag, desconexão ou trade.
- Co-op cross-input mantém custo equivalente.
- Save individual não é corrompido por estado compartilhado.

## Autoridade

| Domínio | Autoridade | Observação |
| --- | --- | --- |
| Movimento | dono com validação | suavização e rollback leve |
| Dano | host/servidor | evita divergência de morte |
| Loot/economia | host/servidor | origem e transação obrigatórias |
| Boss rewards | host/servidor + save individual | elegibilidade por jogador |
| Configuração local | cliente | nunca altera simulação |

## Boss Rewards

Boss rewards em co-op seguem os thresholds autoritativos definidos em [Boss System](<../AI/Boss System.md>) na seção `Eligibility Thresholds`.

Regras de multiplayer:

- O host/servidor valida `HasUnlockedGate`, `ParticipatedAboveThreshold` e `SurvivedOrPaidCorpseCost`.
- Jogador sem `required_flag` do `gate_id` pode ajudar se a arena permitir, mas não recebe reward estrutural nem unlock persistente.
- Não existe bloqueio por "overleveled" de stats; o anti-carry é apenas por progressão de gate.
- Suporte sem dano direto conta se esteve presente na arena por pelo menos 60% da luta, conforme threshold do Boss System.
- Rewards confirmados são gravados por transação idempotente no save individual.

## Session Data

```json
{
  "session_id": "coop_042",
  "host_player_id": "player_01",
  "week_seed": 7712,
  "run_id": "run_042",
  "members": [
    { "player_id": "player_01", "input_mode": "vr", "eligible_flags": ["mine_gate_01"] },
    { "player_id": "player_02", "input_mode": "flatscreen", "eligible_flags": [] }
  ],
  "sync_budget_kbps": 96
}
```

## Desconexão

- Jogador desconectado vira ghost/inactive por janela curta.
- Inventário de run é congelado ou dropado conforme contexto.
- Host migration só entra após MVP.
- Save individual só grava transações confirmadas.

## Teleporte Coesivo

Transições de escada exigem quorum configurável, distância máxima ou timer. Jogadores fora da zona recebem aviso e consequência, não teleporte silencioso sem contexto.

## Anti-Dupe

- Todo item tem `origin` e `transaction_id`.
- Venda consome item instance no servidor.
- Reconnect não reenvia rewards já confirmados.
- Corpse multiplayer possui owner explícito.

## Non-Goals

- MMO.
- PvP no escopo base.
- Host migration no MVP.
- Economia peer-to-peer sem validação.

## Sync Channels

| Canal | Frequência | Conteúdo |
| --- | --- | --- |
| Transform | alta | posição, rotação, pose simplificada |
| Combat | média/alta | hits, dano, stagger |
| Interaction | evento | begin, cancel, complete |
| Economy | evento authoritative | venda, trade, reward |
| Dungeon | evento | sala ativada, transição, gate |
| Save | checkpoint | flags e snapshots confirmados |

## Interest Management

O MVP pode sincronizar apenas o andar/sala ativa. Objetos fora de interesse usam estado agregado: inimigo vivo/morto, sala limpa, recurso extraído, porta aberta.

## Coop Failure Cases

| Caso | Resposta |
| --- | --- |
| Jogador morre | corpse individual, run compartilhada continua se regra permitir |
| Host fecha jogo | salvar snapshot e encerrar sessão |
| Cliente desconecta com loot | loot congela até timeout ou dropa |
| Dois jogadores pegam mesmo item | servidor confirma primeiro transaction_id |
| Boss morto com player inelegível | reward separado por elegibilidade |

## Multiplayer Save Contract

```json
{
  "player_id": "player_01",
  "session_id": "coop_042",
  "confirmed_transactions": ["tx_001", "tx_002"],
  "pending_transactions": [],
  "progress_flags_granted": ["mine_gate_01"],
  "corpse_refs": ["corpse_player_01_run_042"]
}
```

## Critérios de Saída

- Dupla completa uma run sem duplicação.
- Morte de um jogador não corrompe save do outro.
- Reconexão não duplica reward.
- Cross-input mantém action mapping compartilhado.
