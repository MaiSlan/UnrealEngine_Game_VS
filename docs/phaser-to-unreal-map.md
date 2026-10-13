# Phaser → Unreal Map

The Phaser game ("Branded Descent", repo `MaiSlan/capstone-game-vs`) is the **design spec**. This file maps its systems to Unreal ones. Facts below come from reading the repo (265 files) on 2026-10-04.

## Direction change (D-017, 2026-10-13)

The Unreal game is a **boss-focused action game**, not a horde survivor. The Phaser design is still the spec for heroes, relics (weapons), monsters, bosses and lore, but three things change:

- **Fights replace the 20-minute timeline.** `TimeLineDB.js` becomes `DT_Encounters` (one row per fight: boss, minion type, pack size, spawn pattern, boss HP tier). The director is `BP_EncounterDirector`.
- **Small packs replace the horde.** A fight has a boss plus about 5–15 minions, not 250 enemies. The "+40% per minute" scaling has no clock to run against; difficulty becomes table columns.
- **Bosses and heroes are the core content.** `BP_BossBase` gets the most design time. A tilted camera (about 30–45°, D-018) means bosses need clear ground telegraphs.

Names and lore: `Characters_and_Lore.docx` is the source of truth. `Monsters.docx` uses older names (for example "Karnok" for Zul'Karn). In the lore, a "relic" is a weapon.

## What the Phaser game contains

- **6 heroes** (`CharacterDB.js`): hp, speed, starting weapon, localized name/quotes (en/fr/zh).
- **24 weapons** (`WeaponDB.js`): 4 per hero, 5 levels each, per-level arrays (damage, cooldown, speed, plus weapon-specific stats such as bounces, pierceCount, radius, knockbackForce, coneAngle).
- **Monsters** (`MonsterDB.js`): 9 regular types (`abyssal_sludge`, `night_terror`, `blighted_gore_thrall`, `hollowed_legionnaire`, `crimson_strigoi`, `ocular_sentinel`, `abyssal_behemoth`, `echo_of_the_vessel`, `zul_karn`…) plus bosses (`obsidian_falcon`, `carmilla`, `grand_haruspex`, `elara`, `valeria`…). Fields: baseHp, baseDamage, baseSpeed, xpValue, spriteKey, localized name/lore.
- **Timeline** (`TimeLineDB.js`): rows `{startTime, endTime, monsterId, spawnRateMs, countPerSpawn, pattern, hpTier?}` over a 20-minute run.
- **Spawn patterns:** `random_edge`, `circle`, `wall_horizontal`, `wall_vertical`, `pincer`, `cluster`, `boss`.
- **Difficulty:** trash-mob HP/damage/speed +40% per minute survived; bosses use an extra `BOSS_HP_TIERS` multiplier; `MAX_ACTIVE_ENEMIES = 250`.
- **Meta layer** (not gameplay): React menus, auth, BoneFire shop, bestiary, profile stats, FastAPI + Supabase.

## System mapping

| Phaser | Unreal equivalent | Notes |
|---|---|---|
| React/Vite web shell, routing | Unreal menus (UMG widgets) | Rebuild later (Phase 7) |
| Phaser Scene (`MainScene`) | **Level** (`L_Arena`) + **GameMode** (`BP_VSGameMode`) | GameMode owns the run rules and clock |
| `Player.js` + hero classes | `BP_PlayerCharacter` (Character) + hero data row | One BP, hero chosen from `DT_Characters` |
| `BaseMonster.js` + monster classes | `BP_EnemyBase` with child BPs only when behavior differs | Stats from `DT_Monsters` |
| Boss classes (`BaseEclipseLord`, …) | `BP_BossBase` child of `BP_EnemyBase` | Phases via state variable / timelines. Bosses are the core of the game (D-017) |
| `CharacterDB.js` | `DT_Characters` (struct `FCharacterRow`) | |
| `MonsterDB.js` | `DT_Monsters` (`FMonsterRow`) | `spriteKey` → soft reference to mesh/class |
| `WeaponDB.js` (arrays per level) | `DT_WeaponLevels`: **one row per weapon level**, e.g. `bouncing_axe_L1` | Easier to edit than arrays; a flat table is also what Data Tables handle best |
| `TimeLineDB.js` | `DT_Encounters` (`FEncounterRow`, `EWavePattern` enum) | One row per fight instead of one row per time slice (D-017). Read the old timeline for pack ideas only |
| `WaveManager.js` (the "Director") | `BP_EncounterDirector` actor or GameMode component | Starts a fight from a `DT_Encounters` row and tops up the minion pack; uses timers, not Tick |
| Spawn patterns | Functions in `BP_EncounterDirector` | Math on positions around the player |
| `WeaponManager.js` | `BP_WeaponManagerComponent` on the player | Owns equipped weapons and levels |
| Weapon classes (one per weapon) | Weapon base BPs by **behavior type** (projectile, bounce, pierce, orbit, aura, cone, trail); weapons are mostly table data | Avoids 24 hand-built graphs |
| `ItemManager.js`, `ItemDB.js` | Item data table + component on the player | |
| `RewardDB.js` (level-up choices) | `DT_Rewards` + `WBP_LevelUp` widget | |
| `LootManager.js` | `BP_XPGem` pickups + magnet logic | |
| `AnimationManager.js` | **Animation Blueprint** (`ABP_Hero`) | Direction-based sprite anims are replaced by skeletal animation |
| Arcade physics body | **Capsule collision** + Character Movement | |
| `window` custom events (`VS_UPDATE_HP`…) | **Event Dispatchers** / UMG bindings | |
| `i18n` (en/fr/zh) | **String Tables** + Localization Dashboard | Phase 7 |
| Supabase auth, shop, stats, backend | Not ported at first. **Save Game** locally for meta-progress | Optional HTTP backend later |

## Units and coordinates

- Phaser: 2D pixels, y points down. Unreal: centimetres, Z up, the ground is the X/Y plane (the camera is tilted, not straight down).
- Start with **1 Phaser pixel = 1 Unreal unit (cm)** and keep a single `WorldScale` multiplier variable so everything can be re-tuned. Hero speeds (150–250) then read as walking/jogging pace; arena size is tuned by eye.
- Phaser Y-down → Unreal: up on screen = +X with a camera aligned with the world; decide the camera yaw once and record it in `docs/decisions.md` (D-018).

## Timing

Phaser times are in milliseconds (`cooldown: 1200`). Unreal Timers use seconds. Convert with `/1000` when importing the tables (the export script does this).

## Things to watch

- **Enemy count.** Fights use a boss plus about 5–15 minions (D-017), so the Phaser cap of 250 is no longer a goal. Keep ~50 as a safety ceiling (D-013).
- Localized text is embedded in the JS DB files. In the export step, split it out (text tables) so gameplay tables stay clean.
- The Phaser art is 4-direction sprites ("south/east/north"). In 3D these become one model + animations; for a 2.5D look the old sprites can still be used as billboards.
