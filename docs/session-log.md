# Session Log

Newest entry first. Keep each entry short: what we did, what we learned, what is next, problems and fixes.

## 2026-10-13 — Witch model test and game direction (plan A)

**Done**
- Tested Claude Code on one Meshy character, "Moonlit Little Witch" (the hero Yenna), with Blender 5.2.2 running without a window. Work copy and reports: `D:\UE_Projects\models\witch-backup` (`REPORT2.md`, `NOTES.md`). The original `witch-model` folder was not touched.
- Exported FBX variants: A (258,914 triangles, master only), B (60,000) and C (30,000). B and C use a re-baked normal map, which removed the faceted shading. An experimental texture with the pale blotches on the hair tails removed exists for B only so far. A second experiment (new UV layout) was worse and was not exported.
- Export settings that worked: Selected Objects, Apply Scalings = FBX Units Scale, Forward -Z, Up Y, Add Leaf Bones off, textures embedded. The mesh is turned to face +X, stands 1.80 m tall with the feet at Z = 0. Expected in Unreal: 180 cm, facing +X *(verify in 5.8)*.
- Chose the game direction: a boss-focused action game with a tilted camera (D-017). Camera pitch (D-018) and control scheme (D-019) are still open.
- Brought the repo docs and the `UE_docs` mirror up to date with the Claude Project copy (D-014 to D-016, the template tour in the editor guide, Phase 0 ticks) while adding D-017.

**Learned**
- From 60° to 90° of camera pitch the witch's hat hides her whole body. At 45° her chin and chest show; at 30° part of her face shows. This is why the camera is now tilted.
- Her arms are fused to the dress and the hat and hair to the head. An automatic rig would probably animate the legs but drag the dress and hair along (unverified). A simple bob animation is enough for small enemies; a hero with moving arms is easier to regenerate in Meshy in an A-pose.
- Claude Code's recommendation, not yet decided: use C (30k) in the game and B (60k) for close-ups.

**Problems / watch-outs**
- Claude Code's first report mistook the witch's hands for blotches on the skirt and said the arms were missing. Its second report corrected both. Check claims against the renders.

**Next**
- Narrow Claude Code task: apply the blotch-fix texture to B and C and export `_final` FBX files.
- Phase 1: import one cleaned FBX into `L_Sandbox` and check size and facing.
- Fill the witch row in `docs/asset-log.md` (Meshy 6 Lite, Free plan, CC BY 4.0, credit required).
- Phase 2: camera pitch test (D-018).

## 2026-10-04 (later) — First push verified

**Done**
- Project recreated as `D:\UE_Projects\VampireSurvivorLike` (`VampireSurvivorLike.uproject`, engine 5.8). Name issue resolved (D-007).
- First push to `MaiSlan/UnrealEngine_Game_VS` succeeded: 410 git objects, 244 LFS objects (~136 MB). Claude cloned the repo and verified: `.gitignore`, `.gitattributes`, `CLAUDE.md`, `docs/`, `Config/`, `Content/`, `.uproject`; no `Intermediate/`, `Saved/`, `Binaries/`.
- Template confirmed as **Top Down** (`Content/TopDown`: `BP_TopDownCharacter`, `BP_TopDownController`, `BP_TopDownGameMode`, `IMC_Default`, `Lvl_TopDown`; plus `Characters/Mannequins` and `LevelPrototyping`). Default map and GameMode still point at the template (`Config/DefaultEngine.ini`).
- Documentation mirror created at `C:\Users\BasileH\Documents\Documents\Portfolio Projects\UE_docs`.

**Learned**
- Empty folders are not tracked by git; `Content/VSL/...` will show up once it holds assets.
- Most of the LFS size is template mannequin animations and meshes.

**Next**
- Phase 1: Tour the Top Down template, then build `L_Sandbox` (roadmap).

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
