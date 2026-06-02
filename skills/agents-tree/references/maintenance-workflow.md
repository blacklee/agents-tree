# Agents Tree Maintenance Workflow

## When To Add `AGENTS.md`

Add a file only when it will reduce future agent reasoning cost.

Good candidates:

- directory has many files
- directory has several child modules
- directory owns an independent business or technical responsibility
- directory contains high-risk or historically fragile code
- directory has compatibility rules or non-obvious design constraints
- agents repeatedly inspect the directory during normal work

Do not add an `AGENTS.md` for tiny directories with obvious responsibilities.

## When To Update Existing Knowledge

Consider updating an existing `AGENTS.md` when current work changes stable knowledge future agents need:

- a changed file appears in `critical_files`
- a changed symbol appears in `critical_symbols`
- a module responsibility, boundary, entry point, call flow, or verification path changed
- an important directory was added, removed, renamed, or moved
- the agent found a mismatch between existing guidance and current code evidence
- the agent repeatedly had to scan the same directory because local guidance was missing
- the user explicitly asked to update, refresh, or record knowledge

Do not update the tree for every code change. Small implementation edits that do not change durable project understanding should leave the knowledge tree untouched.

## Tree Shape

- Root files act as indexes and route agents.
- Mid-level files describe module boundaries and common work.
- Leaf files may describe stable implementation details.
- Child files must not repeat parent guidance.
- Lower files may override parent rules only when the override is explicit and local.

## Evidence Collection

Prefer the cheapest reliable evidence:

1. existing `AGENTS.md` files
2. code-intelligence tools such as GitNexus or Graphify
3. focused source reads
4. relevant tests or verification commands
5. Git diffs and recent history

Avoid broad source scans unless the existing knowledge is missing or invalid.

## Refresh Discipline

When refreshing:

- verify current code evidence first
- edit only the generated section
- preserve human sections byte-for-byte unless the user asks otherwise
- update metadata only after the generated claims have been checked
- list any unresolved conflicts in the final response

If the user asks for a check-only pass, report findings without editing files.

## Conflict Handling

Treat these as conflicts:

- human text says a module owns one responsibility, but code evidence shows another
- generated text would reverse or weaken a human rule
- a critical file or symbol disappeared
- parent and child `AGENTS.md` files give incompatible instructions

Do not resolve conflicts silently. Report the exact file and the competing claims.
