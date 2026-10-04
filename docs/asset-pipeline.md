# Asset Pipeline: image → Meshy → Blender → Unreal

## Folder rules

```
D:\UE_Projects\
  VampireSurvivorLike\            <- Unreal project (git repo)
  models\                         <- SOURCE ART (kept outside the Unreal project)
    <asset_name>\
      source_image.png            the 2D image fed to Meshy (highest resolution available)
      meshy_original\             untouched Meshy download (FBX/GLB + textures)
      blender\<asset_name>.blend  our cleaned version
      export\<asset_name>.fbx     what we import into Unreal
      NOTES.md                    settings used, problems, license
```

Why outside the project: raw downloads and `.blend` files are big and change often; the Unreal project only needs the finished FBX plus textures. Keep a backup of `models\` (cloud drive or a second drive) — it is not in git.
If we later want source art versioned, create a separate repo or use Git LFS in a `SourceArt/` folder; record the decision in `docs/decisions.md`.

## Workflow per asset

1. **Pick the source image.** Full body, front-facing, clean background, as high resolution as possible. Use the original generated image, not a downscaled pixel-art sprite.
2. **Generate in Meshy** (Image to 3D). Note: model version (e.g. Meshy 6 Lite on the free plan), prompt/settings, date, plan (Free = CC BY 4.0, credit required).
3. **Download FBX** (and GLB if offered) into `meshy_original\`. Keep texture files next to the FBX.
4. **Inspect in Blender** (`docs/blender-guide.md` first-hour checklist). Save a `.blend`.
5. **Fix:** scale, origin at feet, polygon count, junk geometry, normals.
6. **Export FBX** to `export\`.
7. **Import into Unreal** (Content Browser → Import, or drag the FBX onto the Content Browser) into `Content/VSL/Art/Characters/<AssetName>/` (see below).
8. **Check in a level**: size against the player capsule, orientation (facing +X in Unreal), shading, texture appearance under the top-down camera.
9. **Log it** in `docs/asset-log.md` (tool, plan, license, attribution, paths).

## Unreal FBX import dialog (what to look at)

Dialog names can differ slightly in 5.8 **(verify in 5.8)**.

| Option | Setting |
|---|---|
| Mesh type | **Skeletal Mesh** if rigged/animated, otherwise **Static Mesh** (props, scenery, grey-box stand-ins) |
| Import Materials / Textures | On (first import) |
| Import Normals | Import Normals |
| Create Physics Asset (skeletal) | On |
| Convert Scene Unit | On (helps with Blender metres → Unreal cm) |
| Skeleton | None for a new skeleton; pick the existing one when importing a second mesh/animation for the same character |

After import:
- Open the material. Typical PBR maps from Meshy: **Base Color**, **Normal**, **Metallic/Roughness** (sometimes packed). Plug them into the matching material inputs.
- Texture settings: Base Color uses **sRGB on**; normal maps must use **Normal Map compression** (Unreal normally detects this, check it); roughness/metallic/ORM textures use **sRGB off** 🔎.
- Make a **Material Instance** (`MI_...`) from a parent material (`M_Character`) so tweaking colors is cheap.

## Animation and rigging options (decide in Phase 5)

| Option | Notes |
|---|---|
| Meshy auto-rig + animation clips | Fast; per Meshy docs rigging and animation clips cost 0 credits at the time of writing (verify on the live page); quality varies |
| Mixamo (Adobe, free account) | Humanoids only; huge library of walk/run/attack; retarget in Unreal with the **IK Retargeter** |
| Hand-animate in Blender | Slowest; only for special moves |
| 2.5D flipbook billboards | Reuse existing sprite sheets as flat cards; different look, fewer problems |

## Gotchas specific to a swarm game

- 250 enemies × heavy skeletal meshes = trouble. Target low poly, few materials and no per-enemy dynamic shadows if needed. Verify with `stat unit`.
- Many enemies can share **one material instance** and **one mesh**.
- For the biggest swarms we may use static meshes with vertex animation or instanced meshes (advanced; Phase 6).
- Keep animated bosses at a higher quality and fewer count.

## Other asset sources (free)

- **Fab** (in the Epic launcher and inside the editor): free monthly content and a large library. Always check each item's license before shipping.
- **Kenney.nl**, **Quaternius**: CC0 low-poly packs (characters, props, environment).
- **Mixamo**: characters/animations (see terms).
- **Sketchfab**: only items with a license that allows our use.

## AI-generated assets: licensing checklist

- Meshy **Free** plan output: CC BY 4.0 — commercial use allowed with credit to Meshy. Paid plans grant ownership/private licensing. Re-check the live terms before publishing a game.
- Tripo **Free** plan: non-commercial; commercial use needs a paid plan.
- Hunyuan3D: open weights but with regional restrictions in its license; read it before using.
- Record plan + date for every asset in `docs/asset-log.md`. Models generated on a free plan keep that license even if you upgrade later.
