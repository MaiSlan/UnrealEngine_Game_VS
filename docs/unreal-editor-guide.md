# Unreal Editor Guide (UE 5.8)

A practical orientation for someone who has never used a game engine. Items marked **(verify in 5.8)** may have moved slightly between versions; tell Claude and send a screenshot if something doesn't match.

## 1. The five areas you will use all day

| Area | What it is | Web/data analogy |
|---|---|---|
| **Viewport** | The 3D view of the current Level. You place and move things here. | The rendered page / canvas |
| **Content Browser** | All your assets (meshes, Blueprints, tables, textures). Open with `Ctrl+Space` for the drawer, or dock it. | File explorer + database of assets |
| **Outliner** | Every object currently placed in the open Level. | DOM tree |
| **Details panel** | Properties of whatever is selected (location, mesh, variables…). | Inspector / properties panel |
| **Toolbar** | Play button, Save, Source control, Platforms, Settings. | Run/debug bar |

Also useful: **Place Actors** panel (to drag cubes, lights, volumes into the level — if missing, use the Modes dropdown at the top-left, or Window menu) **(verify in 5.8)**, **Output Log** (Window → Output Log: errors and `Print String` output), **World Settings** (Window → World Settings: per-level settings such as the GameMode override).

## 2. Moving around the viewport

| Action | How |
|---|---|
| Fly (like a game) | Hold **right mouse button** + `W A S D`, `Q` down, `E` up |
| Change fly speed | Scroll wheel while holding right mouse, or the speed setting at the top of the viewport |
| Orbit around selection | `Alt` + left mouse drag |
| Pan | `Alt` + middle mouse drag (or middle mouse drag) |
| Zoom | Scroll wheel |
| **Focus on selected** | `F` (the single most useful key) |
| Select | Left click |
| Move / Rotate / Scale tool | `W` / `E` / `R` |
| Snap object to the floor | `End` |
| Toggle between Lit / Unlit / Wireframe | Viewport menu "Lit" at the top |

## 3. Saving, playing, undoing

| Action | Shortcut |
|---|---|
| Save the current level/asset | `Ctrl+S` |
| **Save All** (do this before closing or committing) | `Ctrl+Shift+S` |
| Undo / Redo | `Ctrl+Z` / `Ctrl+Y` |
| Play In Editor | `Alt+P` (or the Play button) |
| Stop playing | `Esc` |
| Get your mouse back while playing | `Shift+F1` |
| Eject from the player to inspect the world while playing | `F8` |
| Open the console while playing | `~` (backtick). Try `stat fps` and `stat unit` |

**Important concept:** *Edit mode* and *Play mode* are different worlds. Changes you make to objects while playing are **lost** when you stop. Change things in edit mode.

## 4. The Content Browser

- Right-click empty space → create folders and assets (Blueprint Class, Material, Data Table, Widget Blueprint, Level…).
- **Import** button (top of Content Browser) or drag a file from Windows Explorer in.
- Never rename or move assets in Windows Explorer — do it in the Content Browser, which fixes references (and leaves "redirectors"; right-click folder → Fix Up Redirectors when needed).
- Folder layout and prefixes are in `CLAUDE.md`.

## 5. Levels and Actors

- A **Level** (a `.umap`) is a scene. An **Actor** is anything placed in a level (cube, light, enemy, camera).
- Dragging a Blueprint class from the Content Browser into the level creates an **instance** of it. The Blueprint is the class; the instance is one copy.
- **Components** are parts of an Actor (mesh, camera, collision). A Blueprint Actor is a tree of components plus logic.

## 6. Blueprint editor (visual scripting)

Open a Blueprint with a double-click. Main parts:

- **Viewport tab:** the components and how the actor looks.
- **Event Graph tab:** the node logic (the "code").
- **Components panel (top-left):** add/remove components.
- **My Blueprint panel (left):** variables, functions, event dispatchers.
- **Details panel (right):** properties of the selected variable/component/node.
- **Compile / Save** (top-left toolbar): **always Compile after changes**; errors show as red on nodes and in the Compiler Results at the bottom.

### Reading a graph

- **White execution wires** carry the order of execution (like statements). **Colored wires** carry data (like function arguments).
- **Red event nodes** start a chain: `Event BeginPlay` (once at start), `Event Tick` (every frame, use sparingly), `Event ActorBeginOverlap` (collision), input events.
- **Blue nodes** are functions. **Green** nodes are pure (no execution pin).

### Useful tricks

| Action | How |
|---|---|
| Add a node | Right-click in the graph and type its name |
| Quick nodes | Hold `B` + click = Branch (if), `S` + click = Sequence, `D` + click = Delay |
| Wire without a node | Drag from a pin into empty space → search shows compatible nodes |
| Comment box | Select nodes → `C` |
| Auto-tidy | Select nodes → `Q` aligns them |
| Delete a wire | Alt + click on the pin |
| Debug | Add a **Print String** node to see values on screen/in the Output Log; right-click a node → Add Breakpoint (`F9`) |
| Copy nodes as text | Select nodes → `Ctrl+C` → paste into chat or a text editor. Claude can read it, and you can paste it back with `Ctrl+V`. Verbose, but works. |

### Blueprint communication cheat sheet

- **Get Player Character / Get Player Pawn** — find the player from anywhere.
- **Cast To BP_X** — "is this object an X? if so give me access to its variables".
- **Blueprint Interface** — a contract (`BPI_Damageable`) several different classes can implement; avoids heavy casting.
- **Event Dispatcher** — like an event emitter/observer: other Blueprints bind to it (`OnDeath`).
- **Spawn Actor from Class** — create an instance at runtime (enemies, projectiles).
- **Set Timer by Event** — run something every N seconds without Tick.

## 7. Project and editor settings worth knowing

- **Edit → Project Settings:** maps & modes (default level, default GameMode), input, collision, packaging.
- **Edit → Editor Preferences:** turn off "Use Less CPU when in Background" so the editor doesn't crawl when you switch windows **(verify in 5.8)**.
- **Window → World Settings:** per-level; set the **GameMode Override** here for `L_Arena`.
- **Window → Developer Tools → Output Log** for messages.

## 8. Things that confuse beginners

- **Units:** Unreal uses **centimetres**. A 180 cm tall character is 180 units. Blender uses metres by default (see `blender-guide.md`).
- **Axes:** Unreal is Z-up and left-handed; X forward, Y right. Blender is Z-up and right-handed. FBX export settings handle this.
- **Lighting rebuild/shader compile:** the first load of a project and new materials compile shaders. Slow is normal; check the bottom-right corner for progress.
- **"Compile" vs "Save":** compile applies Blueprint logic changes; save writes to disk. Do both.
- **Hot reload:** not relevant until we use C++.

## 9. Top Down template tour (what's already in the project)

The project was created from Unreal's **Top Down** template. Everything below is a working example you can read. We'll build our own versions with WASD movement, but it shows the framework in action.

| Asset (Content/TopDown/…) | What it is | What to look for |
|---|---|---|
| `Lvl_TopDown` | The level that opens by default | Press Play, click the floor: the character walks there |
| `Blueprints/BP_TopDownGameMode` | Rules of the match | Which Pawn and Controller classes it uses |
| `Blueprints/BP_TopDownController` | The PlayerController (input → movement) | How it turns a click into a movement command |
| `Blueprints/BP_TopDownCharacter` | The player Character | Its components: camera on a spring arm, mesh, capsule |
| `Input/IMC_Default` and `Input/Actions/…` | Enhanced Input mapping and actions | Which key/click maps to which Input Action |
| `Content/Characters/Mannequins`, `Content/LevelPrototyping` | Template character art and grey-box props | Free placeholder meshes we can borrow |

How to read a Blueprint without changing anything: double-click it, look at the Components list, then open the Event Graph and follow the white execution wires from the red event node. Don't edit the template files; duplicate them first (right-click → Duplicate) if you want to experiment.

Config note: `Config/DefaultEngine.ini` still points the default map and GameMode at the template. We will change that when we build `L_Arena` and `BP_VSGameMode` (roadmap Phase 2).

## 10. Where to learn more (free)

- Epic Developer Community → Learning (search "Your First Hour in Unreal Engine 5", the Blueprint learning paths, and Enhanced Input). Choose material made for 5.x.
- Official docs: dev.epicgames.com/documentation (set the version selector to 5.8).
- When a tutorial was made on an older 5.x, the idea is the same but menu names may differ; ask Claude to translate to 5.8.
