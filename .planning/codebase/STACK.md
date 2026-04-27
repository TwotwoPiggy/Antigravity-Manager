# Technology Stack

**Analysis Date:** 2026-04-28

## Languages

**Primary:**
- Rust 2021 edition - Backend application code and HTTP proxy core
- TypeScript ~5.8.3 - Frontend UI and client logic

**Secondary:**
- CSS/Tailwind - UI styling
- Shell/PowerShell - Installation and build scripts

## Runtime

**Environment:**
- Desktop App: Tauri v2 environment
- Headless Mode: Docker/Linux Server natively supported
- Node.js (build time only)

**Package Manager:**
- Cargo - Rust dependencies
- npm - Node.js dependencies
- Lockfile: `Cargo.lock` and `package-lock.json` (presumed)

## Frameworks

**Core:**
- Tauri v2 - Desktop application framework
- React 19.x - UI framework
- Axum 0.7 - Backend HTTP server/proxy framework
- React Router 7.x - Frontend routing

**Testing:**
- Rust built-in `cargo test` - Backend unit testing

**Build/Dev:**
- Vite 7.x - Frontend bundler
- tauri-build 2.x - Tauri compilation

## Key Dependencies

**Critical:**
- `tokio` (Rust) - Async runtime for high concurrency proxy
- `rquest` & `reqwest` (Rust) - HTTP clients with JA3 fingerprinting camouflage
- `rusqlite` (Rust) - Local SQLite database operations
- `zustand` (TypeScript) - Frontend state management

**Infrastructure:**
- `serde` / `serde_json` (Rust) - Serialization and JSON parsing
- `tailwindcss` / `antd` / `daisyui` (TypeScript) - UI component libraries
- `i18next` (TypeScript) - Internationalization

## Configuration

**Environment:**
- Environment variables (`ABV_API_KEY`, `ABV_WEB_PASSWORD`, `ABV_AUTH_MODE`, `ABV_MAX_BODY_SIZE`, etc.)
- Configuration files (`gui_config.json`, `accounts.json`, `.antigravity_tools/`)

**Build:**
- `Cargo.toml` - Rust workspace config
- `vite.config.ts`, `tsconfig.json` - Frontend build config
- `tailwind.config.js`, `postcss.config.cjs` - Styling config

## Platform Requirements

**Development:**
- Rust toolchain
- Node.js environment
- Tauri v2 prerequisites (OS specific build tools)

**Production:**
- Desktop: macOS (.dmg), Windows (.msi/.zip), Linux (.deb/AppImage)
- Server: Docker container (Headless mode)

---

*Stack analysis: 2026-04-28*
