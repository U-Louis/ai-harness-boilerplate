# 6. Persistent artifacts

**Abstract principle.** The repository is the memory. Each artifact has one writer and a schema. A missing required field stops the next role. The always-loaded file is a short map into `docs/`. The feature list is JSON and starts failing. Only the verifier sets `passes`.

Digest: §6. Evidence: `harness-sources/04-artifacts.md`.

The paths below match the digest so the two trees can be read side by side. Code paths from `README.md` sit beside them. They are not a second knowledge base.

## Tree

```text
AGENTS.md
docs/
  constitution.md
  architecture.md
  security.md
  reliability.md
  quality-score.md
  product-specs/
    index.md
    <slice>.md
  exec-plans/
    active/<slice>.md
    completed/
    decisions.md
  features.json
  progress.md
  evidence/<slice>/
init.sh
```

`init.sh` starts the application the next session will drive. It is the boot Anthropic's harness asked for. Per-worktree logs are the legibility work: the script prints the origin, the log command, and the database name for this working tree.

## `init.sh`

The script is idempotent. A second run on a booted tree migrates and serves. It does not drop the database.

```bash
#!/usr/bin/env bash
set -euo pipefail
cd "$(dirname "$0")"
composer install --no-interaction
npm ci
php artisan migrate --force
php artisan serve --host=127.0.0.1 --port="${APP_PORT:-8000}" &
npm run dev
printf 'origin http://127.0.0.1:%s\n' "${APP_PORT:-8000}"
```

When the skeleton already defines a single composer script that runs the PHP server, the queue listener, and Vite together, `init.sh` calls that script instead of inventing a second boot. The script prints the origin. The agent refuses a session that starts `php artisan serve` on an unrecorded port.

Playwright's `baseURL` is that origin. Feature tests do not go through `init.sh`. They use the testing environment in `phpunit.xml`.

## Constitution fields that gate the code

`docs/constitution.md` names, before product code:

- one storage engine, one authorization mechanism, one error shape, one log shape
- allowed dependency edges (`09-modules.md`)
- data protection levels for stored fields
- `complexity-score` and, when it blocks, the ceiling
- `diff-posture`
- `instruction-model`
- the ASVS level, when the organization has chosen one
- budgets, each either filled (five SLO fields, or an API4-style bound) or explicitly empty

Empty SLO fields mean the latency gate is off. They are written as empty, not filled with a placeholder millisecond.

## `features.json`

Writer of `description` and `steps`: the spec author. Writer of `passes`: the verifier. The implementer does not edit the file, except when orient finds a core journey red and the digest's rule says to mark that feature failing again. That edit flips `passes` to `false` and is recorded in the progress log.

```json
{
  "id": "F12",
  "module": "M3",
  "category": "functional",
  "description": "A member can claim an open item that nobody else holds.",
  "steps": [
    "Sign in as member A through the login page",
    "Open an unclaimed item",
    "Claim it",
    "See it as claimed by A",
    "Sign in as member B and see that claim is refused"
  ],
  "passes": false
}
```

`steps` are user-visible. "Create `ClaimItemController`" is a plan step, not a feature step. A step that only a Pest test can see is an HTTP seam in the spec, and the feature still has a journey step.

## Slice spec

Path: `docs/product-specs/<slice>.md`. If any field is empty, G2 fails and the planner does not start.

- outcome and actor
- done-means (observable results)
- non-goals
- assumptions, each `confirmed` or `guess`
- invariants this slice must not break
- data written and data read, with protection level
- security-critical paths touched, or "none"
- allowed paths and forbidden paths
- new dependencies, or "none"
- acceptance examples: given / when / then, including one refusal, expected values fixed here
- test seams: public boundary, what it catches, what it misses. The list includes a journey seam. The steward accepts the list at G2.
- quality budgets that apply, or "unchanged"
- risk class
- how a failure is rolled back

### Seams this stack is allowed to name

| Seam | Public boundary | Catches | Misses |
| --- | --- | --- | --- |
| Domain | `App\Domain\<Context>\<Action>` called with an in-memory repository | The rule and the refusal, with literal expected values | HTTP, session, React, SQL |
| HTTP | The named route via `php artisan test` | Status, redirect, error `code`, Inertia prop allow-list, one row in the real test database | Whether the page shows the prop. `actingAs` misses the login form |
| Journey | Playwright against `init.sh`'s origin | The `steps` in `features.json`, as a signed-in person | Native browser dialogs. Time-based races the spec did not name |
| Neighbor contract | Examples the other side owns | A request or response that neighbor depends on | The provider's business rules |

A slice names the smallest set that covers done-means. A feature with a page includes the journey seam. The HTTP seam is included when the route's status, error code, or prop list is part of done-means. The domain seam is included when a rule must fail with the middleware stack absent.

## Plan

Path: `docs/exec-plans/active/<slice>.md`. Required fields:

- ordered steps inside the slice
- files expected to change (concrete paths)
- which existing mechanism is reused: Policy, repository binding, error shape, log shape
- checks to add, and the command
- what stays untouched
- rollback: revert to the commit
- `blocked by`: feature ids, or "none"
- `cut`: the user-visible path through the layers, or a named exception

A feature slice's cut names the page, the route, the Form Request, the Policy, the action, and the migration or repository method. A cut that names only `resources/js/Pages/Items/Show.tsx` fails GP. A wide mechanical change (a column rename, a shared type rename) is expand, then migrate in green batches, then contract.

Exceptions, named in the plan: M0 constitution, M6 evidence, M7 release.

The plan is this folder's thin plan. It is not an invitation to re-decide the spec during implementation. Discoveries that change a `guess` into a new business rule stop the session for the steward.

## Progress log

`docs/progress.md` is append-only prose. Each entry has: slice id, commit, what passed, what failed, dead ends and why, next slice.

```text
## 2026-11-02 M3 F12
commit: abc123
passed: domain seam and HTTP seam for claim
failed: journey, member B still saw the claim button submit a 200
dead end: hid the button in React. Reverted. The Policy was not called.
next: F12 repair, ClaimItemRequest::authorize
```

The next session reads this file and `git log --oneline -20`. A dead end that is missing will be tried again.

## Evidence

`docs/evidence/<slice>/` contains the command output and this record:

```text
slice: M3
commit: <id>
spec: docs/product-specs/M3.md
commands:
  - ./init.sh
  - php artisan test tests/Feature/Items/ClaimItemTest.php
  - npx playwright test e2e/claim.spec.ts
results: pass | fail
frozen-example: <the given/when/then copied from the spec>
rule-change: <the one-line change to the rule that must fail that example>
limitations: <what the journey cannot see>
risk-class: authorization
complexity-score: crap | cognitive | unset
diff-posture: read | metrics | unset
human-read: yes | sample | metrics
blind-spot: <what the checks would miss, or "none named">
```

`rule-change` is a concrete edit, for example flipping the holder comparison in `ItemPolicy::close` or the `where('state', 'open')` in the repository update, with the frozen test then going red, then the edit reverted. A sentence that says "deleting the route would fail the test" does not fill the field.

The authoring agent can write this file. The file is not the merge block. The pinned CI check on the combined tree is the merge block. `14-cicd.md`.

## Map file

`AGENTS.md` stays short enough to load on every turn. Target: the digest's hundred-line map. It contains pointers and the commands the agent cannot infer from a stock Laravel tree. It does not contain the architecture essay.

```markdown
# Desk

Laravel 12, Inertia, React, TypeScript. One origin. Session auth.
Read docs/constitution.md before editing.

## Boot and checks
- ./init.sh
- php artisan test
- npx playwright test
- vendor/bin/pint --test

## Current work
- docs/features.json
- docs/progress.md
- docs/product-specs/

## Rules that are easy to miss
- app/Domain has no Eloquent and no HTTP imports.
- The Policy is the allow decision. Form Requests call it.
- Status props omit notes. See docs/security.md.
- The verifier sets passes. The implementer leaves it false.
```

Product-specific lines replace "Desk" and the notes rule. A generated encyclopedia is reverted. Commands the agent can already infer from `composer.json` stay out.

If `CLAUDE.md` exists, it imports this map. Two competing always-loaded files are a stale-guide failure.

## Single writers

| Artifact | Writer |
| --- | --- |
| `docs/constitution.md` | Spec author, then steward at G2. Later changes are their own slice |
| `docs/product-specs/<slice>.md` | Spec author until G2 |
| `docs/exec-plans/active/<slice>.md` | Planner |
| `docs/features.json` descriptions and steps | Spec author |
| `docs/features.json` `passes` | Verifier |
| `docs/progress.md` | The session that is ending, append |
| `docs/evidence/<slice>/` | Implementer for self-check output, verifier for `verify.md`, auditor for the checklist |
| `docs/quality-score.md` | Auditor, then gardener updates gaps |
| Product code | Implementer or gardener, inside the slice |

## What an agent does

- Creates a missing artifact with every required field, or stops and names the field.
- Keeps `init.sh` executable and committed.
- Updates the progress entry in the same commit as the code, or in the commit the contract names.

## What an agent refuses

- A second feature list in `README.md`, Notion, or a chat recap.
- Markdown checkboxes in place of `docs/features.json`.
- Rewriting `steps` so they match the code the session just wrote.
- Putting secrets, `.env` values, or session cookies into `docs/evidence`.
- A plan that says "same as the other controller" without naming the files.

## Review checks

- Every spec field is present.
- `features.json` is valid JSON. Every object has `id`, `module`, `category`, `description`, `steps`, `passes`.
- The evidence `frozen-example` matches the spec text.
- `AGENTS.md` is still a map.
- `init.sh` is the boot the progress log names.

## Traps

- Laravel's `.env` is invisible to the next session if the only copy lives on one laptop. `.env.example` lists the keys with empty secrets. The required-configuration check fails the boot when a shipped module's key is missing.
- `php artisan config:cache` in a local boot freezes env changes. `init.sh` for development does not cache config. The deploy script may cache config and is a fragile command in the release slice.
- Inertia page names are strings (`'Items/Show'`). A rename of the TSX file without the `Inertia::render` string is a broken journey that feature tests miss if they assert the component name and nobody renamed the assertion. The plan lists both files. The journey loads the page.
