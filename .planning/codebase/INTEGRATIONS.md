# External Integrations

**Analysis Date:** 2026-04-28

## APIs & External Services

**AI Model Providers:**
- Google Cloud / Gemini API - Primary AI models routing
  - Endpoints used: `cloudcode-pa.googleapis.com/v1internal`, `generativelanguage.googleapis.com`
  - Auth: OAuth Access Tokens
  - Integration method: REST API / SSE Streams
- Anthropic API / z.ai - Model fallbacks and proxying
  - Endpoints used: `api.z.ai`, `api.anthropic.com`
- OpenAI API - Codex and compatible endpoints

**Authentication & Identity:**
- Google OAuth 2.0 - Account authorization
  - Endpoints used: `accounts.google.com/o/oauth2/v2/auth`, `oauthoauth2.googleapis.com/token`
  - Integration method: Local HTTP callback / manual code entry
  - Scopes: `cloud-platform`, `userinfo.email`, `userinfo.profile`, etc.

**Updates & Releases:**
- GitHub API - Auto-updater and release fetching
  - Integration method: REST API (`api.github.com/repos/.../releases/latest`)

**Tunneling / Networking:**
- Cloudflare - Built-in tunnel for public exposure
  - Service: `cloudflared` binary integration

## Data Storage

**Databases:**
- Local SQLite - User data, tokens, and traffic logs
  - Client: `rusqlite`
  - Connection: Local `.vscdb` / SQLite files

**File Storage:**
- Local Filesystem - Config and state storage (`~/.antigravity_tools/`)
  - Format: JSON (`gui_config.json`, `accounts.json`)

**Caching:**
- In-Memory Cache - DashMap for token management and proxy pools

## Authentication & Identity

**Auth Provider:**
- Custom Token Auth - Web Admin / Proxy access
  - Auth: API Keys (`ABV_API_KEY`) and Web Passwords (`ABV_WEB_PASSWORD`)
  - Integration method: Custom Axum middleware

## Monitoring & Observability

**Logs:**
- Local File Logging - Backend debugging
  - Implementation: `tracing` and `tracing-appender`
  - Destination: `app.log`
- Traffic Monitor - In-app SQLite request/response tracking

## CI/CD & Deployment

**CI Pipeline:**
- GitHub Actions - Build and release
  - Workflows: Cross-platform binaries, AppImage generation, Docker image push

**Hosting:**
- Local Desktop / Self-hosted Docker - User environments

## Environment Configuration

**Production / Docker Headless:**
- Required env vars: `ABV_API_KEY` (Auth), `ABV_WEB_PASSWORD` (Web Admin)
- Optional env vars: `ABV_AUTH_MODE`, `ABV_BIND_LOCAL_ONLY`, `ABV_DATA_DIR`, `ABV_MAX_BODY_SIZE`
- Secrets location: Docker environment variables / `gui_config.json`

## Webhooks & Callbacks

**Incoming:**
- OAuth Callback - `http://127.0.0.1:xxx/callback`
  - Events: Google OAuth token exchange

**Outgoing:**
- None

---

*Integration audit: 2026-04-28*
