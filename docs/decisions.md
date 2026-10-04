# Decision Log

Format: **ID — Title** · Status (Decided / Pending) · Date · Reason.

| ID | Decision | Status | Date | Why |
|---|---|---|---|---|
| D-001 | **Blueprint first, C++ later** | Decided | 2026-10-04 | Beginner in engines and in C++; Blueprints let us learn the framework quickly. C++ is Phase 8. |
| D-002 | **Unreal Engine 5.8.3** | Decided | 2026-10-04 | Latest stable (5.8 stable since June 2026); UE4 is frozen. Tutorials from 5.x are adaptable. |
| D-003 | **Greybox first, art later** | Decided | 2026-10-04 | Art was the bottleneck in the Phaser project; never block gameplay on it. |
| D-004 | **3D / 2.5D top-down view** | Decided | 2026-10-04 | A survivor-like works well top-down. Exact camera pitch/yaw to be set in Phase 2 and recorded here. |
| D-005 | **Source art lives outside the Unreal project** (`D:\UE_Projects\models`) | Decided | 2026-10-04 | Keeps the repo small; only finished FBX/textures are imported. Back it up separately. |
| D-006 | **Meshy for AI model tests; Free plan for now** | Decided | 2026-10-04 | Free plan is CC BY 4.0 (credit required). Re-check before publishing; upgrade if needed. Free plan limits downloads to Meshy 5/6 Lite-class models. |
| D-007 | **Project name must be a valid identifier** (`VampireSurvivorLike`, no spaces/dots) | Pending | 2026-10-04 | Current folder is `VampireSurvivorLike 5.8`. Check the `.uproject` filename; recreate cleanly if needed (roadmap Phase 0). |
| D-008 | **Data-driven design with Data Tables** | Decided | 2026-10-04 | Same philosophy as the Phaser game (`*DB.js`); fits the user's data-engineering strengths. |
| D-009 | **Knowledge base lives in the Unreal repo** (`CLAUDE.md` + `docs/`); a copy sits in the Claude Project "Game Engines" | Decided | 2026-10-04 | Versioned with the project; any Claude session can read it. If the copies differ, the repo wins. |
| D-010 | **Weapons as table-driven behavior types, one row per level** | Decided | 2026-10-04 | 24 weapons are too many to hand-build; group by behavior. |
| D-011 | **Backend/auth/shop (FastAPI + Supabase) not ported initially** | Decided | 2026-10-04 | Not needed to learn Unreal or play the loop; local Save Game replaces it for meta-progress. |
| D-012 | Unit mapping: 1 Phaser px = 1 Unreal cm with a `WorldScale` multiplier | Decided | 2026-10-04 | Simple starting point; tune by eye. |
| D-013 | Starting enemy cap ~50 (Phaser uses 250) | Decided | 2026-10-04 | 3D swarms are heavier; raise after profiling. |
| D-014 | Project template used at creation | Pending | — | To be filled in (Top Down or Blank). |
