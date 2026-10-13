# Branded Descent in Unreal Engine 5.8 — Project Guide

This file orients any Claude session (chat, Project, or Claude Code) working on this project.
**Read it first, then `docs/roadmap.md` for what to do next.** It is also the human's quick reference.

## What this project is

A 3D remake, in **Unreal Engine 5.8.3**, of an existing browser game ("Branded Descent", a Vampire Survivors-like built
with Phaser 4 + React + FastAPI + Supabase). Since 2026-10-13 (decision D-017) it is a **boss-focused action game**
with a tilted camera (about 30–45°): six distinct heroes with four relics (weapons) each, fighting hand-designed bosses
with a small pack of minions. It is no longer a screen-filling horde survivor. The project, repo and folder names still
say `VampireSurvivorLike`. The goals are:

1. **Learn Unreal Engine properly** (industry standard) by building something real that I already understand as a game design.
2. Build a portfolio project and a reusable knowledge base.
3. Reuse the existing game's *design* (heroes, relics, monsters, bosses, lore, balance numbers) and, where it works, its art.

The human is a **beginner in game engines** but strong in Python, SQL, JavaScript, Java, PHP, HTML, data engineering
and Power BI, and has used node-based low-code tools (Power Automate, Azure Logic Apps). Explain Unreal concepts by
analogy to those where it helps. The human is a game designer at heart: they know what feels good in a game.

## Paths and repos (Windows, human's machine)

| Thing | Location |
|---|---|
| Unreal Engine 5.8.3 install | `D:\Program Files\Epic Games\UE_5.8` |
| Unreal project (local, = the git repo root) | `D:\UE_Projects\VampireSurvivorLike` (`VampireSurvivorLike.uproject`, Blueprint, Top Down template) |
| Documentation mirror (local) | `C:\Users\BasileH\Documents\Documents\Portfolio Projects\UE_docs` |
| Source art / generated models | `D:\UE_Projects\models` (FBX from Meshy; see `docs/asset-pipeline.md`) |
| Unreal repo (GitHub, empty at start) | https://github.com/MaiSlan/UnrealEngine_Game_VS |
| Original Phaser game (reference) | https://github.com/MaiSlan/capstone-game-vs |
| Live Phaser game | https://capstone-game-vs.vercel.app |

Claude cannot see the human's screen or run the editor. Claude works from what the human describes, screenshots,
copied Blueprint text (see `docs/unreal-editor-guide.md`), and the repos.

## How Claude should work on this project

- **Teacher first.** The human is learning. Explain *why*, then give steps. One small step at a time; wait for the result before piling on.
- **Name the UI exactly** (panel names, menu paths, button labels) and say what the human should see afterwards. If unsure a menu moved in 5.8, say so and ask for a screenshot.
- **Blueprint first, C++ later** (decision D-001). Don't introduce C++ until the roadmap says so.
- **Data-driven like the Phaser game.** Balance numbers live in Data Tables, not hard-coded in graphs (decision D-008).
- **Greybox first, art later.** Never let missing art block a gameplay step.
- **Use the reference.** The Phaser repo is the design spec. See `docs/phaser-to-unreal-map.md` before designing any system. For names and lore, `Characters_and_Lore.docx` (kept outside the repo) is the source of truth; the older `Monsters.docx` has some outdated names.
- **Be honest about versions.** Unreal changes between releases. Mark anything unverified for 5.8 with "(verify in 5.8)".
- **Debugging Blueprints:** ask for a screenshot or for the nodes copied as text (select nodes, Ctrl+C, paste into chat).
- **Keep this knowledge base alive:** after each working session, update `docs/roadmap.md` checkboxes, append to
  `docs/session-log.md`, record choices in `docs/decisions.md`, and log every generated/downloaded asset in `docs/asset-log.md`.
- Never put secrets (API keys, Supabase service-role keys) in the repo.

## Conventions

### Naming (based on the widely used Allar UE style guide)

`<Prefix>_<Name>_<Variant>` in PascalCase, no spaces.

| Asset | Prefix | Example |
|---|---|---|
| Blueprint | `BP_` | `BP_PlayerCharacter` |
| Blueprint Interface | `BPI_` | `BPI_Damageable` |
| Widget (UMG) | `WBP_` | `WBP_HUD` |
| Data Table | `DT_` | `DT_Monsters` |
| Struct / Enum | `F` / `E` | `FMonsterRow`, `EWavePattern` |
| Input Action / Mapping Context | `IA_` / `IMC_` | `IA_Move`, `IMC_Default` |
| Static / Skeletal Mesh | `SM_` / `SK_` | `SM_Floor`, `SK_Viking` |
| Material / Material Instance | `M_` / `MI_` | `M_Character`, `MI_Viking` |
| Texture | `T_` + suffix | `T_Viking_BC`, `T_Viking_N` |
| Animation Blueprint / Montage | `ABP_` / `AM_` | `ABP_Hero` |
| Niagara System | `NS_` | `NS_AxeTrail` |
| Level (Map) | `L_` | `L_Arena`, `L_Sandbox` |

### Content folder layout

Everything we make goes under one root folder so Marketplace/Fab imports never mix with our work:

```
Content/VSL/
  Core/        GameMode, GameState, PlayerController, shared interfaces
  Characters/  heroes (BP + meshes + anims per hero in subfolders)
  Enemies/     monsters and bosses
  Weapons/     weapon actors / components, projectiles
  Items/       XP gems, pickups
  UI/          widgets
  Data/        Data Tables, structs, enums
  Input/       IA_* and IMC_*
  Maps/        L_* levels
  Art/         Materials, Meshes, Textures, Animations, VFX, Audio (sub-foldered)
```

### Git

- Commit small and often, with a message saying *what* and *why*.
- Binary assets (`.uasset`, `.umap`) go through **Git LFS** (see `setup/unreal.gitattributes`).
- Never commit `Binaries/`, `Intermediate/`, `Saved/`, `DerivedDataCache/`, `.vs/` (see `setup/unreal.gitignore`).
- Close the editor before committing, or at least Save All (Ctrl+Shift+S).

## Where things are

| File | Purpose |
|---|---|
| `docs/roadmap.md` | **The to-do list.** Ordered phases with checkboxes. Update as we go. |
| `docs/unreal-editor-guide.md` | Editor panels, navigation, shortcuts, Blueprint basics. |
| `docs/blender-guide.md` | What Blender is for in this project and a first-hour checklist. |
| `docs/asset-pipeline.md` | Meshy → Blender → Unreal workflow, folders, import settings. |
| `docs/phaser-to-unreal-map.md` | How each Phaser system maps to Unreal. |
| `docs/glossary.md` | Unreal vocabulary for someone coming from data/web development. |
| `docs/decisions.md` | Decision log (what we chose and why). |
| `docs/asset-log.md` | Every generated/downloaded asset, tool, plan and license. |
| `docs/session-log.md` | Running journal of sessions, problems and solutions. |
| `setup/` | `.gitignore` / `.gitattributes` templates for Unreal. |

## Open issues

1. **Art licensing.** Models made on Meshy's free plan are CC BY 4.0 (credit required). Track in `docs/asset-log.md`.
2. **Template content.** The Top Down template (click-to-move, mannequins, LevelPrototyping) is in the project as a working reference; our game uses WASD and our own character. Prune unused template assets later (D-015).
3. **Git LFS quota.** The first push stored about 136 MB in LFS (244 `.uasset` files). GitHub's free plan has a limited LFS storage/bandwidth quota; keep an eye on it and avoid committing large source art (D-005).
4. **Camera and controls.** Camera pitch/yaw (D-018) and the control scheme, manual aim or auto-attack (D-019), are still open. They are settled by playing the grey-box fight (roadmap Phases 2 and 3).
5. **How heroes grow.** XP and level-ups during a fight, or rewards between fights, is not decided yet (see D-017, "Still open").

*Resolved:* project name/folder (D-007) and template choice (D-014).

## Documentation upkeep

The repo copy is canonical. When a session produces something worth keeping (a technical solution, a tricky setup, a design decision, a lesson learned), Claude **proposes** adding it to the docs and the human decides. Keep the mirror at `UE_docs` and the Project copy in sync with the repo.

## Current status

See the **Current focus** block at the top of `docs/roadmap.md`.
