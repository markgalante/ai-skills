# Worked example

A real session that landed well. Annotations in `>` blocks call out what each move is doing.
The topic doesn't matter — the shape does.

---

**User:** *(with `turbo.json` line 6 selected)* again, simply and with shortened chunks, explain
this change with `$TURBO_DEFAULTS$`.

**Assistant:**

Quick note first: the token is `$TURBO_DEFAULT$`, singular — no `S`. Easy to get wrong, and Turbo
won't warn you, it'll just treat it as a filename that doesn't exist.

> Wrong premise corrected before anything else. One sentence, plus *why it matters* — a silent
> failure is worth the extra clause. Then straight into the explanation; no lecture.

---

## Chunk 1 of 3 — What we had before

Before, the `types` list said, roughly: *"hash the `.ts` and `.tsx` files under `src/`."*

That was a list **I wrote by hand**. And a hand-written list is a guess about which files matter.
My guess was wrong in three places:

- `.js` files — the base tsconfig sets `allowJs: true`, so `tsc` compiles those too
- `.json` files — `resolveJsonModule: true`, same story
- `.jest/setup.ts` — a `.ts` file, but it lives outside `src/`, so the glob missed it

Each one is a file that can change the type-check result while leaving the hash unchanged. Same bug
as the eslintrc, just a different missing file.

Check: clear that the problem was me hand-writing a list that turned out to be narrower than what
`tsc` actually reads?

> Count declared up front. Real tokens and paths, no hand-waving. Callback to the eslintrc they
> already understand instead of a new analogy. The check names the specific claim — they can
> disagree with a *part* of it.

---

**User:** Okay, so the *old* list would've ignored `.js` files, `.json` files, and `.jest/setup.ts`?

**Assistant:**

Yes — with one word swapped, because it's the same two-programs distinction from before.

**Turbo** ignored them. **tsc** did not. `tsc` was compiling those `.js` and `.json` files the whole
time and would happily report errors in them. Turbo just wasn't watching them, so it never knew to
re-run `tsc` and let it look.

So the file could break, and Turbo would say "nothing changed" and replay a pass from before it broke.

> Verdict first. Their summary was *nearly* right, so the fix is one swapped word — not a re-run of
> the chunk. Then back to the loop without restarting.

---

## Chunk 2 of 3 — What `$TURBO_DEFAULT$` does

Instead of me listing which files matter, `$TURBO_DEFAULT$` tells Turbo:

> "Hash **everything in this package** that git tracks."

No list to write, so no list to get wrong. Every `.ts`, `.js`, `.json`, setup file, and anything
added next year is included automatically.

The trade is that it's deliberately over-inclusive — editing the package's README now busts the
type-check cache, even though a README can't affect types. You pay a bit of wasted CPU to remove a
whole category of "we forgot to list that file" bugs. That's the same trade you already accepted
for `index.js`.

Check: does "hash everything git tracks in this package, instead of a list I chose" land?

> One idea. The trade-off stated plainly rather than sold. Another callback. The check quotes the
> sentence they need to hold onto.

---

**User:** Does `tsc` affect `.md` files, though?

**Assistant:**

No — `tsc` completely ignores `.md`. Editing a README can never change a type-check result.

So that re-run is pure waste. But notice *which kind* of waste: Turbo re-runs `tsc`, `tsc`
re-checks, and you get the correct answer, just a few seconds slower. The failure is in speed only.

That's the asymmetry worth holding onto:

- **Hash too many files** → needless re-runs → slower, still correct
- **Hash too few files** → skipped runs → fast, and possibly *wrong*

A wrong green build costs far more than a slow one, so when in doubt, over-include.

> Verdict, then one expansion that earns its place — it turns a narrow question into the principle
> behind it. Paired bullets carry the trade-off in two lines. This is the upper bound on how long
> an answer should get.

---

## Chunk 3 of 3 — Why the second line is still there

Your selected line has two entries:

```json
"inputs": ["$TURBO_DEFAULT$", "$TURBO_ROOT$/packages/config/**/tsconfig.json"]
```

If `$TURBO_DEFAULT$` means "everything," why keep the second one?

Because "everything" means **everything in *this* package**. When Turbo hashes
`packages/ui-transaction-history`, it stays inside that folder. The shared tsconfig lives in a
*different* package — `packages/config` — so `$TURBO_DEFAULT$` never reaches it.

That's what `$TURBO_ROOT$` is for: it means "start from the repo root," letting you point at a file
outside the current package.

So the two entries cover two different blind spots:

- `$TURBO_DEFAULT$` → everything **inside** this package
- `$TURBO_ROOT$/packages/config/...` → the shared file **outside** it

Drop either one and a class of change stops invalidating the cache.

That's the whole change. Check: clear why "everything" still needed help reaching across to
`packages/config`?

> Anchored on the exact line they selected. Opens with the obvious objection instead of waiting for
> it. Signals the end — "that's the whole change" — so they know the loop is closing.
