# Latent: Phase 0 + Phase 1 plan (empty repo → public v0.1)

## Context

The repo holds only `latent-plan.md` (design v1.0), `CLAUDE.md`, `PROJECT_STATE.md` and `/wrap-up`. There is no code. This plan takes Latent from that state to a public v0.1 (the plan's Phase 1 ship) by mid-December 2026, after a two-week Phase 0 that retires the two risks that could change the design: camera rendering over USB and WebView2 color management.

Decisions from the user: scope is Phase 0 + Phase 1; the repo is **public from day one**; the library location comes later (make it configurable, never hard-code).

**Environment gaps found (must fix first):** Rust/cargo, Node/pnpm, ExifTool, VS C++ Build Tools and `tauri-cli` are all missing. Present: Python 3.14, git 2.55, WebView2 153.

## Phase 0: Census and gates (Sep 18 → ~Oct 2)

Goal: environment ready, repo publishable, three spikes done, and the go/no-go gates recorded in `PROJECT_STATE.md`.

| # | Task | Output / acceptance |
|---|---|---|
| 0.1 | Install toolchain: VS Build Tools (C++ workload), rustup (stable, `x86_64-pc-windows-msvc`), Node LTS + pnpm, ExifTool (Windows exe), `cargo install tauri-cli`. | `rustc`, `cargo`, `pnpm`, `exiftool`, `cargo tauri --version` all resolve. Versions pinned in `rust-toolchain.toml` and `.nvmrc`. |
| 0.2 | Public-repo hygiene: `LICENSE`, `README` stub, `.gitignore` (photos, `*.db`, `census/`, private fixtures), conventional commits, branch-per-milestone PRs, CI skeleton. | Nothing private can be committed by accident. CI runs on Win/mac/Linux. |
| 0.3 | **Census.** Python script drives `exiftool -json -r -ext raf -ext jpg` over `LATENT_LIBRARY` (env var). Report: film-sim counts, distinct recipe vectors, GPS coverage, RAW vs RAW+JPEG, Auto-vs-applied dynamic range, the Acros/monochrome tag quirk. | `tools/census/` script. Raw dump stays in gitignored `census/`. Only an aggregate `docs/census.md` (no paths, no coordinates) is committed. |
| 0.4 | **Spike A: camera render over USB (X-T4).** Use FilmKit / libfuji as reference; try PTP via `nusb`. Timebox 4 days. | Pass = script sends a RAF plus a recipe and gets back a JPEG, and can write a custom slot. **Gate:** on fail, the Lab ships without camera render and research uses observational data only (per plan). |
| 0.5 | **Spike B: WebView2 color management.** Test images with embedded sRGB, Display P3 and AdobeRGB profiles plus known patches; compare against a reference viewer on the actual display. Timebox 2 days. | Pass = WebView2 renders managed colors correctly. Fail = thumbnails are color-managed in Rust (`lcms2`/`moxcms`) before display. Decision changes M3. |
| 0.6 | **Spike C: RAF embedded-JPEG read.** Parse the RAF header offset/length, read bytes directly, time it over the census set. | ms/file figure. Validates the "thumbnails without decoding RAW" design. |
| 0.7 | Fixture strategy: a small public synthetic set for golden tests, plus an optional private real-photo set via env var. | Fixtures cover every film sim incl. Acros. No real GPS or private photos in git. |
| 0.8 | Close open questions: name/trademark check; license choice (check LibRaw LGPL/CDDL and ExifTool licensing for bundling as a sidecar); offline basemap approach for Atlas (see M7). | Recorded under "Decisions" in `PROJECT_STATE.md`. |

**Phase 0 exit:** toolchain works, census report exists, Spike A/B/C results recorded, name and license decided, repo public-safe.

## Phase 1: Foundation (Oct 5 → mid-Dec), ships v0.1

Architecture as in `latent-plan.md` §Architecture: Tauri 2, React + TS, Rust core, SQLite WAL, ExifTool sidecar, LibRaw later. Layout: Cargo workspace with `latent-core` (scanner, db, exif, thumbs, recipes, rolls) and `latent-app` (Tauri commands), plus a `ui/` React app. Keep the core UI-free so it can be benchmarked and tested headlessly.

| Milestone | Weeks | Scope | Acceptance |
|---|---|---|---|
| **M1 Scaffold** | 1–2 | Tauri 2 + Vite/React/TS shell, workspace, SQLite migrations (`user_version`), neutral-gray design tokens (OKLab a=b=0 from the plan's palette table), Atkinson Hyperlegible Next, CI on 3 OS, test harnesses. | `cargo tauri dev` opens a neutral-gray window; CI green on all 3 OS. |
| **M2 Indexer** | 2–4 | Scanner with the Fujifilm make check first; skipped-file count; incremental detection (size, mtime, xxh3 head+tail); ExifTool `-stay_open` batch; tables `photo/file/recipe/photo_recipe`; photo stacks (RAF + JPEG + Capture One export by time and filename); XMP rating import; `notify` watcher. | Unchanged 20k-file rescan < 2 s (criterion benchmark). Moved/renamed files keep history. |
| **M3 Thumbnails** | 4–6 | RAF embedded-JPEG byte read → WebP in 3 sizes; disposable cache; color-managed path per Spike B. | First thumbnails visible < 2 s after adding a folder. Cache can be deleted and rebuilt. |
| **M4 Library + Loupe** | 5–8 | TanStack Virtual grid; filter chips (recipe, film sim, single settings, lens, focal length, year, rating); Loupe with filmstrip; `R` recipe-card flip (the one animation); keys 1–5 space switching; basic Ctrl+K command bar that parses filter chips. | 60 fps scroll on a synthetic 100k-frame corpus. Every action has a key. Reduced-motion respected. |
| **M5 Recipes** | 6–9 | Canonicalization storing both as-set and as-applied (dynamic range); sensor/processor generation tag; recipe cards with sample frames, counts and usage sparkline; naming; text-file import; near-match named recipes. | Property tests for canonicalization pass. Two photos share a recipe only on exact vector match. |
| **M6 Rolls + Light** | 8–11 | Roll detection (time gaps, then place); contact-sheet view; EV100 and light buckets; light filters; Light Table home (random past roll, "on this day", unopened > 1 yr). | Needs a small `view_log` table (a data-model addition the plan implies but doesn't list). Roll boundaries match hand-checked samples. |
| **M7 Atlas** | 9–12 | MapLibre map/globe with clustering; place source and confidence on every pin; R*Tree region-draw filter; GPX import with clock-drift/time-zone correction and reviewable interpolation; ST-DBSCAN trips. | Region filter returns correct sets against a brute-force check. Reconstructed places are reviewable before saving. |
| **M8 Release v0.1** | 12 | Perf report against the four plan targets, README with screenshots, `docs/`, unsigned Windows installer via GitHub Releases, known-limits list. | Installer runs on a clean Windows machine. Perf report published, with honest misses. |

**Sequencing:** M1 → M2 → M3 → M4 are strictly serial. M5 can overlap M4's second half. M6 needs M2 and M5. M7 needs M2. M8 last. If time runs short, cut GPX/trips (M7) before anything in M2–M5, since the plan already marks them Signature-tier.

**Cross-cutting (every milestone):**
- Tests: golden-image tests for thumbnail/color pipelines, property tests for recipes, fixtures per Phase 0.7.
- Read-only toward originals: the app never opens a RAF/JPEG for writing (enforce with a test).
- Privacy: no real paths, coordinates or photos in commits, issues, or screenshots.
- Run `/wrap-up` at the end of each session; update `PROJECT_STATE.md` at each milestone.

## Risks specific to this plan

- **Offline vs. maps.** The plan says fully offline, but MapLibre needs basemap tiles. Recommend a bundled low-zoom PMTiles basemap, with optional user-supplied regional PMTiles. Decide in 0.8.
- **Licensing on a public repo.** LibRaw (LGPL/CDDL) and ExifTool (Perl Artistic/GPL) constrain how they can be bundled. Keep both as separate dynamically-linked or sidecar components.
- **Two-week Phase 0 can slip on Spike A.** It is timeboxed at 4 days; if unresolved, take the plan's fallback and move on. It does not block any Phase 1 milestone.
- **Scope creep.** The tiers are strict: nothing from Horizon enters Phase 1.

## Verification

- Phase 0: each tool prints a version; census script runs on a small folder and produces `docs/census.md`; spike results are written down with pass/fail.
- Phase 1, per milestone: `cargo test --workspace`, `pnpm test`, and CI green on 3 OS; criterion benchmarks for the scanner and thumbnails; a scripted 100k synthetic-library run for grid FPS; manual smoke via `cargo tauri dev` on the real library.
- v0.1: install the release build on a clean Windows profile and walk the plan's opening sequence (Light Table → Library → Loupe → `R` flip).

## First actions on approval

1. Save this plan into the repo as `docs/roadmap-phase0-1.md` so it survives beyond this session.
2. Rewrite "Next up" in `PROJECT_STATE.md` from Phase 0 tasks 0.1–0.8.
3. Start 0.1 (toolchain install). Installing system software and the public-repo setup will each get confirmed with you before I run them.
