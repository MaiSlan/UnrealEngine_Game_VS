# Decision Log

Format: **ID — Title** · Status (Decided / Pending) · Date · Reason.

| ID | Decision | Status | Date | Why |
|---|---|---|---|---|
| D-001 | **Blueprint first, C++ later** | Decided | 2026-10-04 | Beginner in engines and in C++; Blueprints let us learn the framework quickly. C++ is Phase 8. |
| D-002 | **Unreal Engine 5.8.3** | Decided | 2026-10-04 | Latest stable (5.8 stable since June 2026); UE4 is frozen. Tutorials from 5.x are adaptable. |
| D-003 | **Greybox first, art later** | Decided | 2026-10-04 | Art was the bottleneck in the Phaser project; never block gameplay on it. |
| D-004 | **3D / 2.5D top-down view** | Decided — revised by D-017, D-018 | 2026-10-04 | Originally chosen because a survivor-like works well top-down. Revised 2026-10-13: the game is now boss-focused (D-017), so the camera is tilted about 30–45° to keep heroes and bosses recognizable. Exact pitch/yaw: D-018. |
| D-005 | **Source art lives outside the Unreal project** (`D:\UE_Projects\models`) | Decided | 2026-10-04 | Keeps the repo small; only finished FBX/textures are imported. Back it up separately. |
| D-006 | **Meshy for AI model tests; Free plan for now** | Decided | 2026-10-04 | Free plan is CC BY 4.0 (credit required). Re-check before publishing; upgrade if needed. Free plan limits downloads to Meshy 5/6 Lite-class models. |
| D-007 | **Project name must be a valid identifier** (`VampireSurvivorLike`, no spaces/dots) | Decided — done | 2026-10-04 | Project recreated as `VampireSurvivorLike` at `D:\UE_Projects\VampireSurvivorLike`; `.uproject` is `VampireSurvivorLike.uproject`. Safe to add C++ later. |
| D-008 | **Data-driven design with Data Tables** | Decided | 2026-10-04 | Same philosophy as the Phaser game (`*DB.js`); fits the user's data-engineering strengths. |
| D-009 | **Knowledge base lives in the Unreal repo** (`CLAUDE.md` + `docs/`); a copy sits in the Claude Project "Game Engines" | Decided | 2026-10-04 | Versioned with the project; any Claude session can read it. If the copies differ, the repo wins. |
| D-010 | **Weapons as table-driven behavior types, one row per level** | Decided | 2026-10-04 | 24 weapons are too many to hand-build; group by behavior. |
| D-011 | **Backend/auth/shop (FastAPI + Supabase) not ported initially** | Decided | 2026-10-04 | Not needed to learn Unreal or play the loop; local Save Game replaces it for meta-progress. |
| D-012 | Unit mapping: 1 Phaser px = 1 Unreal cm with a `WorldScale` multiplier | Decided | 2026-10-04 | Simple starting point; tune by eye. |
| D-013 | Starting enemy cap ~50 (Phaser uses 250) | Decided — revised by D-017 | 2026-10-04 | 3D swarms are heavier; raise after profiling. Revised 2026-10-13: fights now use a small minion pack (about 5–15) plus a boss, so the 250-enemy target is dropped. ~50 stays as a safety ceiling. |
| D-014 | **Project template: Top Down (Blueprint)** | Decided | 2026-10-04 | Verified in the repo (`Content/TopDown`, `BP_TopDownCharacter`, `Lvl_TopDown`, mannequins, `LevelPrototyping`). It uses click-to-move; our game will use WASD, so we build our own character and keep the template only as reference. |
| D-015 | **Keep the template content for now; prune later** | Decided | 2026-10-04 | It is a working example to read and borrow from. `Content/Characters/Mannequins` is the bulk of the 136 MB in Git LFS; remove unused template assets in Phase 5/7 once our own versions exist (also saves LFS quota). |
| D-016 | **Knowledge-base copies:** repo (`CLAUDE.md` + `docs/`) is canonical; mirrors at `C:\Users\BasileH\Documents\Documents\Portfolio Projects\UE_docs` and in the Claude Project "Game Engines" | Decided | 2026-10-04 | Claude proposes doc updates when a topic is special or technical; the user decides together with Claude what gets saved. |
| D-017 | **Game direction: boss-focused action game ("plan A"), not a horde survivor** | Decided | 2026-10-13 | The user values the bosses and the variety of heroes most. A 30–45° camera shows faces and silhouettes, which a top-down view hides; swarms are heavy in 3D and hard to read from a low camera; the boss mechanics are already written in the Phaser bestiary. Details below. |
| D-018 | **Camera pitch and yaw** | Pending | 2026-10-13 | Target range 30–45°. Test 30°, 35° and 45° (55° only as a comparison) on the grey-box arena in Phase 2. Why it matters: from 60° to 90° the witch's hat hides her whole body; at 45° her chin and chest show; at 30° part of her face shows. |
| D-019 | **Control scheme: manual aim + ability buttons, or auto-attack** | Pending | 2026-10-13 | The Phaser relics mix auto-targeting with firing along the facing/aim direction. Boss fights with ground telegraphs may feel better with manual control. Decide in Phase 3 by playing both on the grey box. |

## D-017 in detail — what "plan A" means

**Decided**

- The game is a boss-focused action game: hero versus boss, with a small pack of minions (about 5–15) as support. It is no longer a screen-filling horde.
- Hero variety stays central: 6 heroes, each with a kit of 4 relics (the lore name for weapons). The 24 Phaser weapons are the starting content.
- Bosses are the main content: Zul'Karn (mid boss), Echo of the Vessel (sub-boss) and the five Eclipse Lords (Obsidian Falcon, Carmilla, Grand Haruspex, Elara, Valeria).
- The camera is tilted, roughly 30–45° (exact value: D-018).
- Names and lore come from `Characters_and_Lore.docx` (kept outside the repo). `Monsters.docx` uses older names, for example "Karnok" for Zul'Karn.

**Expected consequences** (analysis, not decisions)

- Bosses that suit a low camera: Zul'Karn, Valeria, Echo of the Vessel, Obsidian Falcon.
- Bosses built around floor patterns (Grand Haruspex, Carmilla, Elara) need very clear ground telegraphs and may need a pitch near 45° or more.
- A circle on the ground looks squashed to about sin(pitch) of its width: half as tall as wide at 30°, 0.7 at 45°.

**Not chosen**

- *B — action roguelite (room-by-room runs):* puts the work into rooms and level variety instead of bosses and heroes.
- *C — survivor-lite (fewer enemies, same loop):* keeps the horde structure, which a low camera and 3D cost make the weakest part. It can come back as an extra mode later (roadmap backlog).

**Still open**

- Camera pitch/yaw (D-018) and control scheme (D-019).
- How heroes grow: XP and level-ups during a fight, or rewards between fights.
- Run structure: the order of the bosses and whether a run is a fixed sequence.
