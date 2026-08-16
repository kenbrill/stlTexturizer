# BumpMesh (stlTexturizer) — vendored copy

Browser-based surface-displacement texturing tool by Stefan Hermann (CNC Kitchen).
Vendored into the GrowerWebsite so customers can apply real, printable texture to
the modules they configure, without any data leaving their browser.

- **Upstream:** https://github.com/CNCKitchen/stlTexturizer
- **Pinned commit:** `a6ac179149b8a17c71a9469dd4cb6f866c0c01d1` (2026-07-23)
- **License:** GNU AGPL v3.0 — see `LICENSE` in this folder.

## Fork modifications

All changes are clearly marked in `js/main.js` (search for
`GrowerSite fork addition`):

- `?stl=<same-origin url>[&maskBottom=<mm>][&maskInner=<mm>][&maskTubes=<n>]`
  — auto-loads the given STL and pre-excludes faces: (a) every face within
  `<mm>` of the model's bottom (the stacking slip fitting); (b) every face
  whose centroid is within `<mm>` of the model's rotation axis (the body-bore
  interior, inverted-funnel diverter, riser and support arms) — the axis is
  recovered from the tool's own `currentPoseTrans` (the mesh is centred on
  load, so the model origin lands there); and (c) with `maskTubes=<n>`, every
  face inside the `<n>` seedling-tube bores (the grow-cup seats). Each tube's
  axis is detected from the mesh itself: the flat tip cap of each tube is a
  disk perpendicular to the axis, so its face normals point exactly along the
  tube axis (outward+up at 55/60° — the only faces with that z-component; the
  funnel cones are steeper), and the caps are clustered with k-means (handles
  the two-tier 6/10/12-tube layouts). The tube-mask vertex reads use the raw
  position array (a non-indexed BufferGeometry stores face *i*'s three
  vertices at raw floats `[i*9..i*9+8]`); the earlier version passed those
  offsets to `getX()`, which treats its argument as a *vertex* index and
  multiplies by 3 internally — scrambling every vertex read and pulling the
  detected axes 10–17mm off the true tube axes (texture leaked into the
  bores and could be masked off the exterior on one side). The masks are
  saved with the project (`mask.json`) and can still be refined with the
  built-in paint tools.
- **No-download mode** — the Export STL button runs the full texture
  pipeline but never downloads a file; the result is stashed in IndexedDB
  (keyed by `?module=`/`?line=`) so the hosting configurator can pick it up
  when the user returns. Export-3MF, Save-project and sponsor download links
  are hidden.
- **Order capture** — the IndexedDB stash also records a full snapshot of
  the texture `settings` (projection mode, scale, amplitude, smoothing,
  regularize flags, etc.) so any order can be reproduced or documented. And
  on every Apply the textured STL + settings are POSTed to the site's own
  `/api/order-textures.php` under a persistent per-browser session id
  (localStorage `growerSessionId`), so abandoned carts and textures survive
  a cleared IndexedDB. The configurator sends the same session id at
  checkout and the server merges the session's uploads into the real order.

Everything else is unmodified upstream code. To regenerate the diff against
upstream: clone the repo at the pinned commit and diff `js/main.js`.

## Serving

`public/` is copied into the build output by Vite, so the tool is served at
`<base>/texturizer/` (e.g. `/tower/texturizer/` or `/grower/texturizer/`).
Dependencies (three.js, fflate) load from the jsDelivr CDN via the importmap
in `index.html` — same as upstream.
