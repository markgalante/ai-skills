## JavaScript / TypeScript Review Checklist

### Security 🔒
- [ ] 🔴 XSS via `dangerouslySetInnerHTML` — passing unsanitized user input: `<div dangerouslySetInnerHTML={{ __html: userInput }} />`
- [ ] 🔴 Unsafe DOM manipulation — `element.innerHTML = userValue` without sanitization
- [ ] 🔴 Missing input validation/sanitization before using user-supplied data in queries, commands, or HTML
- [ ] 🟠 Insecure regex (ReDoS) — patterns like `/(a+)+$/` applied to user input cause exponential backtracking
- [ ] 🟠 Missing CORS validation on server-side routes that accept cross-origin requests
- [ ] 🟠 CSRF token not attached to state-mutating requests
- [ ] 🟠 Unsafe deserialization — `JSON.parse` on untrusted input without schema validation
- [ ] 🔵 Dependency vulnerabilities — newly added packages with known CVEs

### Performance ⚡
- [ ] 🔴 Memory leaks — event listeners or subscriptions added without a corresponding cleanup: `addEventListener` with no `removeEventListener`, `setInterval` with no `clearInterval`
- [ ] 🟠 Blocking the main thread — synchronous operations (`fs.readFileSync`, `JSON.parse` on large payloads) where async alternatives exist
- [ ] 🟠 Race conditions in async code — concurrent mutations to shared state without coordination
- [ ] 🟠 Inefficient algorithms — O(n²) loop-in-loop over large arrays where a Map/Set lookup would be O(n)
- [ ] 🟡 Large bundle size impact — importing an entire library for one utility (`import _ from 'lodash'` instead of `import {debounce} from 'lodash'`)
- [ ] 🟡 DOM/layout thrashing — interleaving reads and writes to the DOM in a loop, forcing repeated reflows
- [ ] 🟡 _(React)_ Unnecessary re-renders — state or props changing on every render cycle without `memo`/`useMemo`/`useCallback`
- [ ] 🟡 _(React)_ Missing `key` prop on list items, or using array index as `key` for dynamic lists
- [ ] 🔵 _(React)_ Expensive computation inside render with no memoization

### Tests 🧪
- [ ] 🟠 New logic with no test coverage
- [ ] 🟡 Untested async paths — promise rejections or error branches not covered
- [ ] 🟡 Snapshot tests used as a substitute for assertions — a snapshot that always passes on update catches nothing meaningful
- [ ] 🟡 _(React)_ State updates not wrapped in `act()` — causes warnings and unreliable test behaviour
- [ ] 🔵 Mocks that are never asserted — `jest.fn()` set up but `.toHaveBeenCalledWith(...)` never checked

### Architecture 🏗️
- [ ] 🟠 Circular dependencies — module A imports B which imports A; use a barrel or invert the dependency
- [ ] 🟠 Tight coupling — a component or function reaching directly into another module's internals instead of its public interface
- [ ] 🟠 Missing TypeScript types — `any` used where a proper type is knowable; untyped function parameters
- [ ] 🟡 Single responsibility violated — a component or function handling data fetching, transformation, *and* rendering
- [ ] 🟡 Prop drilling more than 2–3 levels deep — consider context or a state management pattern
- [ ] 🔵 Module organization — logic that belongs in a shared utility living inside a component file
