# Ashvault

Ashvault is a first-person VR/Flatscreen dungeon crawler design project about vertical exploration, recoverable risk, closed-loop economy, systemic knowledge, physical crafting, and emergent progression.

Ashvault is solo developed.

This repository currently contains the production documentation and GDD set for the project. The documents are organized to separate product governance, phase scope, domain rules, and implementation planning.

## Start Here

| Document                                                                                        | Purpose                                                                         |
|-------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| [Ashvault Product Hub](<documentation/GDD/Ashvault%20v1.6.md>)                                  | Macro identity, canonical index, plugin ownership, and module boundaries        |
| [Roadmap Content Scope](<documentation/GDD/Production/Roadmap%20Content%20Scope.md>)            | Sovereign source for phase scope, gates, cuts, and MVP/Alpha/Beta entry         |
| [MVP Implementation Plan](<documentation/GDD/Production/MVP%20Implementation%20Plan.md>)        | Executable milestone plan for the MVP                                           |
| [M0 Core Contracts Backlog](<documentation/GDD/Production/M0%20Core%20Contracts%20Backlog.md>)  | First technical backlog: IDs, events, registry, save envelope, telemetry schema |
| [Production Tech Contracts](<documentation/GDD/Technical/Production%20Tech%20Contracts.md>)     | Shared runtime contracts, event bus, tick order, validators, and boundaries     |

## Authority Model

The documentation follows a strict authority hierarchy:

1. `Roadmap Content Scope` is sovereign for phase, scope, gates, cuts, and feature entry into MVP/Alpha/Beta.
2. `Ashvault Product Hub` is sovereign for macro identity, canonical index, plugin ownership, and module boundaries.
3. Domain GDDs are sovereign for internal rules, formulas, data, edge cases, and acceptance criteria within their allowed phase.
4. If a domain GDD contradicts the Roadmap on phase scope, the Roadmap wins.
5. If a domain GDD contradicts the Product Hub on plugin boundaries, the conflict must be resolved before implementation.

## MVP Loop

The launch MVP is scoped to prove a closed loop:

```text
Hub -> Run -> Dungeon 3-5 floors -> Mining/Combat/Carry/Notes -> Extraction/Death -> Economy/Upgrade -> Boss Gate
```

MVP content target:

- 1 biome: mine.
- 3-5 dungeon floors.
- 1 boss gate.
- Mining, combat, inventory, economy, run loop, basic shop.
- Minimal notes and bestiary.
- Recoverable corpse loop.
- VR and flatscreen functional parity.

## MVP Milestones

| Milestone                             | Goal                                                                            |
|---------------------------------------|---------------------------------------------------------------------------------|
| M0 - Core Contracts + Runtime Shell   | IDs, EventBus, definitions, tick order, save envelope, telemetry schema         |
| M1 - Run + Dungeon Floor Skeleton     | Run start, weekly seed, generated floor, spawn, extraction placeholder, anchors |
| M2 - Interaction + Inventory + Mining | Interaction contract, pickup/carry, mining node, item origin                    |
| M3 - Extraction + Economy + Hub Shop  | Valid extraction, selling, shop counter, stash, market delta                    |
| M4 - Combat + Noise + 2 Enemies       | Damage, stamina, stagger, NoiseAuthority MVP, enemy alert/investigate/attack    |
| M5 - Corpse + Save/Load + Legacy Run  | Death, corpse recovery, crash recovery, legacy run policy                       |
| M6 - Boss Gate MVP                    | 1 boss, eligibility, LayerLockValidator, idempotent reward                      |
| M7 - QA/Fairness/Telemetry            | 100 seeds, event schemas, VR/Flat checks, run summary, death causes             |

## Plugin Boundary Rules

Ashvault should be implemented as modular systems/plugins with explicit coupling:

- Plugins do not read internal state from other plugins.
- Integrations go through contracts, events, DTOs, stable IDs, snapshots, versioned data assets, or public interfaces.
- Feature plugins cannot require Alpha/Beta/P&D systems for the MVP.
- Persisted events must include schema/version and have an explicit parser or migrator.
- Dungeon Generator never instantiates final economic items. It emits anchors, tags, constraints, and context; Loot/Reward resolves item instances, origin, rarity, quality, and value.

## P&D Rule

Systems marked as P&D may exist in isolated prototypes, experimental branches, or technical scenes, but they cannot:

- block the MVP;
- be required for boss, run, economy, or tutorial;
- create mandatory dependencies in earlier-phase GDDs;
- write to permanent production saves without a feature gate.

Examples include Arcane Magic, GOAP/HTN, MORL, advanced Hive behavior, and deep NPC social simulation.

## Repository Layout

```text
documentation/
  GDD/
    Ashvault v1.6.md
    AI/
    Design/
    Dungeon/
    Gameplay/
    Lore/
    Production/
    Technical/
    UX/
```

## Current Production Entry Point

Implementation should start with:

```text
M0 - Core Contracts + Runtime Shell
```

See [M0 Core Contracts Backlog](<documentation/GDD/Production/M0%20Core%20Contracts%20Backlog.md>) for the first executable backlog.
