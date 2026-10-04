# Glossary — Unreal terms for someone from data / web development

| Term | Meaning | Analogy |
|---|---|---|
| **Level (Map)** | A scene: everything placed in a world. Saved as `.umap`. | A page / app screen |
| **Actor** | Anything placed in a Level. | A DOM element / object |
| **Component** | A part attached to an Actor (mesh, camera, collision, movement). | Child element / plugin of an object |
| **Pawn** | An Actor that can be controlled (possessed). | |
| **Character** | A Pawn with walking movement and a capsule collider built in. | A ready-made controllable entity |
| **Controller / PlayerController** | The "brain" that possesses a Pawn: player input or AI. | Controller in MVC |
| **GameMode** | The rules of the match: which Pawn to spawn, which HUD, start/end conditions. One per Level. | Application config + orchestrator |
| **GameState / PlayerState** | Replicated info about the match / a player (score, time). | App state store |
| **HUD / UMG / Widget Blueprint** | On-screen UI built visually. | React components |
| **Blueprint** | Visual-script class. A Blueprint *class* is a template; placed copies are *instances*. | Class vs object |
| **Event Graph** | Where Blueprint logic lives. | Function bodies / event handlers |
| **Event Dispatcher** | A Blueprint event others can subscribe to. | Event emitter / observer |
| **Blueprint Interface** | A contract of functions that unrelated classes can implement. | TypeScript interface |
| **Cast** | Asking "is this object of type X?" to access its members. | `instanceof` + downcast |
| **Tick** | Code that runs every frame. Expensive when many actors tick. | requestAnimationFrame loop |
| **Timer** | Runs something after/every N seconds without Tick. | `setInterval` |
| **Spawn Actor** | Create an instance at runtime. | `new`, `document.createElement` |
| **Data Table** | A table of rows from a struct, importable from CSV/JSON. | A database table / config file |
| **Struct** | Custom data type (a row type, e.g. `FMonsterRow`). | Interface / dataclass |
| **Enum** | Fixed list of values (e.g. wave patterns). | `enum` |
| **Enhanced Input** | The modern input system: **Input Actions** (what) + **Input Mapping Contexts** (which keys). | Event + keybinding config |
| **Static Mesh** | 3D model that doesn't deform (props, floors). | |
| **Skeletal Mesh** | 3D model with a skeleton for animation (characters). | |
| **Animation Blueprint (AnimBP)** | State machine that picks animations from gameplay variables (speed → idle/run). | |
| **Montage** | A one-off animation you trigger (attack). | |
| **IK Retargeter** | Tool that transfers animations between different skeletons. | |
| **Material / Material Instance** | Shader recipe / cheap variation of one. | CSS class / variant |
| **Nanite / Lumen** | UE5 virtual geometry / dynamic global illumination. Optional; we can turn them off for a swarm game. | |
| **Niagara** | The VFX system (particles, trails). | |
| **Nav Mesh** | Generated walkable surface for AI pathfinding. Needs a *Nav Mesh Bounds Volume* in the level. | |
| **Collision channels / Overlap vs Block** | Rules for what hits what; *overlap* fires events without blocking movement. | |
| **Content Browser** | The editor's asset manager. | |
| **Play In Editor (PIE)** | Running the game inside the editor. | Dev server |
| **Packaging / Cooking** | Producing a standalone Windows build. | Production build |
| **Redirector** | A stub left when an asset is moved; "Fix Up Redirectors" cleans it. | |
| **Derived Data Cache** | Cached compiled assets/shaders (`DerivedDataCache/`, not in git). | Build cache |
| **uu / Unreal Unit** | 1 unit = 1 cm. | |
| **Mass Entity** | Data-oriented system for huge crowds (advanced; maybe Phase 6). | ECS |
| **Fab** | Epic's unified marketplace (replaced Unreal Marketplace / Quixel Bridge content). | npm for assets |
| **LFS (Git Large File Storage)** | Stores big binaries outside normal git history. | |
