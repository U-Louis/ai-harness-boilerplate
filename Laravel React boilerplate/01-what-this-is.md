# 1. What this is, and what it is not

**Abstract principle.** A harness is the runtime around the model: the loop, the tools, what enters each context window, the safety controls, and the stop rules. This folder is outer policy for that runtime. A human stays on intent, spec, and evidence. The constitution chooses the complexity score and whether a person reads the diff. The builder does not merge its own work.

Digest: `Abstract boilerplate/Abstract-boilerplate.md` §1. Evidence: `harness-sources/01-harness-definition.md`.

Architecture: `README.md`. One Laravel 12 application, Inertia v2, React 19, TypeScript.

## What the agent is looking at

The coding product already supplies the inner loop (files, shell, a permission mode, compaction). This folder is the outer policy that loop still has to execute: roles, artifacts, gates, and stop rules for a Laravel + Inertia + React tree. A sentence in `AGENTS.md` becomes a harness rule when a check can fail the run. Until then it is a guide.

Three layers stay distinct:

| Layer | Where it lives here | What it decides |
| --- | --- | --- |
| Inner harness | The coding agent the steward already runs | The loop, the shell, compaction, the permission prompt |
| Outer policy | This folder, then the product's `docs/` and checks | What a slice may change, and which command must be green |
| Product code | `app/`, `resources/js/`, `routes/`, `database/`, `tests/`, `e2e/` | The behavior the spec froze |

The agent treats a green Pest file as a sensor reading. It treats a paragraph in a chat as gone when the session ends. The next session reads `docs/progress.md`, `docs/features.json`, and `git log`.

## What this policy requires of the stack

- One application, one origin, one session guard (`web`).
- Domain rules in `app/Domain/<Context>/`, with no HTTP and no Eloquent imports.
- Delivery in an invokable controller, a Prop class, and one React page.
- Storage behind one repository interface, one Eloquent implementation, one container binding.
- Authorization in `app/Policies`, invoked from the Form Request.
- Input rules in the Form Request. The page displays the server's `fields` map.
- A journey in `e2e/` that boots `init.sh` and drives the page.
- Forge rules that reject a merge by the identity that pushed the implementation.

## Guides and sensors

Guides, loaded before the edit:

- `AGENTS.md`, a short map. It points at `docs/constitution.md` and the current spec. It does not contain the constitution.
- The slice's allowed paths and the ask-first list in the spec.
- Path-scoped rules (`.cursor/rules` or `.claude/rules`) only when they point at a file the agent would otherwise miss. A rule that must always hold is a test, a Pint or ESLint failure, or a forge rule.

Sensors, run after the edit, with text that says how to comply:

| Sensor | Command | Failure text tells the agent to |
| --- | --- | --- |
| Format | `vendor/bin/pint --test` and the ESLint format check the constitution names | run the fixer, then re-run the check |
| Domain boundary | `php artisan test tests/Feature/Architecture/DomainBoundaryTest.php` | move the import out of `app/Domain` |
| HTTP seam | `php artisan test tests/Feature/<Context>` | make the route match the frozen example |
| Journey | `npx playwright test e2e/<feature>.spec.ts` | make the booted page show the frozen result |
| Types | `npm run typecheck` (`tsc --noEmit`) | change the Prop class and `resources/js/types.ts` together |
| Secrets | the secret scanner on push, and again in CI | remove the secret from the tree and rotate it |

A sensor whose message is only "violation detected" is unfinished. The agent that meets that message stops and asks for a remedy sentence in the same slice that added the sensor.

## What the constitution still chooses

This folder leaves two product choices empty, matching the digest:

- `complexity-score`: `crap` or `cognitive`, plus a ceiling. Until both are set, the check records cyclomatic complexity, CRAP, and cognitive complexity, and neither score blocks.
- `diff-posture`: `read` or `metrics`. Until it is set, G4 does not merge.

`instruction-model` is the model the compensatory lines were written for. `19-evolvability.md` is the deletion rule.

## What an agent does

- Reads `README.md` and the numbered file for the concern before editing that concern.
- Keeps product changes inside one feature id and the spec's allowed paths.
- Records the command, the tool version, and the result in `docs/evidence/<slice>/`.
- Leaves merge, approval, and production promotion to a forge identity that is not the implementer.

## What an agent refuses

- Treating this folder, or `AGENTS.md`, as a substitute for the forge rule that blocks self-merge.
- Copying OpenAI's agent self-merge into the repository because a blog described a mature internal product.
- Adding a second application (a Node API, a separate SPA origin, a Livewire island) so that a slice can "move faster".
- Filling an empty latency number, an empty page size, or an empty complexity ceiling with a figure from the digest. Those figures are either examples or history. `02-claims.md` lists them.

## Review checks

- The pull request's allowed paths match the spec.
- `routes/api.php` gained no product route.
- `app/Domain` gained no framework delivery or Eloquent import.
- The evidence file names commands and results.
- The author identity is absent from the forge bypass list.

## Traps

- A starter kit that ships settings, teams, and profile pages has already chosen scope. Those routes are either named in the accepted spec or removed before verify. Leaving them unwired teaches the next session that unused routes are architecture.
- `AGENTS.md` that pastes the constitution back into the always-loaded file recreates the giant instruction file the digest rejected. The map points. The constitution stays in `docs/constitution.md`.
- Claude loads `CLAUDE.md` and skips `AGENTS.md` unless `CLAUDE.md` imports it. A repository that keeps both files imports the map from `CLAUDE.md`, or deletes the duplicate so one file is the map.
