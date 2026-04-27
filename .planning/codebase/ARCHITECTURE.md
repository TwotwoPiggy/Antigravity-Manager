# Architecture

**Analysis Date:** 2026-04-28

## Pattern Overview

**Overall:** Tauri Desktop Application with Built-in HTTP Reverse Proxy

**Key Characteristics:**
- **Hybrid Architecture:** React frontend for configuration/monitoring and a high-performance Rust backend handling heavy lifting and networking.
- **Embedded Proxy Server:** Runs an Axum HTTP server (port 8045) internally to serve as an AI API gateway, supporting a Headless mode for Docker deployments.
- **Multi-Protocol Translation:** Acts as a universal adapter, converting incoming requests in OpenAI, Anthropic (Claude), or Gemini Native formats into requests for Google/Vertex AI.
- **Intelligent Routing & Failover:** Incorporates advanced token management, adaptive circuit breaking, and smart account rotation to handle rate limits and capacity issues.

## Layers

**Frontend (React/Vite):**
- Purpose: UI for account management, proxy settings, security rules, and real-time monitoring.
- Location: `src/`
- Contains: React components, pages, Zustand stores, i18n configurations.
- Depends on: Tauri IPC API for system-level operations, HTTP API for headless mode.

**Tauri Command Bridge (IPC):**
- Purpose: Connects the React frontend to the Rust backend logic.
- Location: `src-tauri/src/commands/`
- Contains: `#[tauri::command]` functions exposing configuration, account, and proxy management.
- Used by: `src/services/` in the frontend.

**Proxy Server (Axum):**
- Purpose: HTTP gateway handling external client requests (e.g., from Cursor, Claude CLI).
- Location: `src-tauri/src/proxy/server.rs`
- Contains: Routing setup, middleware orchestration, and HTTP listeners.

**Proxy Handlers & Mappers:**
- Purpose: Endpoint-specific request handling and protocol conversion.
- Location: `src-tauri/src/proxy/handlers/` and `src-tauri/src/proxy/mappers/`
- Contains: Protocol-specific logic (`openai.rs`, `claude.rs`, `gemini.rs`) and data mapping.

**Core Services (Modules):**
- Purpose: Database management, OAuth flows, configuration, and security.
- Location: `src-tauri/src/modules/`
- Contains: `account.rs`, `security_db.rs`, `oauth.rs`, `config.rs`.

## Data Flow

**AI Request Proxy Flow:**

1. **Client Request:** An external client (e.g., Claude Code) sends a request to `http://127.0.0.1:8045/v1/messages`.
2. **Middleware:** Request passes through Axum middleware (`ip_filter`, `auth`, `monitor`) for security and logging.
3. **Handler:** `handlers::claude::handle_messages` receives the request.
4. **Token & Routing:** `TokenManager` selects the best available account based on quota, health score, and model routing rules.
5. **Mapper:** `mappers::claude::wrap_request` converts the Claude format to the Gemini upstream format.
6. **Upstream:** `UpstreamClient` sends the request to Google/Vertex AI APIs.
7. **Response Mapper:** Upstream response (SSE/JSON) is parsed, and `mappers::claude` converts it back to the Claude format for the client.

**State Management:**
- **Frontend:** Zustand is used for global state (`useAccountStore.ts`, `useConfigStore.ts`).
- **Backend:** `AppState` uses `Arc<RwLock<T>>` to share configuration (`ProxyConfig`, `TokenManager`, `SecurityConfig`) across the Axum server and Tauri commands.

## Key Abstractions

**TokenManager:**
- Purpose: Centralized management of account pools, intelligent routing, quota tracking, and rate limit isolation.
- Examples: `src-tauri/src/proxy/token_manager.rs`
- Pattern: Thread-safe singleton pattern using `Arc<RwLock<T>>`.

**UpstreamClient:**
- Purpose: Handles HTTP connections to upstream AI providers, incorporating JA3 fingerprinting and custom User-Agent spoofing to bypass WAFs.
- Examples: `src-tauri/src/proxy/upstream/client.rs`

**SignatureCache:**
- Purpose: Persists "thought signatures" across multi-turn conversations for Claude thinking models to prevent 400 errors.
- Examples: `src-tauri/src/proxy/signature_cache.rs`

## Entry Points

**Frontend Application:**
- Location: `src/main.tsx`
- Triggers: Application launch by the user.
- Responsibilities: Bootstraps React, applies global CSS, mounts the app.

**Backend Desktop (Tauri):**
- Location: `src-tauri/src/main.rs` & `src-tauri/src/lib.rs`
- Triggers: OS execution.
- Responsibilities: Initializes Tauri plugins, registers IPC commands, and starts the background Axum server.

**Backend Headless (Docker/Server):**
- Location: `src-tauri/src/lib.rs` (Headless branch in `run()`)
- Triggers: Execution with `--headless` flag.
- Responsibilities: Bypasses Tauri window creation, sets up Tokio runtime, serves static frontend files, and starts the Axum proxy server.

## Error Handling

**Strategy:** Adaptive circuit breaking, intelligent backoff, and graceful degradation.

**Patterns:**
- **Rate Limit Tracking:** Detects 429/503 errors and applies model-level or account-level lockouts.
- **Account Rotation:** On upstream failure (e.g., Quota Exhausted), the system seamlessly retries the request using the next available account without breaking the client connection.
- **Fallback Endpoints:** Automatically switches between `prod`, `daily`, and `sandbox` endpoints when upstream services are unstable.

## Cross-Cutting Concerns

**Logging:**
- Handled by `tracing` crate. Logs are captured via `debug_logger.rs` and bridged to the frontend using Tauri events or HTTP endpoints.
- High-volume logs are persisted to SQLite databases (`proxy_db.rs`, `security_db.rs`).

**Validation & Cleansing:**
- JSON Schemas for tool calls are recursively flattened and cleaned to ensure compatibility with strict upstream APIs (e.g., removing `anyOf`, `const`).

**Authentication:**
- Proxy endpoints are secured by an API Key, handled by `auth_middleware`.
- Admin endpoints can be secured by an independent `WEB_PASSWORD`.

---

*Architecture analysis: 2026-04-28*