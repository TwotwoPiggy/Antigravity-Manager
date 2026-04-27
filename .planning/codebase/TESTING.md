# Testing Patterns

**Analysis Date:** 2026-04-28

## Test Framework

**Runner:**
- Backend: Native Rust `cargo test`.
- Frontend: No dedicated frontend test files (`*.test.ts`, `*.spec.ts`) detected in the repository.

**Run Commands:**
```bash
cargo test              # Run all backend tests
rtk cargo test          # Run tests with RTK token filtering (recommended)
```

## Test File Organization

**Location:**
- **Co-located Tests:** Unit tests for specific utilities are placed at the bottom of the source file inside a `tests` module.
  - Example: `src-tauri/src/utils/crypto.rs`
- **Integration/Feature Tests:** Larger test suites are placed in dedicated test directories.
  - Example: `src-tauri/src/proxy/tests/` contains `ultra_priority_tests.rs`, `security_integration_tests.rs`, `rate_limit_404_tests.rs`, etc.

**Structure:**
```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_feature_name() {
        // test logic
    }
}
```

## Test Structure

**Patterns:**
- Tests typically follow an Arrange-Act-Assert pattern.
- `assert!`, `assert_eq!`, and `assert_ne!` macros are standard for verification.

```rust
#[test]
fn test_encrypt_decrypt_cycle() {
    let password = "my_secret_password";
    let encrypted = encrypt_string(password).unwrap();
    
    assert!(encrypted.starts_with(ENCRYPTED_PREFIX));
    assert_ne!(password, encrypted);

    let decrypted = decrypt_string(&encrypted).unwrap();
    assert_eq!(password, decrypted);
}
```

## Mocking

**What to Mock:**
- External API endpoints and network requests are mocked or bypassed in unit tests to ensure fast and deterministic execution.
- Tests in `proxy/tests/` verify routing, priority, and rate limiting logic without making actual upstream calls.

## Test Types

**Unit Tests:**
- Heavily utilized in Rust backend for utilities (e.g., crypto, formatting) and isolated proxy logic.

**Integration Tests:**
- Located in `proxy/tests/comprehensive.rs` and `security_integration_tests.rs`, testing interaction between proxy layers, token managers, and rate limiters.

**Frontend Tests:**
- Not currently enforced or detected. E2E or component tests are missing.

---

*Testing analysis: 2026-04-28*