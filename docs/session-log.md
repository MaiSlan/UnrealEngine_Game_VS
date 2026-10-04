# Session Log

Newest entry first. Keep each entry short: what we did, what we learned, what is next, problems and fixes.

## 2026-10-04 — Setup and planning

**Done**
- Chose Unreal Engine 5.8.3 (installed on D:), Blueprint-first approach.
- Reviewed the Phaser repo (`capstone-game-vs`): 6 heroes, 24 weapons, 9 regular monsters + bosses, timeline-driven director, data-driven DB files.
- Generated first 3D models on Meshy's free plan (Meshy 6 Lite), FBX saved in `D:\UE_Projects\models`.
- Imported a model into Blender and looked at it and its textures; interface felt overwhelming → wrote `docs/blender-guide.md`.
- Unreal project exists and opens; the GitHub repo `UnrealEngine_Game_VS` is still empty.
- Created this knowledge base (`CLAUDE.md`, roadmap, guides, logs).

**Learned**
- Blender is a workshop (inspect, fix scale/origin/polycount, rig, export), not just a renderer.
- Meshy Free = CC BY 4.0; Tripo Free = non-commercial.

**Problems / watch-outs**
- Local project folder is named `VampireSurvivorLike 5.8` (space + dot). Check the `.uproject` name (Open Issue #1).

**Next**
- Roadmap Phase 0: fix project name if needed, `.gitignore` + LFS, first commit and push.
- Then Phase 1: editor orientation in a sandbox level.
