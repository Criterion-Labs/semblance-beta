# Semblance™ Beta

> Likeness, under light

Semblance™ is a cross-platform desktop studio for inspecting, presenting, converting, and preparing 3D models. It combines multipart mesh and STEP import, surface controls, physically based materials, a reusable Library, studio lighting, and exact-size image export in one workspace.

This page is for **SemblanceBeta**. The program is self-contained, so a separate .NET install is not required. The first launch installs the bundled 1K Sample Pack: 41 Materials and six HDRIs.

Semblance is under active development. File compatibility and visual behavior are tested continuously, but a malformed CAD or mesh file can still expose defects in its source geometry or in a third-party tessellator.

## This beta

SemblanceBeta has no License menu. The live view, the export preview, and exported image, ray-trace, and save-last-image files carry one faint diagonal watermark: Semblance™ by TommyBilt™.

A beta build allows:

- 10 new package saves. Saving a package that already exists does not use another save.
- 10 STL exports, 10 OBJ exports, and 10 3MF exports.

The menu bar shows how many of each remain.

## Install

Each download is one zip.

**Windows.** The zip contains only the Setup program. Run it and approve the administrator prompt. Setup installs SemblanceBeta and can add Start-menu and desktop shortcuts.

**Linux.** The zip contains only the x86-64 AppImage, for mainstream glibc desktops. It does not run on ARM or musl systems. After extracting, allow the file to run as a program, or run `chmod +x` on the AppImage, then start it.

**Mac.** The zip contains only `SemblanceBeta.app`, for Apple Silicon on macOS 26 or later. The app includes KosmicKrisp, so the Apple GPU is used for the live view and for ray tracing. After extracting, Control-click the app and choose Open, then Open again. If macOS reports that the app is damaged or will not run, move it to Applications and run:

```bash
xattr -cr "/Applications/SemblanceBeta.app"
chmod +x "/Applications/SemblanceBeta.app/Contents/MacOS/SemblanceBeta"
```

## What it does

### Models

- Load OBJ, STL, 3MF, STEP, and STP models.
- Keep independently renderable source parts for multipart formats.
- Show volume, surface area, and triangle counts for each accepted model part.
- Select, rename, show, or hide individual model parts.
- Highlight the selected part.
- Switch between shaded and wireframe presentation.
- Use perspective or orthographic cameras and standard inspection views.

### Surfaces and materials

- Edit Standard and physically based material properties per model part.
- Apply base color, opacity, normal, metalness, and roughness when the material source provides them. Recognized height and occlusion maps are retained for later renderer support.
- Generate or transform UV coordinates with AutoUV, box, cylindrical, and planar mapping.
- Adjust U/V scale and shift, and cylindrical seam rotation.
- Rebuild flat or SurfaceHit shading without changing the source model file.
- Apply HDRIs, Backdrops, Colors, and Materials from the Library.
- Create a custom material from a required base-color image and optional supporting maps.
- Install additional 1K HDRI or Material packs from the Library toolbars. Semblance does not check the catalog at startup.
- Import supporting files into Assets → Project. Those files travel inside the saved `.sempkg` package and do not enter the permanent Library.
- Drag a standalone raster Asset onto a visible model part to make a Standard planar material. Images that belong to a reusable Material appear only through that Material.
- Open or double-click a file Asset to launch a temporary copy with the operating system's default application. Delete removes an unused Project Asset after confirmation.

### Decals

Image Assets can be placed on a part as overlays. They follow the local mesh, preserve the underlying material, and appear beneath their host in Scene → Surface. Edit position, size, rotation, opacity, projection depth, stacking order, visibility, and source image. Transparent PNGs leave the host visible where image alpha is zero. Color-key cleanup can remove a fringe or background color. SVG overlays can override fill and stroke color. SVG files must be static and self-contained; convert text to paths for portable artwork.

### Geometry

- Inspect triangle topology before enabling mesh subdivision.
- Attempt mesh repair for an open imported mesh before refinement.
- Refine an eligible mesh part from its retained mesh state.
- Re-tessellate a STEP part with independent linear and angular tolerances.
- Keep the previous valid scene when an import, repair, refinement, or re-tessellation fails.

### Presentation and export

- Adjust the camera, product appearance, light rig, shadows, and reflections from the Viewport. Lighting presets can be saved and restored.
- Use an invisible ground receiver that fits the current view. Ground shadow applies together in the viewport, Image Export, Ray Trace, and their framing previews. The floor does not appear and does not contribute reflected or bounced light. Model self-shadows stay separate.
- Export an exact-size PNG, with optional transparency, or a JPEG at a selected quality.
- Frame ordinary and ray-traced exports in one Viewport flyout. The frame sets the output. Portrait, Landscape, and Long edge change the result without stretching the scene. Stop cancels only the current attempt. Done closes the editor.
- Ray-trace the current view with indirect HDRI and surface illumination, object-to-object reflections, ray-tested soft shadows, Standard and PBR maps, Draft, Balanced, or Fine quality, optional Backdrop compositing, and a sharp ground reflection.
- Windows and Linux use hardware ray tracing when the graphics card supports it. Every platform, including Apple Silicon, can render the same frame with Vulkan compute. The CPU is used only when both GPU paths cannot finish the frame. Stop does not start the CPU fallback.
- Convert the current model to OBJ, STL, or 3MF.

## Packages

A `.sempkg` file is the saved workspace. It is a self-contained archive of the model, scene settings, required material content, and workspace Assets. Library items that already exist on the computer may remain references. Missing optional Library items fall back to documented defaults and produce a warning.

Drop a `.sempkg` file onto the viewport to open it. When a model is already open, Semblance asks whether to save the current workspace. Opening a package from another folder first copies it into the managed packages folder and leaves the original in place.

`Ctrl+N` starts a blank package. It clears the model, workspace Assets, scene settings, undo history, and save target, and restores the default Cloud Fade Backdrop. Library content and application preferences stay as they are.

The Files tab lists saved packages. Open, double-click, or drag a row onto the viewport. Rename and confirmed Delete keep the `.sempkg` extension. Additions, renames, and deletions made outside Semblance appear in the list.

Assets → Project holds files you import into the current package. Assets → Packages is a read-only view of the package you opened. Copy an item from that view into Project before editing it. Neither action installs the file into the shared Library.

## File formats

| Purpose | Read | Write | Notes |
| --- | --- | --- | --- |
| Polygon models | `.obj`, `.stl`, `.3mf` | `.obj`, `.stl`, `.3mf` | Multipart structure is kept where the format can represent it. STL is a single triangle surface. |
| CAD models | `.step`, `.stp` | — | STEP and STP parts are tessellated into renderable meshes. |
| Packages | `.sempkg` | `.sempkg` | The saved workspace: model, scene, required materials, and workspace Assets. |
| Workspace Assets | Any non-empty file | Embedded in the package | Grouped by file type. PNG, JPEG, BMP, GIF, WebP, TIFF, and SVG images can be placed on a part. |
| Library material images | `.png`, `.jpg`, `.jpeg` | Managed Library assets | Imported images are normalized to 1K, 2K, or 4K. |
| Rendered images | — | `.png`, `.jpg`, `.jpeg` | PNG can be transparent. JPEG always includes a background. |

A supported extension does not make every source file valid. Semblance reports the part that failed instead of replacing the scene with an empty result.

## Library

The Library holds Files, HDRIs, Backdrops, Colors, and Materials. The first run installs the bundled 1K Sample Pack locally. It does not contact the website.

HDRI and Material toolbar actions are the only catalog checks. They list installable 1K category packs. Semblance verifies each archive size and SHA-256 hash before the pack is committed. An installed pack is labeled `INSTALLED` and is not downloaded again. A failed or cancelled install rolls back.

Additional packs are published at [tommybilt.com](https://www.tommybilt.com) under `/downloads/semblance/`.

## Where files are kept

| Data | Default location |
| --- | --- |
| Logs | `%LOCALAPPDATA%\TommyBilt\Semblance\Logs` on Windows. On Linux and macOS, the equivalent is under the user data folder `TommyBilt/Semblance/Logs`. |
| Beta license and export counts | `TommyBilt\Semblance Beta` in the same local application data folder. |
| Packages | `Documents\Semblance\Projects`. This folder can be changed in Settings. |
| Shared Library on Windows | `%PROGRAMDATA%\TommyBilt\Semblance\Assets` |
| Library on Linux and macOS | The user data folder `TommyBilt/Semblance/Assets` |

## Requirements

- Windows or Linux: a Vulkan-capable GPU and a current vendor graphics driver. Linux also needs the usual desktop libraries.
- Mac: Apple Silicon and macOS 26 or later.
- No separate .NET runtime and no Vulkan SDK.

## Help

The question-mark icon, or `F1`, opens the Help manual inside Semblance. Help stays open while the workspace remains usable, does not need a browser, and matches the installed version. Search tolerates likely misspellings.

## Uninstall

Uninstall removes the application and leaves packages, the Library, preferences, and logs in place unless you choose to remove them.

On Windows, Setup's uninstaller offers that choice. Removing only the application is the default. On Linux, run the AppImage with `--uninstall`. Press Enter to keep the data, or type `YES` to remove it.

Removing everything deletes the Library, preferences, logs, and the managed `Documents\Semblance\Projects` folder that the uninstaller owns. Files you stored somewhere else stay where you put them. A later install of the Sample Pack or other Library packs is required again only when the Library was removed.

An upgrade replaces the program and keeps Library packs, preferences, saved packages, and logs.

## Report a problem

1. Select the bug icon beside About.
2. Enter a title and describe what happened. Add the category, steps, expected behavior, and affected content when you have them.
3. Leave system details enabled when the version and platform would help.
4. **Open Logs** only shows the log folder. Semblance does not upload it.
5. **Review & Submit on GitHub** opens a prefilled report on this repository. Review it, attach only files that are safe to share, and submit it yourself.

GitHub requires an account to submit. The form links to [GitHub signup](https://github.com/signup). Issues are tracked at [Criterion-Labs/semblance-beta](https://github.com/Criterion-Labs/semblance-beta/issues).

Logs can contain exception text, part names, and local file paths. They are not intended to contain model geometry, textures, packages, or credentials. Read a log before sharing it.
