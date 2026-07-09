# Recipe: Safe bulk rename with a dry run

You want to rename a concept everywhere (`getUser` → `fetchUser`, a product name, a config key), but a blind find-and-replace across 80 files is how you break things you didn't know existed.

## The prompt(s)

```
> I want to rename the concept "getUser" to "fetchUser" everywhere —
> source, tests, docs. FIRST: list every file you'd change and what kind
> of occurrence it is (definition / call / string / comment). change nothing yet.
```

Review the list, exclude what shouldn't move, then:

```
> looks right, but skip the CHANGELOG and anything under vendor/.
> do the rename, update imports, then run the test suite to confirm.
```

## Why it works

The dry run turns an irreversible-feeling bulk edit into two cheap decisions: *is the list right?* and *did the tests pass?* The agent finds occurrence types (a string literal is not a function call) that plain `sed` would treat identically.

## Caveats & variations

- On a repo without tests, add: "and grep for any remaining occurrences afterwards".
- For very large repos, scope it: "only under `src/` and `docs/`".
- Commit before running — `git diff` is your final review, `git reset` your undo.

---
*Contributed by the maintainers*
