# Project state

Last updated: 2026-09-18

## Where we are

**Phase 0: Census** (target: late September 2026). Only `latent-plan.md` exists so far. No code, no scaffold.

Roadmap: P0 Census (Sep) → P1 Foundation, ship v0.1 (Oct–Dec) → P2 Signature, ship v0.5 portfolio build (Jan–Mar) → P3 Research (Mar–May) → P4 Release, v1.0 (May–Jun).

## Done

- 2026-09-18: Wrote plan v1.0 (`latent-plan.md`).
- 2026-09-18: Set up session-memory system (`CLAUDE.md`, this file, `/wrap-up`).
- 2026-09-18: **Task 0.2 done (local).** Added MIT `LICENSE`, `README.md`, privacy-focused `.gitignore` (verified with `git check-ignore`), `.github/workflows/ci.yml` (3-OS skeleton, conditional on Cargo/package.json). CI has not run yet; there is no GitHub remote.
- 2026-09-18: **Task 0.1 done.** Installed VS Build Tools 17.14, Rust 1.98.1 (pinned in `rust-toolchain.toml`), Node 24.19.0 (`.nvmrc`), pnpm 12.4.2, ExifTool 13.59, tauri-cli 2.11.4. New terminals/Claude Code sessions need a restart to pick up PATH.
- 2026-09-18: Planned Phase 0 + Phase 1 (`docs/roadmap-phase0-1.md`), approved by user.

## In progress

- Nothing yet.

## Next up (in order)

Full detail in `docs/roadmap-phase0-1.md` (Phase 0 → Phase 1 M1–M8, ships public v0.1 by mid-Dec 2026).

1. Commit everything so far (all untracked; user has not yet asked for a commit). Then push to a GitHub remote (none configured yet) so CI can run; repo is public, so confirm before pushing.
3. **0.3** Census script (`tools/census/`, library path via `LATENT_LIBRARY`); commit only aggregate `docs/census.md`.
4. **0.4** Spike A: X-T4 camera render over USB (4-day timebox). **Gate:** if it fails, Lab ships without camera render.
5. **0.5** Spike B: WebView2 color management (2 days). Fail → Rust-side managed thumbnails.
6. **0.6** Spike C: RAF embedded-JPEG read timing. **0.7** Fixture strategy. **0.8** Name, license, offline basemap.
7. Then Phase 1 **M1 Scaffold** (Tauri 2 + React/TS + Rust workspace).

## Decisions made

- Strict neutral darkroom UI. Photos are the only color; safelight amber only for the camera link.
- Light film vocabulary: rolls, contact sheets, edge strip only.
- Fujifilm only. Other cameras are skipped and counted.
- Stack: Tauri 2, React + TS, Rust core, SQLite (WAL, R*Tree, FTS5), LibRaw, `nusb`, `ort` + DirectML, bundled ExifTool sidecar. Training is off-device in PyTorch, exported to ONNX.

- License: **MIT**, copyright holder set to git handle "Fuzzer33" (change to legal name if preferred).
- Repo is **public from day one** (decided 2026-09-18). No real photos, paths or GPS in commits.
- Scope of current plan: Phase 0 + Phase 1 through public v0.1. Library location is configurable (`LATENT_LIBRARY`), not hard-coded.

## Open questions

- Name: "Latent" is a working title and needs a trademark and app-name check (task 0.8).
- LibRaw (LGPL/CDDL) and ExifTool (Artistic/GPL) bundling terms still to verify against MIT before v0.1 (0.8). License itself is decided: MIT.
- Atlas basemap: plan says offline, MapLibre needs tiles. Recommended: bundled low-zoom PMTiles + optional regional PMTiles (0.8).
- Library location and size: user will provide later.

## Blockers / risks to watch

- X-T4 camera rendering may not be scriptable (decided at Phase 0).
- WebView2 color management may be incomplete.

## Session log (last 5)

- 2026-09-18: Trimmed MCP/plugin bloat, created this state system, planned Phase 0+1, installed the toolchain (0.1), MIT license + repo hygiene files (0.2). Next: commit, then 0.3 census script.
