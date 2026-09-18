# Latent

*A recipe-aware photo library for Fujifilm shooters. Plan v1.0.*

> Working title. A latent image is the invisible picture sitting in exposed film before it is developed. A latent space is where a model learns what things have in common. This app lives in both.

---

## The feeling

You open the app on a gray Tuesday. There's no dashboard or stats wall, just a contact sheet from a roll you shot three winters ago on the coast, one you forgot existed. Frame 14 stops you. You press space and it fills the screen. You press R and the print flips over: on the back is the recipe card, written the way the camera saw it. Classic Chrome, shadows +1, grain weak, WB shifted toward red, taken at EV 9 under an overcast sky. You wonder what it would have looked like in Classic Neg, drag one dial, and the image shifts before your finger leaves the key. You like it. You send it to the camera, and the camera's own processor renders the real thing. Then you write the recipe to C4, because you're going out tomorrow and it's supposed to be overcast.

Everything in this plan exists to make that sequence feel inevitable.

---

## What it is, and what it is not

**It is:**

- A fast, local, beautiful way to browse thousands of Fujifilm photos by what makes Fuji photos different: the recipe.
- A place to understand color: what a recipe does, why a photo looks the way it does, and how close a Fuji can get to a look you love.
- A bridge between your library and your camera: preview recipes, render with the camera's own processor, and write recipes back to custom slots.

**It is not:**

- An editor. No masks, sliders for exposure, healing, or export pipelines. Capture One already does that.
- A cloud service. Everything is local, offline, and yours. No accounts.
- A general DAM. Latent is Fujifilm-only, unapologetically. Files from other cameras are skipped at scan time and reported as a single count ("1,204 non-Fujifilm files skipped"), never half-supported. Every screen can assume a recipe exists, and that assumption is what keeps the design clean.
- A replacement for taste. The models give measured starting points; the photographer decides.

---

## Principles

- **The photos are the only color on screen.** The interface is built from neutral grays (OKLab a = b = 0) with no tinted chrome, no colored selection highlights, and no colored icons, because any color near a photo biases how you judge it. This mirrors ISO 12646's neutral surround guidance for color-critical viewing. This is the product's signature visual choice.
- **Instant or honest.** Every interaction either responds within a frame, or tells you exactly what it's waiting on ("rendering on camera, 3 of 9").
- **Keyboard first, mouse welcome, dials optional.** Every action has a key. Recipe parameters can be mapped to physical MIDI knobs.
- **Read-only toward your originals.** The app never writes to a RAF or JPEG. All state lives in its own database and cache.
- **Metadata is the material.** The recipe, the light, the place, and the palette aren't sidebars; they're how you move through the library.
- **Show the math when asked.** Every inference (a matched recipe, a predicted look, a light estimate) can reveal its confidence and inputs in one click. Never magic, never hidden.

---

## Core concepts

These are the nouns of the app. Users learn them once and they hold everywhere.

- **Photo:** one captured frame, which may be several files (RAF, JPEG, your Capture One export) grouped together.
- **Recipe:** the complete in-camera rendering settings for a photo, stored as a canonical settings vector. Two photos share a recipe when their vectors match exactly. Recipes can be named by you or imported from lists you keep. Every recipe is tagged with the sensor and processor generation it came from (X-Trans III, IV, V), because available film simulations and the rendering of the same settings differ across bodies. A recipe is only offered for writing to cameras that can actually reproduce it.
- **Roll:** an automatically detected shooting session, grouped by time gaps and, when available, place. Rolls are the unit of rediscovery and are shown as contact sheets. This is the app's one deliberate film metaphor.
- **Light:** the scene brightness at capture, estimated as EV100 = log2(N²/t) − log2(ISO/100), corrected for exposure compensation, and bucketed into human terms (night, dim interior, overcast, open shade, bright sun).
- **Palette:** the dominant colors of a photo, extracted in OKLab so that distances match perception.
- **Place:** a location with its source and confidence: from the camera, reconstructed from a GPX track, or set by hand.
- **Look:** a target appearance you want a recipe to hit, defined by a reference image, a color chart, or a set of photos.

---

## Navigation

Five spaces, one overlay, and a command bar. Nothing is nested more than two levels deep.

| Key | Space | What it's for |
|---|---|---|
| `1` | **Light Table** | Home. Rediscovery. A contact sheet from a past roll, "on this day," frames you haven't opened in years. |
| `2` | **Library** | Everything, as a fast grid or a timeline of rolls. Filter by anything. |
| `3` | **Recipes** | Every recipe you've shot, as cards. Usage over time, performance by light, sample frames. |
| `4` | **Atlas** | Your photos on a map and globe, with trips and places. |
| `5` | **Lab** | Re-render, compare, match a look, write to the camera. |

- **Loupe (Space, from anywhere):** a full-screen view of one photo. Press `R` to flip it to the recipe card on its back. Arrow keys move through the current set, and the filmstrip at the bottom shows neighbors.
- **Command bar (Ctrl+K or /):** search and actions in one field. Queries are parsed into filter chips as you type: `classic chrome overcast 2023 teal` becomes Recipe: Classic Chrome, Light: overcast, Year: 2023, Color: teal. The same bar runs actions: "write this recipe to C3," "open roll from Acadia."
- **Color lens (C):** not a space but a lens over any grid. It re-sorts or filters the current set by palette, or lays it out as a color field.

### Wireframe: Library with loupe filmstrip

```
┌────────────────────────────────────────────────────────────────────────┐
│  Light Table   Library   Recipes   Atlas   Lab          [ Ctrl+K ...  ]│
├──────────────┬─────────────────────────────────────────────────────────┤
│ Rolls        │  [Classic Chrome ×] [Overcast ×] [2023 ×]      412 frames│
│  2026        │ ┌────┐┌────┐┌────┐┌────┐┌────┐┌────┐┌────┐┌────┐        │
│  2025        │ │    ││    ││    ││    ││    ││    ││    ││    │        │
│  2024        │ └────┘└────┘└────┘└────┘└────┘└────┘└────┘└────┘        │
│  2023  ◀     │ ┌────┐┌────┐┌────┐┌────┐┌────┐┌────┐┌────┐┌────┐        │
│   Acadia     │ │    ││    ││    ││    ││    ││    ││    ││    │        │
│   Portland   │ └────┘└────┘└────┘└────┘└────┘└────┘└────┘└────┘        │
│   Baxter     │                                                         │
│              │                                                         │
│ Recipes      │                                                         │
│ Light        │                                                         │
│ Places       │                                                         │
└──────────────┴─────────────────────────────────────────────────────────┘
```

### Wireframe: the recipe card (back of the print)

```
┌───────────────────────────────────────────────┐
│  Coastal Chrome                    your name  │
│                                               │
│  Film simulation     Classic Chrome           │
│  Grain               Weak, small              │
│  Color Chrome        Strong    FX Blue  Weak  │
│  White balance       Daylight   R +2  B −4    │
│  Highlights  −1      Shadows  +1              │
│  Color  +2           Clarity  0               │
│  Dynamic range       DR400                    │
│                                               │
│  Light  EV 9, overcast                        │
│  Used on 214 frames across 11 rolls           │
│  ▁▂▅▇▃▁▁▂▆▄   2021 to 2026                     │
│                                               │
│  [ Try another recipe ]  [ Write to camera ]  │
└───────────────────────────────────────────────┘
│ ░ ▓ ░ ▓  22A  ▸  CLASSIC CHROME  ▸  EV9  ░ ▓ │  ← edge strip
```

The thin strip along the bottom is modeled on film edge printing: frame number, recipe, and light, set in a dot-matrix face. It appears under every photo in the Loupe and is the one decorative flourish the app allows itself.

---

## Features

Features are sorted into three tiers. **Foundation** makes the app useful on its own. **Signature** makes it unlike anything else. **Horizon** is where it goes once the research works.

### Light Table (home)

- **Foundation:** a random past roll presented as a contact sheet on every launch. "On this day" across years. Frames you haven't opened in over a year.
- **Signature:** "same light, different year": frames shot in similar light and season to today.
- **Horizon:** a resurfacing model that learns which rediscovered frames you actually open, and weights toward those.

### Library

- **Foundation:**
  - A virtualized grid that stays smooth at 100,000 frames.
  - Rolls timeline, grouped by year.
  - Filter chips for recipe, film sim, any single setting, light, lens, focal length, year, place, and rating.
  - Photo stacks: RAF, out-of-camera JPEG, and Capture One export grouped as one photo, matched by capture time and filename.
  - Star ratings imported from XMP sidecars when present.
- **Signature:**
  - Color lens: sort or filter by palette, or "more like this color."
  - Camera vs. your edit: hold a key in the Loupe to toggle between the camera's render and your Capture One export.
- **Horizon:** semantic search ("foggy harbor at dusk") using local CLIP embeddings.

### Recipes

- **Foundation:**
  - Every distinct recipe as a card with sample frames, frame count, and a usage-over-time sparkline.
  - Name your recipes, or import a recipe list from a text file.
  - Named-recipe matching across the library, including near matches ("Kodachrome 64, except shadows +1").
- **Signature:**
  - **Performance by light:** for each recipe, which light conditions you actually shoot it in, and which ones you keep (by rating). This answers "what should I load for tomorrow's weather" from your own history.
  - **Recipe diff:** pick two recipes and see exactly what differs, visualized as a tone curve, a hue shift map, and a rendered color chart.
  - **Shareable recipe card:** export a clean image of the settings plus four sample frames, the format photographers already share recipes in.
- **Horizon:** a recipe recommendation for a given forecast, based on your taste model.

### Atlas

- **Foundation:**
  - Map and globe view (MapLibre GL) with clustering at every zoom.
  - Place source and confidence shown on every pin.
  - Draw a region to filter the library.
- **Signature:**
  - **GPX geotag reconstruction:** import a phone GPS log, correct for camera clock drift and time zones, and interpolate positions. Every reconstructed place keeps its confidence and can be reviewed before it's saved.
  - **Trips:** automatic spatiotemporal clustering (ST-DBSCAN) of rolls into journeys, drawn as routes.
- **Horizon:** "near here" suggestions from nearest-neighbor spatial queries.

### Lab

- **Foundation:**
  - **Camera render:** send any RAF to the connected camera with a chosen recipe and get back the true in-camera JPEG.
  - **Comparison grid:** one RAF rendered under N recipes side by side.
  - **Write to camera:** save a recipe to a custom slot (C1 to C7 on the X-T4).
- **Signature:**
  - **Instant preview:** the forward model applies any recipe in real time as you adjust settings. When you commit, the camera renders the real thing, and the app shows the difference between the prediction and the truth, so you always know how much to trust the preview.
  - **Dials:** map any MIDI controller's knobs to recipe parameters, so adjusting a recipe feels like turning the camera's dials.
  - **A/B picking:** choosing between two renders feeds your taste model.
- **Horizon:**
  - **Match a look:** drop in a film scan, a movie still, a color chart, or a set of your own Capture One exports, and get the closest reachable recipe, a closeness score, and a visualization of what the camera can't reach.
  - **Your edits as a recipe:** "your last 200 Capture One edits are closest to this in-camera recipe," which lets you get your edited look straight out of the camera.

---

## Visual design

### Palette

The interface has no hue. Every surface is a neutral gray, so the photos carry all the color.

| Token | Value | Use |
|---|---|---|
| `surround` | OKLab L 0.22, a 0, b 0 | Main background (dark mode) |
| `surface` | OKLab L 0.27, a 0, b 0 | Panels, cards |
| `line` | OKLab L 0.36, a 0, b 0 | Dividers, grid gaps |
| `text` | OKLab L 0.92, a 0, b 0 | Primary text |
| `text-quiet` | OKLab L 0.68, a 0, b 0 | Secondary text |
| `safelight` | a single deep amber | Only for "camera connected" and "rendering on camera." Never near a photo. |

- **Three viewing surrounds:** dark, middle gray (around 18% reflectance, the photographic reference), and light. You pick one per task: dark for browsing, middle gray for judging color.
- **Selection is shown with brightness, not color:** a white outline and a slight lift, never a colored ring.

### Type

- **Interface:** Atkinson Hyperlegible Next. It's very legible at small sizes and has a distinct character, which suits dense metadata.
- **Edge strip only:** a dot-matrix face (such as Doto) in the style of film edge printing. It's used nowhere else.
- **Sentence case everywhere.** No all-caps labels, except that the edge strip imitates real edge printing.

### Motion

- **One orchestrated moment:** the Loupe flip from photo to recipe card, a quick card turn that answers the `R` key.
- **Otherwise, motion only answers actions:** opening, filtering, and confirming. No hover animations on grid cells and no entrance animations.
- **Reduced motion is respected everywhere.**

### Color management

For a color app, this is non-negotiable:

- Honor embedded ICC profiles on every image.
- Render to the display profile.
- Never let a thumbnail pass through an unmanaged path.

This needs early verification, because WebView2's handling of display profiles on Windows has to be confirmed before trusting it.

---

## Architecture

### Stack

- **Shell:** Tauri 2. A Rust core with a web frontend, a small install, and low memory use.
- **Frontend:** React with TypeScript, TanStack Virtual for the grids, MapLibre GL JS for Atlas, and a WebGL2 shader for instant recipe previews.
- **Core (Rust):**
  - Indexer and file watcher (`walkdir`, `notify`).
  - SQLite in WAL mode (`rusqlite`), using the built-in R*Tree module for spatial indexing and FTS5 for text search.
  - LibRaw through FFI for RAW decoding in the Lab.
  - `nusb` for native USB and PTP communication with the camera.
  - `ort` (ONNX Runtime) for inference, using DirectML on Windows so it runs on integrated GPUs.
- **Metadata:** ExifTool bundled as a sidecar and run in `-stay_open` batch mode, since it's the most complete reader of Fujifilm MakerNotes.
- **Model training:** Python and PyTorch, off-device, exported to ONNX. No Python ships with the app.

### Data flow

```
 folder ──▶ scanner ──▶ changed files ──▶ ExifTool batch ──▶ metadata ──┐
   │           (size, mtime, xxh3 of head+tail)                           │
   │                                                                       ▼
   └────────▶ RAF header ──▶ embedded JPEG ──▶ thumbnails (WebP, 3 sizes) SQLite
                                     │                                     ▲
                                     └──▶ palette (OKLab k-means) ─────────┤
                                     └──▶ light (EV100) ──────────────────┤
                                     └──▶ embeddings (later) ─────────────┘
```

- **Fujifilm filter first:** the scanner checks the camera make before any other work, so non-Fujifilm files cost almost nothing to skip.
- **Incremental by design:** a file is reprocessed only when its size, mtime, or head-and-tail hash changes. Hashing lets moved and renamed files keep their history.
- **Thumbnails without decoding RAW:** the RAF header stores the offset and length of the camera-rendered JPEG preview, so thumbnails come from a direct byte read. This is the single biggest speed win.
- **Everything derived is disposable:** the cache can be deleted and rebuilt at any time, and only ratings, names, and confirmed places are irreplaceable.

### Data model (core tables)

- `photo` (id, capture_time, camera, lens, rating, roll_id)
- `file` (id, photo_id, path, kind, size, mtime, hash)
- `recipe` (id, canonical_hash, settings_json, name)
- `photo_recipe` (photo_id, recipe_id, as_set_json, as_applied_json)
- `roll` (id, start, end, place_summary)
- `light` (photo_id, ev100, bucket)
- `palette` (photo_id, colors_oklab, weights)
- `place` (photo_id, lat, lon, source, confidence), plus an R*Tree index over coordinates
- `render` (id, file_id, recipe_id, source [camera | model], path, created)
- `look` (id, kind, definition_json)

**Recipe canonicalization matters:** some settings are "Auto" as set but resolve to a concrete value in the file (dynamic range is the main case). Storing both "as set" and "as applied" keeps recipe matching honest.

### Performance targets

These are goals to measure against, not claims.

- First thumbnails visible within 2 seconds of adding a folder.
- Incremental rescan of an unchanged 20,000-file library in under 2 seconds.
- Grid scrolling at 60 fps at 100,000 frames.
- Instant preview under 16 ms per update at screen resolution.

### Quality

- A fixture set of RAF and JPEG files covering every film simulation, including the monochrome quirks where Acros lives in a different tag.
- Golden-image tests for the thumbnail and color pipelines.
- Property tests for recipe canonicalization.
- CI builds for Windows, macOS, and Linux on GitHub Actions.

---

## The research track

This is the part that makes Latent more than a nice viewer, kept separate so the app ships even if the research runs long.

### Models

- **Recipe inference (pixels to settings):** trained on your library's embedded previews, labeled for free by their own MakerNotes. It identifies recipes on photos that have lost their metadata.
- **Forward model (RAW plus recipe to rendered image):**
  - Predicts a settings-conditioned 3D LUT plus a tone curve, following the image-adaptive 3D LUT approach (Zeng et al., 2020).
  - A LUT runs in a fragment shader in microseconds, which is what makes instant preview possible.
  - Local operations (clarity, sharpening, grain) are modeled separately or left to the camera render. The preview says which parts are approximate.
- **Inverse search (look to recipe):**
  - Searches over the discrete recipe space against a target: CIEDE2000 on color chart patches, or sliced Wasserstein distance on OKLab distributions for whole scenes.
  - Because the LUT is cheap to evaluate, broad search is feasible before any refinement.
- **Taste model:** a Bradley–Terry model over your A/B picks and ratings, conditioned on light.

### Data

- **Observational:** RAF plus embedded-preview pairs from your library. Free and plentiful, but confounded (you chose recipes for scenes) and narrow in coverage.
- **Interventional:** the same RAFs rendered by the camera under sampled recipes, generated overnight by the Lab's render queue. This fixes both the confounding and the coverage problem.
- **Film targets:** color charts shot on real film and on the X-T4 under the same light, plus manufacturer characteristic curves.

### Evaluation

- **Forward model:** color error on held-out recipes the model never saw, not just held-out photos.
- **Inverse search:** distance to target versus Fuji X Weekly's published recipes for the same film stocks, used as an expert baseline. Ask Ritchie Roesch before using any of his sample images, and credit him prominently either way.
- **Everything is reported with honest failure cases,** especially looks the camera cannot reach.

### Compute

- **Training:** a cloud or university GPU, since the XPS 9315 has only integrated graphics.
- **Inference:** runs locally on the integrated GPU through DirectML.

---

## Roadmap

Sized for roughly nine months, aimed at a portfolio-ready build by spring recruiting and a polished release before graduation.

### Phase 0: Census (late September)

- ExifTool dump of the library: film sim counts, recipe diversity, GPS coverage, RAW versus RAW+JPEG.
- Verify camera-side rendering over USB on the X-T4 (FilmKit or libfuji as reference).
- Settle the name and the open design questions.
- **Gate:** if camera rendering fails, the Lab ships without it and the research track uses observational data only.

### Phase 1: Foundation (October to December)

- Indexer, thumbnails, Library, Loupe, rolls, Light Table.
- Recipe fingerprints, naming, and the Recipes space.
- Light estimation and filters.
- Atlas with GPX reconstruction and trips, doubling as the spatial databases final project.
- **Ship v0.1.**

### Phase 2: Signature (January to March)

- Color lens and palettes.
- Camera render, comparison grid, write to camera.
- Forward model v1 and instant preview.
- Recipe diff and shareable cards.
- **Ship v0.5, the portfolio build.** Record the demo video here.

### Phase 3: Research (March to May)

- Interventional data at scale.
- Inverse search and film chart targets.
- Evaluation against Fuji X Weekly recipes, with a written report.
- Taste model and "your edits as a recipe."

### Phase 4: Release (May to June)

- Signed installers, documentation, a project page, and a short technical writeup.
- **Ship v1.0.**

---

## Risks

| Risk | Impact | Response |
|---|---|---|
| X-T4 camera rendering can't be scripted | Lab loses true renders; research loses interventional data | Decide at Phase 0; fall back to observational data and preview-only Lab |
| WebView2 color management is incomplete | Colors shown wrong in a color app | Verify in Phase 0; render managed thumbnails in Rust if needed |
| Forward model underfits local ops | Preview visibly differs from camera | Scope preview to global color and tone, and label the rest |
| Scope creep | Nothing ships by recruiting season | Tiers are strict; Horizon features never block a phase |
| Film targets are slow and costly | Research track stalls | Limit to two stocks; charts before scenes |

---

## Decisions

- **Mood: strict neutral darkroom.** Hueless grays, photos as the only color, and safelight amber reserved for the camera link.
- **Film vocabulary: light touch.** Rolls, contact sheets, and the edge strip, and nowhere else. Everything else uses plain photo language, so nobody has to learn a metaphor to find a setting.
- **Scope: Fujifilm only.** No partial support for other cameras.

## Still open

- **Name.** Latent is the working title. It needs a trademark and app-name check.
- **Distribution.** Open source from day one, or private until v0.5? Public from day one builds a visible commit history for recruiters, at the cost of showing rough early work.
