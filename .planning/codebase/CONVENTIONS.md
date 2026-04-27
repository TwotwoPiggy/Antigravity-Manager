# Coding Conventions

**Analysis Date:** 2026-04-28

## Naming Patterns

**Files:**
- Frontend (React/TS): PascalCase for React components (e.g., `src/components/navbar/Navbar.tsx`), camelCase for hooks and utilities (e.g., `src/hooks/useProxyModels.tsx`, `src/utils/format.ts`).
- Backend (Rust): snake_case for all modules and files (e.g., `src-tauri/src/proxy/token_manager.rs`, `src-tauri/src/utils/crypto.rs`).

**Functions:**
- Frontend: camelCase (e.g., `formatRelativeTime`).
- Backend: snake_case (e.g., `get_encryption_key`).

**Variables:**
- Frontend: camelCase.
- Backend: snake_case.

**Types/Interfaces/Structs:**
- Frontend & Backend: PascalCase (e.g., `ProxyConfig` in Rust, `Account` in TS).

## Code Style

**Formatting:**
- Rust: `cargo fmt` is used and enforced in CI (`cargo fmt -- --check`).
- Frontend: Prettier/ESLint are typically used, but configuration relies on standard ecosystem defaults or package.json scripts.

**Linting:**
- Rust: `clippy` is enforced in CI (`cargo clippy --all-targets --all-features -- -D warnings`).
- TypeScript: `strict: true` is enabled in `tsconfig.json`, along with `noUnusedLocals` and `noUnusedParameters`.

## Import Organization

**Order:**
1. Standard library imports
2. Third-party crate/package imports
3. Internal module imports

## Error Handling

**Patterns:**
- Rust: Extensive use of `Result<T, E>`. Custom errors are defined using `thiserror` (e.g., `thiserror = "2.0.17"` in `Cargo.toml`), and `anyhow` is used for application-level error bubbling.
- Error responses are standardized for API endpoints, mapping internal errors to proper HTTP status codes.

## Logging

**Framework:**
- Rust: `tracing` ecosystem is heavily utilized (`tracing`, `tracing-subscriber`, `tracing-appender`, `tracing-log`).

**Patterns:**
- `INFO` level is reserved for key business events (e.g., startup, high-level request summaries).
- `DEBUG` and `TRACE` are used for high-frequency or detailed logs (e.g., tool calls, payload details).
- Log files are persisted and automatically rotated (e.g., `app.log`).

## Comments

**When to Comment:**
- Feature tags and issue references are often included in comments to denote fixes (e.g., `// [FIX #1738] 防止双重加密`).

**Documentation:**
- Rust: `///` is used for function-level documentation (e.g., `/// 生成加密密钥 (基于设备 ID)`).

## Function Design

**Return Values:**
- Rust: Predominantly return `Result<T, E>` for operations that can fail, utilizing `?` operator for propagation.

## Module Design

**Structure:**
- Backend code is well-segregated into logical layers: `commands` (Tauri IPC), `proxy` (core API handling/routing), `modules` (domain services/DB), and `utils`.

---

*Convention analysis: 2026-04-28*