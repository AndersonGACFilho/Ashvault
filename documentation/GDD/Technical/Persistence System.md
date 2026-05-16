# Persistence System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Consolida save/load, domínios e crash recovery.

## High Concept

Persistência separa progresso permanente, estado de run, mercado, dungeon semanal e configurações. Misturar domínios torna morte, reset e co-op frágeis.

## Domínios

- PlayerMeta
- RunState
- WorldWeekState
- MarketState
- DungeonSeedState
- Settings
- TelemetryBuffer

## Regras

- Escrita atômica.
- Versionamento de schema.
- Migração explícita.
- Crash recovery com último checkpoint válido.
- Dados econômicos validados contra origem.

## MVP

- Save local versionado.
- PlayerMeta e RunState separados.
- Backup simples.
- Recuperação após crash.

## Critérios de Aceite

- Morte não corrompe meta progressão.
- Reset semanal não apaga dados permanentes.
- Load inválido falha de modo seguro.

## Save Envelope

```json
{
  "schema_version": 3,
  "profile_id": "player_01",
  "created_at": "2026-05-15T18:00:00Z",
  "checksum": "sha256...",
  "domains": {
    "player_meta": {},
    "run_state": {},
    "market_state": {},
    "world_week_state": {},
    "settings": {}
  }
}
```

## Atomic Write

1. Serializar domínio em memória.
2. Validar schema.
3. Escrever `.tmp`.
4. Verificar checksum.
5. Renomear para arquivo final.
6. Manter backup anterior.

## Migração

Migrações devem ser explícitas por versão. Load de versão desconhecida falha para backup ou perfil protegido. Migração nunca deve inventar valor econômico sem origem.

## Crash Recovery

Prioridade de recuperação:

1. Save final válido.
2. Backup anterior válido.
3. Snapshot de checkpoint.
4. Perfil meta sem run ativa.

## Legacy Run Persistence

Quando `WeekResetCommitted` encontra `RunState` ativo, Persistence não mistura a run antiga com `WorldWeekState` novo. O save mantém:

- `legacy_run: true`;
- `original_week_seed`;
- `legacy_run_lock: true`;
- snapshots de floor, loot origin e contratos temporários;
- expiry policy para corpse/contratos da semana antiga;
- bloqueio contra abrir segunda run até a legacy run terminar ou ser abandonada.

Migração de save nunca converte item, corpse ou contrato legado em recurso da nova semana sem evento de liquidação emitido pelo RunManager.

Validação obrigatória:

```text
ValidLegacyRun =
  legacy_run == true
  && original_week_seed != current_week_seed
  && RunStateHasSingleOwner
  && NoMarketDeltaWithoutRunLiquidated
  && NoDuplicateCorpseForRun
```

## Testes Obrigatórios

- Crash durante venda.
- Crash durante morte.
- Crash durante transição de andar.
- Schema antigo migrando para novo.
- Save corrompido carregando fallback.

## Non-Goals

- Save único monolítico.
- Persistir objeto runtime inteiro sem schema.
- Corrigir corrupção inventando recursos.

## Contratos por Domínio

| Domínio | Frequência de save | Pode ser descartado? | Observação |
| --- | --- | --- | --- |
| PlayerMeta | após mudança permanente | não | skills, flags, unlocks |
| RunState | checkpoint e transição crítica | sim, com fallback | estado mutável da run |
| MarketState | após transação confirmada | não | precisa anti-dupe |
| WorldWeekState | início/fim de semana | não | seed, demandas, reset |
| Settings | ao alterar opção | sim | pode voltar ao default |
| TelemetryBuffer | batch | sim | não bloqueia gameplay |

## Eventos que Disparam Save

- `RunStarted`
- `FloorTransitionCompleted`
- `PlayerDied`
- `CorpseCreated`
- `CorpseRecovered`
- `ItemSold`
- `BossDefeated`
- `WeekResetCommitted`
- `SettingsChanged`

## Política de Consistência

Persistência deve favorecer consistência econômica sobre conveniência. Se houver dúvida entre perder uma transação recém-feita ou duplicar recurso, o sistema deve preservar o último estado comprovadamente válido e registrar erro.

## Validação de Load

```text
ValidSave =
  ChecksumValid
  && SchemaVersionSupported
  && RequiredDomainsPresent
  && NoDuplicateItemInstances
  && MarketTransactionsBalanced
  && RunStateReferencesExistingDefinitions
```

## Edge Cases

| Caso | Resposta esperada |
| --- | --- |
| Crash após remover item e antes de creditar moeda | rollback da transação |
| Crash após morte e antes de criar corpse | recriar corpse a partir do death snapshot |
| Load com item sem definição | mover para quarantine e não vender |
| Week reset durante run ativa | marcar `legacy_run` e preservar `original_week_seed` |
| Versão antiga sem campo obrigatório | migração com default explícito |

## Estrutura Recomendada

```text
/Saves
  /Profiles
    player_01.meta.json
    player_01.meta.bak.json
  /Runs
    run_042.state.json
  /World
    week_007.world.json
  /Market
    market_state.json
```

## Critérios Técnicos de Saída

- 100 saves consecutivos sem corrupção.
- Teste automatizado de crash em cada evento crítico.
- Nenhum item duplicado após reload/reconnect.
- Migração de pelo menos uma versão anterior testada.
