> **Documento:** Ashvault Product Hub
> **Versão:** 1.8 - Product Launch Index
> **Status:** Hub macro, índice de GDDs e escopo de lançamento
> **Engine:** Unity - OpenXR / SteamVR / Input System
> **Plataformas alvo:** PC VR e PC Flatscreen
> **Idioma primário:** Português BR

---

# Ashvault Product Hub

Este documento não é mais o GDD detalhado de sistemas. Ele é o índice macro do produto, a referência de lançamento e o contrato de separação entre módulos/plugins.

## Regra de Autoridade

1. [Roadmap Content Scope](<Production/Roadmap%20Content%20Scope.md>) é soberano para fase, escopo, gates, cortes e entrada de features no MVP/Alpha/Beta.
2. Product Hub é soberano para identidade macro, índice canônico, ownership de plugins e fronteiras entre módulos.
3. GDDs de domínio são soberanos para regras internas, fórmulas, dados, edge cases e critérios de aceite dentro da fase permitida.
4. Se um GDD de domínio contradizer o Roadmap em escopo de fase, o Roadmap vence.
5. Se um GDD de domínio contradizer o Product Hub em fronteira de plugin, o conflito deve ser resolvido antes da implementação.
6. Um sistema novo só entra no produto quando tiver dono, fase de release e fronteira de plugin definida.

---

## Regra de P&D

Sistemas marcados como P&D podem existir em protótipos isolados, branches experimentais ou cenas técnicas, mas não podem:

- bloquear o MVP;
- ser requisito para boss, run, economia ou tutorial;
- gerar dependência obrigatória em GDDs de fase anterior;
- escrever em save permanente de produção sem feature gate.

---

## Identidade do Produto

Ashvault é um dungeon crawler first-person VR/Flatscreen sobre exploração vertical, risco recuperável, economia fechada, conhecimento sistêmico, crafting físico e progressão emergente.

Fantasia principal: entrar em uma dungeon viva, extrair valor, sobreviver ao risco, voltar ao Hub e transformar conhecimento em vantagem econômica e mecânica.

Fontes canônicas:

| Área                                 | Documento                                                                            |
|--------------------------------------|--------------------------------------------------------------------------------------|
| Identidade, fantasia e target player | [Game Identity & Player Fantasy](<Design/Game%20Identity%20&%20Player%20Fantasy.md>) |
| Lore, Pantheon e origem da dungeon   | [Narrative Lore & Worldbuilding](<Lore/Narrative%20Lore%20&%20Worldbuilding.md>)     |
| Arte e áudio direcional              | [Art Audio Direction](<Production/Art%20Audio%20Direction.md>)                       |
| Riscos, non-goals e métricas         | [Failure States Risk Metrics](<Production/Failure%20States%20Risk%20Metrics.md>)     |

---

## Produto de lançamento

O lançamento inicial deve provar um loop fechado, não a totalidade da fantasia final.

Loop de lançamento:

1. Preparar no Hub.
2. Entrar na dungeon semanal.
3. Explorar 3-5 andares da mina.
4. Minerar, lutar, carregar loot e registrar conhecimento.
5. Extrair ou morrer e recuperar corpse.
6. Vender, melhorar preparação e repetir.
7. Enfrentar 1 boss gate funcional.

Escopo MVP:

| Categoria   | Lançamento                                                                    |
|-------------|-------------------------------------------------------------------------------|
| Bioma       | 1 bioma: mina                                                                 |
| Dungeon     | 3-5 andares, geração procedural, reset semanal                                |
| Boss        | 1 boss com gate e rewards validados                                           |
| Recursos    | Mining, loot básico, inventário, economia e venda                             |
| Progressão  | Skills mínimas, notes, bestiário mínimo, Pantheon MVP se shrine estiver ativo |
| Morte       | Corpse recuperável, compass/mapa básico, sem NPC explorer                     |
| Input       | VR e Flatscreen com paridade funcional                                        |
| Multiplayer | Escopo restrito conforme roadmap; não bloquear MVP single/co-op mínimo        |
| QA          | Run de 15-30 minutos, save/load básico, fairness VR/Flatscreen                |

Fonte canônica de fases, cortes e gates: [Roadmap Content Scope](<Production/Roadmap%20Content%20Scope.md>).

---

## Plugins e Fronteiras

Ashvault deve ser implementado como sistemas modulares/plugins, com acoplamento explícito. Nenhum plugin deve depender de internals de outro plugin. Integrações passam por contratos, eventos, DTOs, ScriptableObjects versionados ou interfaces públicas estáveis.

Plugin core compartilhado:

| Plugin           | Responsabilidade                                            | Não deve conter              |
|------------------|-------------------------------------------------------------|------------------------------|
| Core Contracts   | IDs, eventos, tags, result types, interfaces, versionamento | regra específica de sistema  |
| Runtime Shell    | bootstrap, lifecycle, tick, scene orchestration             | fórmulas de gameplay         |
| Save/Persistence | snapshots, migration, crash recovery                        | decisão de balanceamento     |
| Telemetry/QA     | eventos de validação e métricas                             | alteração direta de gameplay |

Plugins de produto:

| Plugin               | Papel no lançamento                                           | Fonte canônica                                                                                                                                                                                                                    |
|----------------------|---------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dungeon Generator    | seed semanal, andares, validação, escadas, quedas, boss gates | [Dungeon Generation System](<Dungeon/Dungeon%20Generation%20System.md>)                                                                                                                                                           |
| Dungeon Biomes       | kit visual/mecânico da mina e expansão por biomas             | [Biome System da Dungeon](<Dungeon/Biome%20System%20da%20Dungeon.md>)                                                                                                                                                             |
| Hazards              | abismos, gás, fogo, colapso, áreas seladas                    | [Environmental Hazards and Dungeon Threats](<Dungeon/Environmental%20Hazards%20and%20Dungeon%20Threats.md>)                                                                                                                       |
| Player/Input         | estados do player, input mode e paridade                      | [Player System](<Gameplay/Player%20System.md>) + [Cross Input Policy](<Technical/Cross%20Input%20Policy.md>)                                                                                                                      |
| Interaction          | contrato unificado de interação VR/Flatscreen                 | [Interaction System](<Gameplay/Interaction%20System.md>)                                                                                                                                                                          |
| Run Session          | início, extração, falha, checkpoints e edge cases da run      | [Run System](<Gameplay/Run%20System.md>)                                                                                                                                                                                          |
| Combat               | melee, ranged, defesa, stamina, dano e status aplicados       | [Combat System](<Gameplay/Combat%20System.md>)                                                                                                                                                                                    |
| AI/Enemies           | percepção, inimigos, bosses e NPCs                            | [AI Systems](<AI/AI%20Systems.md>)                                                                                                                                                                                                |
| Noise/Audio Gameplay | eventos acústicos, propagação e audibilidade                  | [Noise & Perception System](<Gameplay/Noise%20&%20Perception%20System.md>) + [Gameplay Audio](<Technical/Gameplay%20Audio%20&%20Acoustic%20Simulation.md>)                                                                        |
| Inventory/Items      | item instances, carga, equipamento e durabilidade             | [Inventory & Carry System](<Gameplay/Inventory%20&%20Carry%20System.md>) + [Itemization & Equipment System](<Gameplay/Itemization%20&%20Equipment%20System.md>)                                                                   |
| Loot/Rewards         | drops, chests, boss rewards e origem de item                  | [Loot Drops & Reward System](<Gameplay/Loot%20Drops%20&%20Reward%20System.md>)                                                                                                                                                    |
| Economy/Hub          | loja, venda, preparação, contratos e sinks                    | [Economy System](<Gameplay/Economy%20System.md>) + [Hub Shop & Preparation System](<Gameplay/Hub%20Shop%20&%20Preparation%20System.md>)                                                                                           |
| Death/Rest           | descanso, morte, corpse e recuperação                         | [Rest System](<Gameplay/Rest%20&%20Corpse%20System.md>) + [Death Corpse & Recovery System](<Gameplay/Death%20Corpse%20&%20Recovery%20System.md>)                                                                                  |
| Progression          | skill tree, Pantheon, bestiário e notes                       | [Skill Tree System](<Gameplay/Skill%20Tree%20System.md>) + [Pantheon System](<Gameplay/Pantheon%20System.md>)                                                                                                                     |
| Crafting Suite       | mining, crafting, forging, alchemy e cooking                  | [Mining System](<Gameplay/Mining%20System.md>) + [Crafting & Forging System](<Gameplay/Crafting%20&%20Forging%20System.md>) + [Alchemy System](<Gameplay/Alchemy%20System.md>) + [Cooking System](<Gameplay/Cooking%20System.md>) |
| Multiplayer          | party, authority, save sync e co-op rules                     | [Party System & Coop Social Rules](<Technical/Party%20System%20&%20Coop%20Social%20Rules.md>) + [Multiplayer Save & Coop System](<Technical/Multiplayer%20Save%20&%20Coop%20System.md>)                                           |
| UX/UI                | onboarding, screens, comfort, accessibility                   | [UI UX Flow](<UX/UI%20UX%20Flow.md>) + [Onboarding Tutorial System](<UX/Onboarding%20Tutorial%20System.md>) + [VR UX Accessibility System](<Technical/VR%20UX%20Accessibility%20System.md>)                                       |

---

## Regras Anti-Interdependência

Estas regras existem para permitir que sistemas como o Dungeon Generator sejam desenvolvidos como plugins independentes.

1. Plugin não lê estado interno de outro plugin.
2. Plugin publica eventos e snapshots; consumidores decidem como reagir.
3. Dados compartilhados usam IDs estáveis, tags e DTOs pequenos.
4. Dependência permitida aponta para contratos, não para implementação.
5. Feature de Alpha/Beta não pode ser requisito duro para MVP.
6. GDD micro deve declarar as suas entradas, saídas, eventos, saves e critérios de aceite.
7. Evento persistido deve ter schema/version e parser ou migrator explícito.
8. O hub não guarda fórmulas, tabelas de balanceamento ou lifecycle detalhado.

Exemplo: Dungeon Generator

| Pode emitir                                                                                 | Pode consumir                                        | Não deve conhecer                                                    |
|---------------------------------------------------------------------------------------------|------------------------------------------------------|----------------------------------------------------------------------|
| `FloorGenerated`, `RoomValidated`, `GatePlaced`, `LootAnchorCreated`, `HazardAnchorCreated` | seed semanal, constraints, biome kit, runtime budget | preço de item, dano de arma, AI state machine, inventário do jogador |

Se o Dungeon Generator precisar "colocar loot", ele cria anchors e tags. O Loot plugin decide tabela, raridade, item instance e origem.

Invariantes técnicos:

- Dungeon Generator nunca instancia item econômico final.
- Dungeon Generator só cria anchor, tag, constraint e contexto.
- Loot/Reward resolve item instance, origem, raridade, qualidade e valor.
- Economy só recebe item depois de origem e instância validada.

---

## Dependency Direction

Direção recomendada de dependência:

```text
Core Contracts
  -> Runtime Shell
  -> Feature Plugins
  -> Presentation/UX
  -> Telemetry/QA
```

Fluxo de gameplay esperado:

```text
Input -> Interaction -> Player -> Run
Run -> Dungeon Generator -> Anchors
Anchors -> Loot/AI/Hazards
Inventory -> Economy -> Hub
Combat -> Noise -> AI
Death/Rest -> Persistence -> Run
Telemetry observa, não comanda.
```

Dependências proibidas no MVP:

| De                | Para                      | Motivo                                           |
|-------------------|---------------------------|--------------------------------------------------|
| Dungeon Generator | Economy internals         | loot deve passar por anchors e Loot plugin       |
| Combat            | UI concreta               | feedback passa por eventos/view models           |
| AI                | Inventory internals       | IA reage a percepção, não a estrutura de item    |
| Economy           | Combat internals          | precificação usa item metadata, não dano runtime |
| Persistence       | Scene objects crus        | save usa snapshots serializáveis                 |
| Telemetry         | Mutação direta de domínio | telemetria observa e recomenda balanceamento     |

---

## Índice Canônico de GDDs

### Direção e Produção

| Documento                                                                                    | Uso                                      |
|----------------------------------------------------------------------------------------------|------------------------------------------|
| [Roadmap Content Scope](<Production/Roadmap%20Content%20Scope.md>)                           | fases, gates, cortes e escopo de release |
| [MVP Implementation Plan](<Production/MVP%20Implementation%20Plan.md>)                       | marcos técnicos do MVP                   |
| [QA Playtesting & Validation Plan](<Production/QA%20Playtesting%20&%20Validation%20Plan.md>) | matriz de validação                      |
| [VRChat Social Acquisition](<Production/VRChat%20Social%20Acquisition.md>)                   | estratégia externa de aquisição social   |
| [Art Audio Direction](<Production/Art%20Audio%20Direction.md>)                               | direção audiovisual                      |
| [Failure States Risk Metrics](<Production/Failure%20States%20Risk%20Metrics.md>)             | riscos, non-goals e métricas             |

### Gameplay

| Documento                                                                              | Uso                                       |
|----------------------------------------------------------------------------------------|-------------------------------------------|
| [Player System](<Gameplay/Player%20System.md>)                                         | entidade jogador, estados e dano          |
| [Combat System](<Gameplay/Combat%20System.md>)                                         | combate base                              |
| [Interaction System](<Gameplay/Interaction%20System.md>)                               | interação física e abstrata               |
| [Inventory & Carry System](<Gameplay/Inventory%20&%20Carry%20System.md>)               | inventário e carga                        |
| [Itemization & Equipment System](<Gameplay/Itemization%20&%20Equipment%20System.md>)   | equipamento, raridade e instancia de item |
| [Status Effects System](<Gameplay/Status%20Effects%20System.md>)                       | runtime unificado de efeitos              |
| [Run System](<Gameplay/Run%20System.md>)                                               | lifecycle da run                          |
| [Rest System](<Gameplay/Rest%20&%20Corpse%20System.md>)                                | Breath, Camp, Shrine e Hub Rest           |
| [Death Corpse & Recovery System](<Gameplay/Death%20Corpse%20&%20Recovery%20System.md>) | morte, corpse, recovery e global pool     |
| [Loot Drops & Reward System](<Gameplay/Loot%20Drops%20&%20Reward%20System.md>)         | rewards e drops                           |
| [Quest & Contract System](<Gameplay/Quest%20&%20Contract%20System.md>)                 | contratos                                 |
| [Hub Shop & Preparation System](<Gameplay/Hub%20Shop%20&%20Preparation%20System.md>)   | Hub e preparação                          |

### Sistemas Sistêmicos

| Documento                                                                  | Uso                        |
|----------------------------------------------------------------------------|----------------------------|
| [Arcane Magic System](<Gameplay/Arcane%20Magic%20System.md>)               | magia linguística          |
| [Mining System](<Gameplay/Mining%20System.md>)                             | mineração                  |
| [Harvesting System](<Gameplay/Harvesting%20System.md>)                     | harvesting                 |
| [Crafting & Forging System](<Gameplay/Crafting%20&%20Forging%20System.md>) | crafting e forja           |
| [Alchemy System](<Gameplay/Alchemy%20System.md>)                           | alquimia por fase          |
| [Cooking System](<Gameplay/Cooking%20System.md>)                           | comida e buffs             |
| [Bestiary System](<Gameplay/Bestiary%20System.md>)                         | conhecimento de criaturas  |
| [Note System](<Gameplay/Note%20System.md>)                                 | notes e descobertas        |
| [Skill Tree System](<Gameplay/Skill%20Tree%20System.md>)                   | progressão por uso         |
| [Pantheon System](<Gameplay/Pantheon%20System.md>)                         | deuses, afinidade e shrine |

### Dungeon, AI e Técnico

| Documento                                                                                                               | Uso                               |
|-------------------------------------------------------------------------------------------------------------------------|-----------------------------------|
| [Dungeon Generation System](<Dungeon/Dungeon%20Generation%20System.md>)                                                 | plugin de geração procedural      |
| [Biome System da Dungeon](<Dungeon/Biome%20System%20da%20Dungeon.md>)                                                   | biomas                            |
| [Boss Gates Vertical Locks & Floor Progression](<Dungeon/Boss%20Gates%20Vertical%20Locks%20&%20Floor%20Progression.md>) | gates e progressão vertical       |
| [Environmental Hazards and Dungeon Threats](<Dungeon/Environmental%20Hazards%20and%20Dungeon%20Threats.md>)             | hazards                           |
| [AI Systems](<AI/AI%20Systems.md>)                                                                                      | IA geral                          |
| [Enemy System](<AI/Enemy%20System.md>)                                                                                  | inimigos                          |
| [Boss System](<AI/Boss%20System.md>)                                                                                    | bosses e eligibility              |
| [NPC System](<AI/NPC%20System.md>)                                                                                      | NPCs                              |
| [Production Tech Contracts](<Technical/Production%20Tech%20Contracts.md>)                                               | contratos, tick e limites runtime |
| [Persistence System](<Technical/Persistence%20System.md>)                                                               | save/load                         |
| [Multiplayer Save & Coop System](<Technical/Multiplayer%20Save%20&%20Coop%20System.md>)                                 | autoridade, save e co-op          |
| [Party System & Coop Social Rules](<Technical/Party%20System%20&%20Coop%20Social%20Rules.md>)                           | party e regras sociais            |
| [Cross Input Policy](<Technical/Cross%20Input%20Policy.md>)                                                             | política VR/Flatscreen            |
| [Animation Physics & Interaction Feel](<Technical/Animation%20Physics%20&%20Interaction%20Feel.md>)                     | feel, IK e física                 |
| [Gameplay Audio & Acoustic Simulation](<Technical/Gameplay%20Audio%20&%20Acoustic%20Simulation.md>)                     | audio como gameplay               |
| [Telemetry Balance System](<Technical/Telemetry%20Balance%20System.md>)                                                 | telemetria e balanceamento        |
| [Localization System](<Technical/Localization%20System.md>)                                                             | localização                       |
| [UI UX Flow](<UX/UI%20UX%20Flow.md>)                                                                                    | fluxo de UI                       |
| [Onboarding Tutorial System](<UX/Onboarding%20Tutorial%20System.md>)                                                    | onboarding                        |
| [VR UX Accessibility System](<Technical/VR%20UX%20Accessibility%20System.md>)                                           | conforto VR e acessibilidade      |

---

## Checklist Para Adicionar Novo Plugin

Antes de adicionar um novo plugin ao produto:

1. Definir fase: MVP, Alpha, Beta ou Backlog.
2. Criar GDD micro próprio ou declarar documento autoritativo existente.
3. Declarar entradas, saídas, eventos e dados persistidos.
4. Declarar quais plugins pode consumir por contrato.
5. Declarar o que ele explicitamente não conhece.
6. Adicionar critério de aceite no GDD micro.
7. Adicionar link neste hub somente depois dos passos acima.

---

## Histórico

Versões anteriores deste arquivo continham detalhes de sistemas. A partir da v1.8, esses detalhes foram movidos para GDDs de domínio para evitar duplicação, desync e interdependência total entre plugins.
