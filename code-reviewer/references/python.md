## Python Review Checklist

### Security 🔒
- [ ] 🔴 SQL injection — string-formatting queries instead of parameterized: `f"SELECT * FROM users WHERE id = {user_id}"` 
- [ ] 🔴 Command injection — passing user input to `subprocess`, `os.system`, or `eval`
- [ ] 🔴 Unsafe `pickle` — deserializing untrusted data; `pickle.loads` executes arbitrary code
- [ ] 🔴 Path traversal — `open(user_input)` without normalizing and validating the path stays within an allowed root
- [ ] 🟠 Unsafe deserialization — `yaml.load(data)` (use `yaml.safe_load`) or trusting JSON structure without schema validation
- [ ] 🟠 Insecure cryptography — `md5`/`sha1` for passwords; use `bcrypt`/`argon2`; `random` instead of `secrets` for tokens
- [ ] 🟠 Missing input validation — user-supplied values used directly without type or range checks
- [ ] 🔵 Dependency vulnerabilities — newly added packages with known CVEs

### Performance ⚡
- [ ] 🟠 N+1 queries — fetching related records inside a loop instead of a single query with `select_related`/`prefetch_related` or a JOIN
- [ ] 🟠 Blocking I/O in async code — `time.sleep`, synchronous file reads, or `requests` calls inside `async def`
- [ ] 🟡 Inefficient string building — concatenating strings in a loop (`s += chunk`) instead of `"".join(chunks)`
- [ ] 🟡 Missing generators for large datasets — loading an entire result set into memory when `yield` would do
- [ ] 🟡 Duplicate computations — the same expensive call made multiple times where a local variable or `functools.cache` would suffice

### Architecture 🏗️
- [ ] 🟠 Circular imports — module A imports from B which imports from A; restructure or use a lazy import
- [ ] 🟠 Tight coupling — a function importing and calling a concrete implementation it should receive as a parameter
- [ ] 🟠 Missing type hints on public functions and class methods
- [ ] 🟡 Single responsibility violated — a function that fetches data, transforms it, *and* writes output
- [ ] 🟡 Bare `except:` or `except Exception:` masking real failures — catch specific exception types
- [ ] 🔵 Missing context managers for resources — `open()` without `with`, database connections not closed on error

### Tests 🧪
- [ ] 🟠 New logic with no test coverage
- [ ] 🟡 Mocks that assert nothing — `patch` applied but never called with `.assert_called_with(...)` or similar
- [ ] 🟡 Tests that depend on execution order — no test should rely on state left by a previous test; use `setUp`/`tearDown` or fixtures to isolate
- [ ] 🔵 Fixtures or `setUp` doing too much — shared setup that obscures what a specific test actually needs
