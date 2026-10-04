# Roadmap — Vampire Survivor-Like in Unreal Engine 5.8

No dates: this is an ordered to-do list. Work top to bottom; don't skip phases. Tick boxes (`[x]`) as we finish things.
Each phase ends with a **Done when** line so we know it is really finished.

> **Current focus:** Phase 0 — Housekeeping
> **Last updated:** 2026-10-04 (initial version)

Legend: 🧠 = concept to understand, 🛠 = hands-on step, 🔎 = verify in 5.8 / needs a screenshot.

---

## Phase 0 — Housekeeping (do once, ~everything else depends on it)

*Why: a clean project name and version control now saves hours later.*

- [ ] 🛠 Find the `.uproject` file in the project folder. Does its filename contain a space or a dot? (Open Issue #1 in `CLAUDE.md`)
- [ ] 🛠 If yes: create a fresh project named exactly `VampireSurvivorLike` (Games → Top Down or Blank, **Blueprint**, Desktop, Maximum quality, **Starter Content off**, **Ray Tracing off**) at `D:\UE_Projects\VampireSurvivorLike`
- [ ] 🛠 Record the chosen template in `docs/decisions.md`
- [ ] 🛠 Copy `CLAUDE.md`, `docs/` and `setup/` from the knowledge base into the project root
- [ ] 🛠 In the project root: copy `setup/unreal.gitignore` → `.gitignore` and `setup/unreal.gitattributes` → `.gitattributes`
- [ ] 🛠 `git init`, `git lfs install`, add remote `https://github.com/MaiSlan/UnrealEngine_Game_VS`, first commit, push
- [ ] 🛠 Editor Preferences: turn off **Use Less CPU when in Background** (search "CPU" in Edit → Editor Preferences) 🔎
- [ ] 🛠 Create the `Content/VSL/...` folder layout from `CLAUDE.md` (right-click in Content Browser → New Folder)
- [ ] 🛠 Confirm Blender opens and Meshy account works (done: models already generated)

**Done when:** the project opens, the repo on GitHub contains it (no `Intermediate/` or `Saved/`), and the knowledge base is inside it.

---

## Phase 1 — Editor orientation (no code yet)

*Why: you can't build in a tool you can't navigate. Goal: stop feeling lost.* Read `docs/unreal-editor-guide.md` first.

- [ ] 🧠 Identify the five main areas: Viewport, Content Browser, Outliner, Details, Toolbar
- [ ] 🛠 Create a new empty level `L_Sandbox` (File → New Level → Empty Level), save it into `Content/VSL/Maps`
- [ ] 🛠 Fly around the viewport: hold right mouse + WASD/QE; scroll to change speed; `F` to focus a selected object
- [ ] 🛠 Place a **Cube** (Place Actors panel → Shapes) and move/rotate/scale it with `W` / `E` / `R`
- [ ] 🛠 Make a floor: scale a cube flat and wide (about 50 x 50 x 0.2). This is the arena
- [ ] 🛠 Add a **Directional Light** and a **Player Start**
- [ ] 🛠 Press Play (`Alt+P`), look around, press `Esc` to stop. Notice what changes between editing and playing
- [ ] 🛠 Create your first Blueprint: right-click in Content Browser → Blueprint Class → Actor → `BP_Test`, drag it into the level, add a mesh component, compile, save
- [ ] 🛠 Import one Meshy FBX into `Content/VSL/Art/...` and drop it in the sandbox just to see it (details in `docs/asset-pipeline.md`)

**Done when:** you can open the project, find any asset, place and move objects, press Play and stop, and save without hunting for menus.

---

## Phase 2 — Core framework: a player you can move

*Why: this teaches the Unreal gameplay framework (GameMode, Pawn/Character, Controller, Input) that everything else sits on.*

- [ ] 🧠 Understand: GameMode, Character, PlayerController, Possess, Input Mapping Context (see `docs/glossary.md`)
- [ ] 🛠 Create level `L_Arena` (copy the sandbox floor and lights)
- [ ] 🛠 Create `BP_VSGameMode`; in World Settings set it as GameMode Override for `L_Arena`
- [ ] 🛠 Create `BP_PlayerCharacter` (parent: Character) with a placeholder mesh (cube/capsule) 
- [ ] 🛠 Add **Spring Arm + Camera** looking down at about 60–75° pitch, fixed (no player camera rotation); set `Use Pawn Control Rotation` off 🔎
- [ ] 🛠 Create `IA_Move` (Axis2D) and `IMC_Default` with WASD (use modifiers for W/S/A/D directions) 🔎
- [ ] 🛠 In `BP_PlayerCharacter`: add the mapping context on BeginPlay, and on `IA_Move` call **Add Movement Input** (world X/Y)
- [ ] 🛠 Character faces movement direction (Orient Rotation to Movement) 
- [ ] 🛠 Tune **Max Walk Speed** to match the hero speed from Phaser (start with 1 Phaser px/s = 1 Unreal cm/s; see map doc)
- [ ] 🛠 Optional: dash (`IA_Dash`) like the Phaser Player

**Done when:** pressing Play drops you into `L_Arena` with a top-down camera and a character that moves smoothly with WASD.

---

## Phase 3 — Grey-box combat loop (one hero, one enemy, one weapon)

*Why: the whole survivor-like game is this loop repeated. Build it ugly first.*

- [ ] 🛠 `BP_HealthComponent` (Actor Component): current/max HP, `ApplyDamage`, event dispatcher `OnDeath`. Reusable by player and enemies
- [ ] 🛠 `BP_EnemyBase` (Character): moves toward the player (Add Movement Input toward player each Tick, or AI MoveTo with a Nav Mesh Bounds Volume) 🔎
- [ ] 🛠 Contact damage: enemy overlaps player → player takes damage, with a per-enemy cooldown
- [ ] 🛠 `BP_Projectile` (Actor + Projectile Movement Component): overlaps enemy → applies damage → destroys itself
- [ ] 🛠 Auto-fire weapon on the player: a **Set Timer by Event** that finds the nearest enemy and spawns a projectile toward it
- [ ] 🛠 `BP_EnemySpawner`: on a timer spawns enemies on a ring around the player (the Phaser `random_edge`/`circle` idea)
- [ ] 🛠 `BP_XPGem`: dropped on enemy death, picked up by player (start with overlap, add magnet radius later)
- [ ] 🛠 XP counter and **level-up event** (for now: just print "Level up!")
- [ ] 🛠 `WBP_HUD`: HP bar, XP bar, survival timer (UMG; Create Widget + Add to Viewport from the PlayerController or GameMode)
- [ ] 🛠 Game over: on player death show a widget with Restart (Open Level)
- [ ] 🛠 Cap alive enemies (start at ~50, raise later; Phaser uses `MAX_ACTIVE_ENEMIES = 250`)

**Done when:** you can play a 2–3 minute loop: enemies swarm in, you auto-kill them, collect XP, level up (even if only a message) and eventually die and restart.

---

## Phase 4 — Make it data-driven (reuse the Phaser DB files)

*Why: this is where your data-engineering strength pays off. Content lives in tables, logic stays generic.*

- [ ] 🛠 Write a small export script (Node/Python) that converts `MonsterDB.js`, `CharacterDB.js`, `WeaponDB.js`, `TimelineDB.js` into CSV/JSON Data Table imports (Claude can write this)
- [ ] 🛠 Define row structs in Unreal (`FMonsterRow`, `FCharacterRow`, `FWeaponLevelRow`, `FTimelineRow`) and enum `EWavePattern`
- [ ] 🛠 Import to `DT_Monsters`, `DT_Characters`, `DT_WeaponLevels` (one row per weapon level), `DT_Timeline`
- [ ] 🛠 `BP_EnemyBase` reads its stats from `DT_Monsters` by row name (HP, damage, speed, XP)
- [ ] 🛠 `BP_WaveDirector`: reads `DT_Timeline` against the run clock and spawns with the patterns: `random_edge`, `circle`, `wall_horizontal`, `wall_vertical`, `pincer`, `cluster`, `boss`
- [ ] 🛠 Difficulty scaling formula (+40% per minute for trash mobs) and boss HP tiers, as variables / table columns
- [ ] 🛠 Weapon levels read from `DT_WeaponLevels` (damage, cooldown, speed, extras)
- [ ] 🛠 Level-up screen offering rewards from a reward table (port `RewardDB.js`)
- [ ] 🛠 Localization: English only for now; names/lore stay in the table, localization comes in Phase 7

**Done when:** changing a number in a table (not a graph) changes gameplay, and a timed wave schedule drives the spawns for several minutes.

---

## Phase 5 — Art integration

*Why: now the game works, so replacing placeholders is safe and measurable.* See `docs/asset-pipeline.md` and `docs/blender-guide.md`.

- [ ] 🛠 Inspect first Meshy model in Blender: scale, orientation, origin at feet, polygon count
- [ ] 🛠 Import to Unreal (`Content/VSL/Art/Characters/<name>/`); fix scale/orientation; set up material (Base Color, Normal, ORM/roughness-metal) 🔎
- [ ] 🛠 Swap one hero's placeholder for the real model; check readability from the top-down camera
- [ ] 🛠 Choose the animation path: Meshy rig/animation vs Mixamo + **IK Retargeter** 🔎
- [ ] 🛠 `ABP_Hero`: idle ↔ run driven by speed
- [ ] 🛠 Swap one enemy; then batch the rest
- [ ] 🛠 Environment: floor, props from Fab / Kenney / Quaternius; basic lighting and post-process so enemies stay readable
- [ ] 🛠 Log every asset and license in `docs/asset-log.md`

**Done when:** one hero and two enemy types use real models with animations, and the game still runs at a steady frame rate.

---

## Phase 6 — Scale the content

- [ ] 🛠 Group the 24 weapons by behavior (projectile, bounce, pierce, orbit, aura/area, cone, trail) and build reusable base classes; each weapon is then mostly table data
- [ ] 🛠 Port the other five heroes
- [ ] 🛠 Port monsters (9 regular types) and bosses (mid-game filter at 10:00, Eclipse Lords at 20:00, mimic sub-boss)
- [ ] 🛠 Items and rewards; pickups and magnet
- [ ] 🛠 Performance pass at 250 enemies: `stat unit`, `stat fps`, then optimize (simpler movement, fewer ticks, instanced meshes, pooling) 🔎
- [ ] 🧠 Learn when Mass Entity / Niagara-driven swarms make sense (advanced; only if performance demands it)

**Done when:** a full 20-minute run with all heroes is playable at a stable frame rate.

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
- [ ] 🛠 Port one hot-path system (e.g. damage/health or the wave director) to C++ and expose it to Blueprints
- [ ] 🧠 Decide whether to reconnect online features (accounts, stats) via an HTTP backend

---

## Backlog / ideas (not scheduled)

- Replace the Phaser sprites with 2.5D flipbook billboards as an alternative art style
- Gamepad support
- Steam-style achievements from the bestiary
