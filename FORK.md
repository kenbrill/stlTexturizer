# BumpMesh (stlTexturizer) — vendored copy

Browser-based surface-displacement texturing tool by Stefan Hermann (CNC Kitchen).
Vendored into the GrowerWebsite so customers can apply real, printable texture to
the modules they configure, without any data leaving their browser.

- **Upstream:** https://github.com/CNCKitchen/stlTexturizer
- **Pinned commit:** `a6ac179149b8a17c71a9469dd4cb6f866c0c01d1` (2026-07-23)
- **License:** GNU AGPL v3.0 — see `LICENSE` in this folder.

## Fork modifications

One addition, clearly marked in `js/main.js` (search for
`GrowerSite fork addition`):

- `?stl=<same-origin url>[&maskBottom=<mm>]` — when the app is opened with
  these query params it auto-loads the given STL and pre-excludes every face
  within `<mm>` of the model's bottom (the stacking slip fitting), so
  displacement texture cannot be applied to the slip fit. The mask is saved
  with the project (`mask.json`) and can still be refined with the built-in
  paint tools.

Everything else is unmodified upstream code. To regenerate the diff against
upstream: clone the repo at the pinned commit and diff `js/main.js`.

## Serving

`public/` is copied into the build output by Vite, so the tool is served at
`<base>/texturizer/` (e.g. `/tower/texturizer/` or `/grower/texturizer/`).
Dependencies (three.js, fflate) load from the jsDelivr CDN via the importmap
in `index.html` — same as upstream.
