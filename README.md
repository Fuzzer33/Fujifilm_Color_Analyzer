# Latent

A recipe-aware photo library for Fujifilm shooters. Local, offline, and read-only toward your originals.

> **Status: early development.** Nothing is released yet. Latent is a working title. See [`docs/roadmap-phase0-1.md`](docs/roadmap-phase0-1.md) for the current plan and [`latent-plan.md`](latent-plan.md) for the full design.

## What it is

- A fast, local way to browse thousands of Fujifilm photos by what makes Fuji photos different: the recipe (film simulation and settings).
- A place to understand color: what a recipe does and why a photo looks the way it does.
- Later, a bridge to your camera: preview recipes, render with the camera's own processor, and write recipes to custom slots.

## What it is not

- Not an editor, not a cloud service, and not a general photo manager. It is Fujifilm-only, and files from other cameras are skipped and counted.
- It never writes to your RAF or JPEG files. All state lives in its own database and cache.

## Stack

Tauri 2 (Rust core, React + TypeScript frontend), SQLite, ExifTool (bundled sidecar), LibRaw.

## Development

Requires Rust (version pinned in `rust-toolchain.toml`), Node (see `.nvmrc`), pnpm, ExifTool, and on Windows the Visual Studio C++ Build Tools. There is no app to run yet; the scaffold lands in Phase 1.

The library location is configured with the `LATENT_LIBRARY` environment variable and is never hard-coded.

## Privacy

Photos, location data and personal paths must never be committed. `.gitignore` blocks common photo and location formats, except JPEG screenshots under `docs/` and the synthetic fixtures in `fixtures/public/`.

## License

[MIT](LICENSE)
