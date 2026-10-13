# Roadmap — Branded Descent in Unreal Engine 5.8

No dates: this is an ordered to-do list. Work top to bottom; don't skip phases. Tick boxes (`[x]`) as we finish things.
Each phase ends with a **Done when** line so we know it is really finished.

> **Current focus:** Phase 1 — Editor orientation (2 small Phase 0 leftovers: editor preference, `Content/VSL` folders)
> **Direction:** boss-focused action game with a tilted camera (D-017, 2026-10-13). Phases 2, 3, 4 and 6 were rewritten for it.
> **Last updated:** 2026-10-13 (direction change D-017)

Legend: 🧠 = concept to understand, 🛠 = hands-on step, 🔎 = verify in 5.8 / needs a screenshot.

---

## Phase 0 — Housekeeping (do once, ~everything else depends on it)

*Why: a clean project name and version control now saves hours later.*

- [x] 🛠 Find the `.uproject` file in the project folder; check its name (Open Issue #1) — project is now `VampireSurvivorLike.uproject`, clean name
- [x] 🛠 Recreate the project with a clean name if needed — done: `D:\UE_Projects\VampireSurvivorLike`, Blueprint, engine 5.8
- [x] 🛠 Record the chosen template in `docs/decisions.md` — **Top Down** template (D-014)
- [x] 🛠 Copy `CLAUDE.md`, `docs/` and `setup/` into the project root (`setup/` stays only in the knowledge-base copy)
- [x] 🛠 Copy `unreal.gitignore` → `.gitignore` and `unreal.gitattributes` → `.gitattributes`
- [x] 🛠 `git init`, `git lfs install`, remote, first commit, push — done and verified on GitHub (244 `.uasset` files via LFS, no `Intermediate/` or `Saved/`)
- [ ] 🛠 Editor Preferences: turn off **Use Less CPU when in Background** (search "CPU" in Edit → Editor Preferences) 🔎
- [ ] 🛠 Create the `Content/VSL/...` folder layout from `CLAUDE.md` (right-click in Content Browser → New Folder). Note: git doesn't track empty folders; they appear in the repo once they hold an asset
- [x] 🛠 Confirm Blender opens and Meshy account works (done: models already generated)

**Done when:** the project opens, the repo on GitHub contains it (no `Intermediate/` or `Saved/`), and the knowledge base is inside it.

---

## Phase 1 — Editor orientation (no code yet)

*Why: you can't build in a tool you can't navigate. Goal: stop feeling lost.* Read `docs/unreal-editor-guide.md` first.

- [ ] 🧠 Identify the five main areas: Viewport, Content Browser, Outliner, Details, Toolbar
- [ ] 🛠 Tour the **Top Down template** we started from: open `Lvl_TopDown` (Content/TopDown), press Play and click to move; then open `BP_TopDownCharacter`, `BP_TopDownController`, `BP_TopDownGameMode` and `IMC_Default` just to *read* them (see "Top Down template tour" in `docs/unreal-editor-guide.md`)
- [ ] 🛠 Create a new empty level `L_Sandbox` (File → New Level → Empty Level), save it into `Content/VSL/Maps`
- [ ] 🛠 Fly around the viewport: hold right mouse + WASD/QE; scroll to change speed; `F` to focus a selected object
- [ ] 🛠 Place a **Cube** (Place Actors panel → Shapes) and move/rotate/scale it with `W` / `E` / `R`
- [ ] 🛠 Make a floor: scale a cube flat and wide (about 50 x 50 x 0.2). This is the arena
- [ ] 🛠 Add a **Directional Light** and a **Player Start**
- [ ] 🛠 Press Play (`Alt+P`), look around, press `Esc` to stop. Notice what changes between editing and playing
- [ ] 🛠 Create your first Blueprint: right-click in Content Browser → Blueprint Class → Actor → `BP_Test`, drag it into the level, add a mesh component, compile, save
- [ ] 🛠 Import one Meshy FBX into `Content/VSL/Art/...` and drop it in the sandbox just to see it (details in `docs/asset-pipeline.md`). The cleaned witch FBX files in `D:\UE_Projects\models\witch-backup\export` are a good first test: expect 180 cm tall and facing +X 🔎

**Done when:** you can open the project, find any asset, place and move objects, press Play and stop, and save without hunting for menus.

---

## Phase 2 — Core framework: a player you can move

*Why: this teaches the Unreal gameplay framework (GameMode, Pawn/Character, Controller, Input) that everything else sits on.*

- [ ] 🧠 Understand: GameMode, Character, PlayerController, Possess, Input Mapping Context (see `docs/glossary.md`)
- [ ] 🛠 Create level `L_Arena` (copy the sandbox floor and lights)
- [ ] 🛠 Create `BP_VSGameMode`; in World Settings set it as GameMode Override for `L_Arena`
- [ ] 🛠 Create `BP_PlayerCharacter` (parent: Character) with a placeholder mesh (cube/capsule) 
- [ ] 🛠 Add **Spring Arm + Camera**, tilted down about 45° to start, fixed (no player camera rotation); set `Use Pawn Control Rotation` off. The pitch is the **Y** rotation value of the Spring Arm; tick **Absolute Rotation** so it doesn't turn with the character. Keep the pitch in one variable so it is easy to change 🔎
- [ ] 🛠 Camera test (D-018): compare 30°, 35° and 45° (55° as a reference) with the same placeholder, and with the cleaned witch once imported. Check that you can read the hero's silhouette and facing and see ground circles clearly (a circle looks squashed to about sin(pitch) of its width: half at 30°, 0.7 at 45°). Pick one and record it in `docs/decisions.md`
- [ ] 🛠 Create `IA_Move` (Axis2D) and `IMC_Default` with WASD (use modifiers for W/S/A/D directions) 🔎
- [ ] 🛠 In `BP_PlayerCharacter`: add the mapping context on BeginPlay, and on `IA_Move` call **Add Movement Input** (world X/Y)
- [ ] 🛠 Character faces movement direction (Orient Rotation to Movement) 
- [ ] 🛠 Tune **Max Walk Speed** to match the hero speed from Phaser (start with 1 Phaser px/s = 1 Unreal cm/s; see map doc)
- [ ] 🛠 Optional: dash (`IA_Dash`) like the Phaser Player

**Done when:** pressing Play drops you into `L_Arena` with a tilted camera (30–45°, value recorded in D-018) and a character that moves smoothly with WASD.

---

## Phase 3 — Grey-box boss fight (one hero, one minion, one boss shell)

*Why: the whole game is this loop — a hero against a boss and a few minions — repeated with more heroes and bosses. Build it ugly first.*

- [ ] 🧠 Control scheme (D-019): manual aim + ability buttons, or auto-attack? Build the auto-fire below first (it is the simplest), then try a manual version and compare. Record the choice
- [ ] 🛠 `BP_HealthComponent` (Actor Component): current/max HP, `ApplyDamage`, event dispatcher `OnDeath`. Reusable by player, minions and bosses
- [ ] 🛠 `BP_EnemyBase` (Character): the minion. Moves toward the player (Add Movement Input toward player each Tick, or AI MoveTo with a Nav Mesh Bounds Volume) 🔎
- [ ] 🛠 Contact damage: enemy overlaps player → player takes damage, with a per-enemy cooldown
- [ ] 🛠 `BP_Projectile` (Actor + Projectile Movement Component): overlaps enemy → applies damage → destroys itself
- [ ] 🛠 Auto-fire weapon on the player: a **Set Timer by Event** that finds the nearest enemy and spawns a projectile toward it
- [ ] 🛠 `BP_BossBase` (child of `BP_EnemyBase`): big HP, two phases switched by a state variable at 50% HP. Test it with Zul'Karn's slam: a **telegraph** (a ground circle appears, then damages after about 1 second) repeated every 3 seconds. A cube or capsule is enough for the body
- [ ] 🛠 Minion pack: at the start of the fight spawn a small pack (about 5–15) around the arena edge and top it up when it is thinned (the Phaser `random_edge`/`circle` idea). A plain spawn function is enough; `BP_EncounterDirector` comes in Phase 4
- [ ] 🛠 Fight result: boss dies → victory widget with a placeholder reward text. How heroes grow (XP and level-ups during a fight, or rewards between fights) is still open (D-017), so don't build XP gems yet
- [ ] 🛠 `WBP_HUD`: hero HP bar, boss HP bar, fight timer (UMG; Create Widget + Add to Viewport from the PlayerController or GameMode)
- [ ] 🛠 Game over: on player death show a widget with Restart (Open Level)
- [ ] 🛠 Keep the number of living enemies small (minions plus the boss). The cap of ~50 (D-013) is a safety ceiling, not a target

**Done when:** you can play a 2–3 minute fight: a small pack of minions and a boss shell with one telegraphed attack; you can kill them and see the victory screen, or die and restart.

---

## Phase 4 — Make it data-driven (reuse the Phaser DB files)

*Why: this is where your data-engineering strength pays off. Content lives in tables, logic stays generic.*

- [ ] 🛠 Write a small export script (Node/Python) that converts `MonsterDB.js`, `CharacterDB.js`, `WeaponDB.js` and `RewardDB.js` into CSV/JSON Data Table imports (Claude can write this). `TimelineDB.js` is read for ideas only: the encounter table is new (see below)
- [ ] 🛠 Define row structs in Unreal (`FMonsterRow`, `FCharacterRow`, `FWeaponLevelRow`, `FEncounterRow`) and enum `EWavePattern`
- [ ] 🛠 Import to `DT_Monsters`, `DT_Characters`, `DT_WeaponLevels` (one row per weapon level) and write `DT_Encounters`: one row per fight (boss, minion type, pack size, spawn pattern, boss HP tier)
- [ ] 🛠 `BP_EnemyBase` reads its stats from `DT_Monsters` by row name (HP, damage, speed)
- [ ] 🛠 `BP_EncounterDirector`: starts a fight from a `DT_Encounters` row, spawns the boss and the minion packs with the patterns `random_edge`, `circle`, `wall_horizontal`, `wall_vertical`, `pincer`, `cluster`, and tops the pack up when it is thinned
- [ ] 🛠 Difficulty as table columns (boss HP tier, pack size) instead of the Phaser "+40% per minute" formula, because fights no longer run against a 20-minute clock. Revisit once the first boss plays well
- [ ] 🛠 Weapon levels (relic levels) read from `DT_WeaponLevels` (damage, cooldown, speed, extras)
- [ ] 🛠 Reward screen after a fight offering rewards from a reward table (port `RewardDB.js`). Design after the "how heroes grow" question in D-017 is answered
- [ ] 🛠 Localization: English only for now; names/lore stay in the table, localization comes in Phase 7

**Done when:** changing a number in a table (not a graph) changes gameplay, and a row in `DT_Encounters` is enough to start a different fight.

---

## Phase 5 — Art integration

*Why: now the game works, so replacing placeholders is safe and measurable.* See `docs/asset-pipeline.md` and `docs/blender-guide.md`.

- [x] 🛠 Inspect first Meshy model in Blender: scale, orientation, origin at feet, polygon count (done for the witch, see session log 2026-10-13)
- [ ] 🛠 Import to Unreal (`Content/VSL/Art/Characters/<name>/`); fix scale/orientation; set up material (Base Color, Normal, ORM/roughness-metal) 🔎
- [ ] 🛠 Swap one hero's placeholder for the real model; check readability from the game camera (30–45°)
- [ ] 🛠 Choose the animation path: Meshy rig/animation vs Mixamo + **IK Retargeter** 🔎
- [ ] 🛠 `ABP_Hero`: idle ↔ run driven by speed
- [ ] 🛠 Swap one enemy; then batch the rest
- [ ] 🛠 Environment: floor, props from Fab / Kenney / Quaternius; basic lighting and post-process so heroes, bosses and ground telegraphs stay readable
- [ ] 🛠 Log every asset and license in `docs/asset-log.md`

**Done when:** one hero and two enemy types use real models with animations, and the game still runs at a steady frame rate.

---

## Phase 6 — Scale the content

- [ ] 🛠 Group the 24 weapons (relics) by behavior (projectile, bounce, pierce, orbit, aura/area, cone, trail) and build reusable base classes; each weapon is then mostly table data
- [ ] 🛠 Port the other five heroes, one at a time. Each hero's four relics are what makes the hero feel different
- [ ] 🛠 Port the minion types (the 9 regular monsters are the pool to pick from)
- [ ] 🛠 Port bosses one by one: Zul'Karn first, then Echo of the Vessel, then the five Eclipse Lords. Start with those that read well from a low camera (Valeria, Obsidian Falcon); Grand Haruspex, Carmilla and Elara rely on floor patterns and need strong ground telegraphs
- [ ] 🛠 Items and rewards between fights
- [ ] 🛠 Performance pass at the heaviest fight (boss + largest pack + effects): `stat unit`, `stat fps`, then optimize (simpler movement, fewer ticks, pooling) 🔎

**Done when:** all six heroes can fight all bosses at a stable frame rate.

---

## Phase 7 — Polish and ship

- [ ] 🛠 VFX with Niagara for weapons and hits
- [ ] 🛠 Audio (reuse the Phaser sounds if licensing allows)
- [ ] 🛠 Main menu, hero select, settings, pause
- [ ] 🛠 Save Game for local meta-progression (gold, unlocks) — replaces the Supabase backend for now
- [ ] 🛠 Localization via String Tables (English, French, Simplified Chinese)
- [ ] 🛠 Package a Windows build (Platforms → Windows → Package Project) and test the packaged game, not just the editor 🔎
- [ ] 🛠 README with screenshots/GIF for the portfolio

**Done when:** someone else can download the build, play, and you can show it in an interview.

---

## Phase 8 — Optional: C++

- [ ] 🧠 Learn the Unreal C++ basics (UCLASS, UPROPERTY, UFUNCTION, Actors, Components)
- [ ] 🛠 Install Visual Studio with the "Game development with C++" workload
- [ ] 🛠 Add a C++ class (Tools → New C++ Class); build; open in Visual Studio
- [ ] 🛠 Port one hot-path system (e.g. damage/health or the encounter director) to C++ and expose it to Blueprints
- [ ] 🧠 Decide whether to reconnect online features (accounts, stats) via an HTTP backend

---

## Backlog / ideas (not scheduled)

- Replace the Phaser sprites with 2.5D flipbook billboards as an alternative art style
- Gamepad support
- Steam-style achievements from the bestiary
- A survivor-style swarm mode as an extra, after the boss game works (rejected as the main direction in D-017; Mass Entity / Niagara-driven swarms would be the tools to learn)
