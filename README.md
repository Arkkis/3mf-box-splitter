# 3MF Box Splitter

Cut a `.3mf` model into two printable halves with an axis-aligned crop box, in the browser.
Useful for pulling a single part off a kit-card sprue, or splitting a model that arrived as one
welded mesh, without losing multi-material paint data.

No build step, no server, no upload — the file never leaves the machine.

## Use

1. Open the page and drop a `.3mf` on it.
2. Drag the six sliders to place the crop box. Tick **Preview** to hide everything outside it.
3. Download the **inside** and **outside** halves as separate `.3mf` files.

Orbit with the left mouse button, zoom with the wheel, pan with shift-drag or right-drag.

## What it does

- Reads every `<object>` across all `.model` parts in the archive, resolves `<component>`
  transforms and the build item transform, and merges the result into one mesh in plate coordinates.
- Splits each triangle exactly against the six box planes (Sutherland–Hodgman), so the cut face is
  clean rather than stair-stepped along triangle edges. The two halves are an exact partition of the
  original surface.
- Welds coincident vertices, walks the open boundary loops left by the cut and caps each one with a
  centroid fan, so both halves come out closed and 2-manifold.
- Copies `Metadata/project_settings.config` into both outputs, so filament slots and printer
  settings still line up.

Painting is preserved: each triangle's `paint_color` string is carried over verbatim, and fragments
of a split triangle inherit their parent's code.

## Limits

- The crop box is axis-aligned. Rotate the model in a slicer first if you need an angled cut.
- `paint_color` is Bambu's own subdivision encoding and is not re-encoded. A triangle that the cut
  splits keeps its parent's full code, so painting can be slightly off on the handful of triangles
  the cut actually passes through. Everything else is exact.
- The preview assigns each distinct paint code a filament colour by frequency (unpainted takes
  filament 1). That is a display guess only — it never affects the exported files.
- Exports carry no plate thumbnails; the slicer regenerates them on load.

## Deploy

Static files. Any host works. For GitHub Pages, push to the default branch and point
**Settings → Pages** at the branch root.

## Licence

MIT
