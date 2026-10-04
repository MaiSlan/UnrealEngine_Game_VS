# Blender Guide (for this project)

## What Blender is (and isn't)

Blender is a free, open-source **3D creation suite**: modeling, sculpting, UV unwrapping, texturing, rigging, animation, rendering, even video editing and a node-based procedural system (Geometry Nodes). Rendering images is only one small part of it.

**In this project Blender is a workshop for fixing and preparing models**, not something we must use for every asset. Many Meshy FBX files can be imported straight into Unreal. We open Blender when we need to:

- **Look at** a model and its textures properly, and check its quality
- **Fix scale, orientation and origin** so it behaves in Unreal
- **Reduce the polygon count** (swarms of 250 enemies need light meshes)
- **Clean up** AI-generation artifacts (stray geometry, holes, a ground plane under the character, flipped faces)
- **Rig and animate** if auto-rigging fails (later)
- **Export** FBX/glTF back out

Blender also has a **Python API**; Claude can write scripts that batch-process models (rename, decimate, export).

## The interface in plain words

Default startup layout ("General" workspace):

| Area | Where | Use |
|---|---|---|
| **3D Viewport** | Center | Where you see and edit the model |
| **Outliner** | Top right | List of objects in the scene (like a file tree) |
| **Properties editor** | Bottom right | Tabs for object, modifiers, materials, etc. |
| **Timeline** | Bottom | Animation playback (ignore for now) |
| **Toolbar** | Left of viewport | Tools (move, rotate, scale…) |
| **Workspace tabs** | Very top | Layout, Modeling, Sculpting, UV Editing, Texture Paint, **Shading**, Animation… |

Collapse/expand the side panel inside the viewport with `N` (shows exact location/rotation/scale and **Dimensions**).

## Navigation (the part that makes people feel lost)

| Action | How |
|---|---|
| Orbit | Middle mouse drag |
| Pan | `Shift` + middle mouse drag |
| Zoom | Scroll wheel |
| **Frame selected** | Numpad `.` (or View → Frame Selected) |
| **Frame everything** | `Home` |
| Front / Side / Top views | Numpad `1` / `3` / `7` (or View → Viewpoint). Without a numpad: Edit → Preferences → Input → Emulate Numpad |
| Switch perspective/orthographic | Numpad `5` |
| Select | Left click (default) |
| Select all / none | `A` / `Alt+A` |
| Move / Rotate / Scale | `G` / `R` / `S` (then optionally `X`, `Y` or `Z` to lock an axis, or type a number) |
| Undo | `Ctrl+Z` |

Tip: hover the mouse over the area you want to act in. Blender sends keys to the area under the cursor.

## First-hour checklist: inspect a Meshy model

1. **File → Import → FBX** (`.fbx`), pick the file from `D:\UE_Projects\models`. Press `Home` to see it.
2. **See the textures:** top-right of the viewport use the shading buttons (four little spheres) and pick **Material Preview**. If it's grey, switch to the **Shading** workspace tab and check the image texture nodes point at the right files (Blender → File → External Data → Find Missing Files if needed).
3. **Look around** it from front (`1`), side (`3`) and back (`Ctrl+1`).
4. **Check the stats:** viewport top-right **Overlays** dropdown → tick **Statistics**. Note the triangle count. For a mass-spawned enemy aim for a few thousand triangles or fewer; a hero can be higher.
5. **Check the size:** press `N` → Item tab → **Dimensions**. A 1.8 m character should be about 1.8 m (see Units below).
6. **Check the origin:** the little orange dot of the selected object. For characters it should sit **between the feet on the ground** (Z = 0).
7. **Check faces:** Overlays → **Face Orientation**. Blue = correct, red = flipped (fix with Edit Mode → select all → `Shift+N` to recalculate normals). Turn the overlay off afterwards.
8. **Check for extra junk:** a ground disc under the feet, loose pieces, inside faces. Switch to wireframe (`Shift+Z` or the shading buttons) to see.
9. Take a screenshot or note issues in `docs/asset-log.md`.

## Common fixes

| Problem | Fix |
|---|---|
| Model is tiny/huge | Select it, `S` to scale; then **Object → Apply → Scale** (`Ctrl+A` → Scale) |
| Origin not at the feet | Edit Mode, select bottom vertices, `Shift+S` → **Cursor to Selected**, Object Mode, **Object → Set Origin → Origin to 3D Cursor**. Then `Shift+S` → Cursor to World Origin and move the object so the feet sit at Z=0 |
| Too many polygons | Properties → Modifiers (wrench) → **Add Modifier → Generate → Decimate**, lower the ratio, apply when happy |
| Stray ground/junk | Edit Mode → select the unwanted vertices → `X` → Vertices |
| Duplicate/loose vertices | Edit Mode, `A`, then Mesh → Clean Up → **Merge by Distance** |
| Rotated the wrong way | Object Mode: rotate, then Apply → Rotation |
| Texture missing | See step 2 above |

Always **save the .blend** (File → Save) into `D:\UE_Projects\models\<asset>\` next to the source files so edits are reproducible.

## Units and exporting to Unreal

- Blender default: **metres**. Unreal: **centimetres**. A 1.8 m character should arrive as 180 units tall in Unreal.
- Export: **File → Export → FBX (.fbx)**. Suggested settings (verify in the Blender version you have):
  - **Selected Objects** ticked (export only what you chose)
  - **Apply Scalings: FBX Units Scale**
  - Forward / Up: leave defaults; if the model arrives rotated, note it in the asset log and adjust
  - Add Leaf Bones: off (for rigged meshes)
  - Path Mode: **Copy** with the embed button on if you want textures packaged with the FBX
- After importing into Unreal, check the size against the default player capsule/mannequin. If it's 100× too small or large, it's a units/scale setting on export or in Unreal's FBX import dialog.

## Rigging and animation (later)

- Meshy offers auto-rigging and animation clips; Mixamo (humanoids) is another free path. Rigged characters keep their skeleton on export to FBX.
- In Unreal, animations from a different skeleton are reused through the **IK Retargeter** (Phase 5 of the roadmap).

## Learning plan (stay light)

We use Blender as needed, not as a course. If you want a structured start:

- Blender's official manual (docs.blender.org/manual).
- Blender Studio / Blender Foundation beginner series and the popular "donut" beginner tutorial (Blender Guru) are good free starting points; pick one for the version you have installed.
- Ask Claude: "I see X in Blender, what is it?" with a screenshot — and "write a Blender Python script to…" for repetitive jobs.
