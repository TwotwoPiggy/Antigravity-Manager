# Codebase Structure

**Analysis Date:** 2026-04-28

## Directory Layout

```
D:\Computers\AI Develop\Tools\Antigravity-Manager/
├── Casks/              # Homebrew Cask definitions for macOS
├── deploy/             # Deployment and packaging scripts (e.g., Arch Linux)
├── docker/             # Dockerfiles and docker-compose configurations
├── docs/               # Documentation and images
├── scripts/            # Helper scripts for build/dev
├── src/                # React frontend source code
│   ├── assets/         # Static assets (images, icons)
│   ├── components/     # Reusable React components (Dashboard, Settings, etc.)
│   ├── config/         # Frontend configuration constants
│   ├── hooks/          # Custom React hooks
│   ├── locales/        # i18n translation files
│   ├── pages/          # Top-level route components (Accounts, ApiProxy)
│   ├── services/       # API clients for Tauri IPC / HTTP bridging
│   ├── stores/         # Zustand state management
│   ├── types/          # TypeScript interface definitions
│   └── utils/          # Frontend utility functions
├── src-tauri/          # Rust backend source code
│   ├── capabilities/   # Tauri capabilities configuration
│   ├── icons/          # Application icons
│   └── src/            # Rust source files
│       ├── commands/   # Tauri IPC command handlers
│       ├── models/     # Shared data structures and database models
│       ├── modules/    # Core business logic (account, config, db, oauth)
│       ├── proxy/      # API Proxy Server engine
│       └── utils/      # Rust utility functions
└── web_site/           # Landing page or marketing website
```

## Directory Purposes

**`src/components/`:**
- Purpose: UI building blocks.
- Contains: `accounts/`, `dashboard/`, `navbar/`, `settings/`, etc.
- Key files: `UpdateNotification.tsx`

**`src/stores/`:**
- Purpose: Global state management.
- Contains: Zustand hooks.
- Key files: `useAccountStore.ts`, `useConfigStore.ts`, `useViewStore.ts`

**`src-tauri/src/commands/`:**
- Purpose: The bridge between frontend and backend.
- Contains: Functions annotated with `#[tauri::command]`.
- Key files: `proxy.rs`, `user_token.rs`, `security.rs`

**`src-tauri/src/proxy/`:**
- Purpose: The core HTTP reverse proxy engine for AI API routing.
- Contains: The Axum server, middleware, handlers, and protocol mappers.
- Key files: `server.rs` (Axum setup), `token_manager.rs` (Account routing/limits)

**`src-tauri/src/proxy/handlers/`:**
- Purpose: HTTP endpoint controllers for specific AI protocols.
- Contains: Implementations for routing and extracting request data.
- Key files: `claude.rs`, `openai.rs`, `gemini.rs`

**`src-tauri/src/proxy/mappers/`:**
- Purpose: Translates payloads between standardized formats (OpenAI/Claude) and the upstream (Google) format.
- Contains: Data transformation logic.

## Key File Locations

**Entry Points:**
- Frontend: `src/main.tsx`
- Tauri App: `src-tauri/src/lib.rs` (Initializes plugins, logger, state, and spawns the Axum server)

**Configuration:**
- Frontend build: `vite.config.ts`, `tailwind.config.js`
- Tauri build: `src-tauri/tauri.conf.json`
- Rust dependencies: `src-tauri/Cargo.toml`

**Core Logic:**
- Proxy Server: `src-tauri/src/proxy/server.rs`
- Token Routing: `src-tauri/src/proxy/token_manager.rs`
- Protocol Conversion: `src-tauri/src/proxy/mappers/`

## Naming Conventions

**Files:**
- React Components: PascalCase (`Dashboard.tsx`, `UpdateNotification.tsx`)
- TypeScript Utilities/Hooks: camelCase (`useAccountStore.ts`, `env.ts`)
- Rust Modules: snake_case (`token_manager.rs`, `server.rs`)

**Directories:**
- Frontend: camelCase or lowercase descriptive names (`components`, `stores`)
- Backend: snake_case (`proxy_pool`, `mappers`)

## Where to Add New Code

**New Frontend Page:**
- Implementation: `src/pages/`
- Add corresponding route in `src/App.tsx`.

**New UI Component:**
- Implementation: `src/components/[category]/`

**New Tauri IPC Command:**
- Implementation: `src-tauri/src/commands/`
- Registration: Add to the `invoke_handler` in `src-tauri/src/lib.rs`.

**New AI Protocol Support:**
- Handler: `src-tauri/src/proxy/handlers/`
- Mapper: `src-tauri/src/proxy/mappers/`
- Routing: Register the new endpoint in the Axum router inside `src-tauri/src/proxy/server.rs`.

**New Database/Storage Logic:**
- Implementation: `src-tauri/src/modules/` (e.g., `proxy_db.rs`, `security_db.rs`).

## Special Directories

**`docker/`:**
- Purpose: Docker deployment files for running the proxy in Headless mode on NAS or servers.
- Generated: No
- Committed: Yes

**`Casks/`:**
- Purpose: Homebrew formula for macOS installation via `brew install --cask`.
- Generated: No
- Committed: Yes

**`.planning/`:**
- Purpose: GSD workflow planning, execution, and documentation artifacts.
- Generated: Partially (by agents)
- Committed: Yes

---

*Structure analysis: 2026-04-28*