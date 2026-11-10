---
name: toki
description: Mandatory file-placement guardrail. Invoke this skill EVERY time before you create, add, move or rename any file or directory (components, handlers, routes, models, schemas, utils, tests, benchmarks, examples, executables, scripts, config), even a single small file. To save a turn, invoke it in the same parallel batch as your first look at the project (listing or reading files).
---

# Where does the new file go?

Quick lookup, not a cleanup. Every turn re-sends all context: make few, combined calls.

## Turn plan
1. ONE shell command that both looks and reads: list top level and source root, print the manifest, AND print the files you will likely import from or edit, guessed by convention: the entry file(s) and any existing file for the ticket's entity, via globs. Example: `ls -A . src 2>/dev/null; cat <manifest> <entry file> 2>/dev/null; head -n 150 <globs for the entity's files> 2>/dev/null`. No recursive listings or searches.
2. Only if a file you need is still missing, ONE more `cat a b c`. Otherwise go straight to writing.
3. Write all new files and the wiring edit together. The write tool makes parent dirs; no mkdir/ls. Any move (rule 5) or empty package marker: ONE shell command in the same message, e.g. `mkdir -p <dir> && mv <old> <dir>/<init file> && touch <marker>`. Never move by reading, rewriting and deleting.
4. Reply in at most two plain sentences: files created and wiring added. Don't re-read what you wrote.

## Routing
1. One new file per unit the ticket asks for (component, handler/route, model/schema, utility, test, benchmark, example, executable), in its role's directory, even if existing code is flat or in one entry file. Edit existing files only to wire it in.
2. Reuse an existing role directory, even a variant name (`routes/` or `routers/`). Otherwise use the stack's standard place: the framework's documented layout, or where its build tool or test runner looks by default. Put it inside the source root, or at top level if flat. Use plain names (`components/`, `models/`, `schemas/`, `routes/`, `handlers/`, `utils/`, `tests/`).
3. If every directory is an import package and the language has a root for non-entry packages, put new packages there, never in a bare top-level role dir, even if flat. In Go, for example: `internal/handlers/`, extra executables in `cmd/<name>/`.
4. No new code files in the project root unless the convention requires it. Tests go in `tests/` even if flat, unless the language requires tests beside the code, in the same package. Helpers go in a role directory or the new unit's file, never a new root file.
5. If an existing module already has the role directory's name (a root `models` file, for example), `mv` it unchanged into the new package's init/index file so imports still resolve, then add the new file beside it.
6. If the code needs types from a non-importable entry package, pass them in as parameters or a small interface and register from the entry point.

## Preserve existing code
Never rewrite, empty, shorten or delete existing files. Move a file only for rule 5, with its content byte-for-byte intact, and update every import or reference to it.

## Lean
Don't run anything to check placement. Run builds or tests only if the ticket asks; don't retry blocked commands. Add no README, config or scaffolding the ticket didn't ask for.