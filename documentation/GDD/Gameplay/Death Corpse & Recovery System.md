# Death, Corpse and Recovery System

> Documento de domínio derivado de [Ashvault v1.6](<../Ashvault v1.6.md>). Este é o documento autoritativo para morte, criação de corpse, recuperação de corpse, expiração e Global Pool. Descanso, Breath, Camp, Shrine e Hub Rest ficam em [Rest System](<Rest & Corpse System.md>).

## High Concept

Morte é falha com continuidade. O jogador perde parte do valor da run e cria um objetivo recuperável. O sistema deve punir ganância, queda, erro tático e despreparo sem apagar progresso estrutural.

## Death Flow

1. Player chega a estado fatal.
2. RunManager registra `DeathSnapshot`.
3. Inventory separa meta, run items e itens especiais.
4. Corpse é criado em posição válida.
5. Economy calcula valor perdido/recuperável.
6. UI mostra causa, valor e próxima decisão.
7. Persistence grava snapshot e corpse.

## Corpse Compass

O jogo pode fornecer direção aproximada, não GPS perfeito. No MVP, essa informação vem apenas de compass, mapa básico, proximidade e party ping. Informação por NPC Explorer é Alpha.

## Corpse Placement

```text
ValidCorpsePosition =
  NearDeathPosition
  && OnNavigableSurface
  && NotBehindLockedGate
  && NotInsideHazardInstantDeath
  && RecoverableByOwnerProgress
```

## Loot Perdido

| Categoria | Regra |
| --- | --- |
| Meta knowledge | não perde |
| Notes físicas | podem ficar no corpse se não transcritas |
| Run resources | parcialmente no corpse |
| Equipped gear | retém ou danifica conforme regra |
| Quest item | regra específica |
| Currency liquidada | não volta para corpse |

## NPCs Encontrando Corpse

[Alpha] NPC explorer pode reportar localização, vender informação sobre o corpse ou recuperar parte dos itens mediante contrato. Se a facção permitir, pode saquear o corpse após timeout. [Ver NPC System](<../AI/NPC System.md>).

## MVP Sem NPC Explorer

O jogador recebe direção aproximada via compass ou marcação no mapa básico. Party pode fazer ping do corpse. Não há NPC ativo nessa função no MVP.

## Party Recovery

Party pode marcar corpse, proteger área, carregar item crítico ou recuperar parte do loot conforme owner rules. Recuperação não transfere propriedade automaticamente sem regra.

## Death Contexts

| Contexto | Regra especial |
| --- | --- |
| Queda | corpse reposicionado no andar inferior válido |
| Boss | corpse fora da arena ou dentro conforme boss policy |
| Andar descarregado | snapshot recria estado mínimo |
| Weekly reset | expira, converte ou move para global pool |
| Co-op | owner explícito e recovery party-aware |

## Global Pool

Global Pool é um pool controlado de itens vindos de corpses expirados. Ele não gera loot novo: apenas conserva uma fração de valor de itens que já existiram, preservando origem, owner anterior e contexto de perda.

### Quando é Populado

O Global Pool pode receber itens quando:

1. Um corpse expira sem recuperação.
2. Um weekly reset converte corpses antigos conforme policy.
3. [Alpha] Um NPC explorer encontra um corpse expirado e reporta o achado.
4. Um contrato de recuperação falha e a regra manda o restante para pool.

```text
GlobalPoolContribution =
  UnrecoveredCorpseValue
  * PoolRetentionRatio
  * ItemEligibilityModifier
```

### Quem Pode Acessar

| Acesso | Regra |
| --- | --- |
| Owner original | pode receber contrato ou pista prioritária |
| Party do owner | apenas se contrato permitir compartilhamento |
| NPC Explorer | [Alpha] pode recuperar, vender informação ou negociar retorno |
| Contract Board | [Alpha] pode gerar contrato de recuperação/compra |
| Mercado comum | não acessa diretamente itens rastreáveis do pool |

### Como é Acessado

- Contrato de recuperação.
- [Alpha] Serviço pago de NPC explorer.
- Evento raro de mercado com origem explícita.
- Quest de resgate de item.
- Recompensa parcial por reputação/facção.

### Limpeza

O Global Pool deve ser limpo de forma previsível:

- item reclamado por contrato: removido imediatamente;
- item inválido/corrompido: removido na validação;
- item não reclamado: removido no fim do reset semanal seguinte;
- mudança de versão/schema: item vai para quarantine ou é liquidado como valor perdido, nunca duplicado.

### Modelo de Dados

```json
{
  "global_pool_entry_id": "gp_week07_corpse_042_iron_ore_017",
  "source_corpse_id": "corpse_player_01_run_042",
  "previous_owner_id": "player_01",
  "item_instance_id": "run_042_iron_ore_017",
  "value_retained": 42,
  "week_created": 7,
  "expires_end_of_week": 8,
  "access_policy": "owner_priority_contract"
}
```

### Constraints

- Global Pool não pode duplicar item instance.
- Itens no pool não entram no mercado comum sem contrato ou NPC.
- Origem e previous owner são preservados.
- Itens de quest usam regra própria e podem nunca entrar no pool.
- Pool não substitui corpse recovery; ele é fallback tardio e caro.

## Telemetry

- Cause.
- Floor.
- Carried value.
- Encumbrance.
- Combat state.
- Recovery attempt count.
- Corpse recovered value.
- Global Pool entries created, claimed, expired and cleaned.

## MVP

- Um corpse ativo por jogador.
- Recuperação parcial.
- Cause of death visível.
- Corpse placement validado.
- Crash recovery de morte.
- Direção aproximada via compass ou marcação no mapa básico.
- Party ping do corpse.
- Sem NPC Explorer ativo nessa função.

## Critérios de Aceite

- Morte nunca duplica item.
- Corpse é recuperável por rota válida.
- Jogador entende causa e perda.
- Conhecimento estrutural não é apagado.
- Global Pool preserva origem e expira no prazo definido.

## Cross-References

- Descanso, Breath, Camp, Shrine e Hub Rest: [Rest System](<Rest & Corpse System.md>).
- Run lifecycle e liquidação: [Run System](<Run System.md>).
- Loot ownership e item origin: [Loot, Drops and Reward System](<Loot Drops & Reward System.md>) e [Itemization and Equipment System](<Itemization & Equipment System.md>).
- Contratos de recuperação: [Quest and Contract System](<Quest & Contract System.md>).
