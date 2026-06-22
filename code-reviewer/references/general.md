## General Review Checklist

These checks apply to **every diff**, regardless of language or file type.

### Code Hygiene 🧹
- [ ] 🟡 Commented-out code left in
- [ ] 🟠 Debug statements committed — `console.log`, `print`, `debugger`, `pry`, etc.
- [ ] 🔵 `TODO`/`FIXME` without a ticket reference — e.g. `# TODO: fix this` with no link
- [ ] 🔵 Comments that narrate the code — e.g. `// increment counter` above `i++`

### Secrets & Configuration 🔑
- [ ] 🔴 Hardcoded secrets, API keys, tokens, or passwords
- [ ] 🟠 Hardcoded environment-specific values that should be in config — e.g. `const API_URL = "https://prod.example.com"`
- [ ] 🔴 Credentials or PII visible in logs — e.g. `logger.info("Login attempt", { password })`

### Code Quality 🏗️
- [ ] 🟡 Magic numbers or strings — e.g. `if (status === 3)` with no named constant explaining what `3` means
- [ ] 🟠 Functions or files that do too much — flag if a function exceeds ~50 lines or handles multiple distinct concerns
- [ ] 🟡 Dead code — unreachable branches, unused variables, unused imports
- [ ] 🟡 Copy-pasted logic — same block appearing 2+ times that could be a shared utility
- [ ] 🔵 Inconsistent naming — e.g. `getUserData` alongside `fetch_user_record` in the same codebase

### Error Handling 🚨
- [ ] 🔴 Errors silently swallowed — e.g. `catch (_) {}` or bare `except: pass`
- [ ] 🟠 Missing error handling at I/O or network boundaries — unchecked file reads, unhandled promise rejections
- [ ] 🟠 Overly broad exception catching — e.g. `except Exception` hiding real failures instead of catching specific types

### Reliability & Resilience 🛡️
- [ ] 🟠 Missing timeout on I/O, network calls, or external dependencies — a hanging call with no deadline
- [ ] 🟡 No retry logic for transient failures — one-shot calls to flaky external services
- [ ] 🟠 Single points of failure with no fallback — a critical path with no degraded-mode behaviour
- [ ] 🟠 Missing logging at failure points — errors caught but not recorded anywhere

### Tests 🧪
- [ ] 🟠 New logic with no test coverage
- [ ] 🟡 Tests that only assert the happy path — no error or edge cases covered
- [ ] 🟡 Tests coupled to implementation details — asserting internal state or private methods instead of observable behaviour; breaks on refactor without behaviour changing
- [ ] 🔵 Test names that don't describe the expected behaviour — e.g. `test_function_1` instead of `test_returns_404_when_user_not_found`
