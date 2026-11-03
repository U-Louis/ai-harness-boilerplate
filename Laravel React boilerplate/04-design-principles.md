# 4. Design principles

**Abstract principle.** These principles decide what a prompt, an artifact, and a gate may do. A session stays small. The record lives in files. Human time goes to decisions a check cannot make.

Digest: §4. Evidence: `harness-sources/01-harness-definition.md`.

Each principle below is a constraint on Laravel and React edits.

## 1. One prompt, one purpose

A session is one of: research, specify, plan, implement, verify, lens, audit, garden, release. The contracts are `17-prompt-contracts.md`.

An implementer session that also rewrites the spec, or a verifier session that also "fixes the Policy quickly", has two purposes. The agent stops and leaves the second job for the role that owns it.

## 2. Durable artifacts, not chat memory

The next session's inputs are `AGENTS.md`, `docs/constitution.md`, `docs/progress.md`, the slice spec, `docs/features.json`, and `git log`. A decision that exists only in the chat is absent. The agent writes it into the spec, the decision log, or the progress entry before it commits.

`storage/logs/laravel.log` is a runtime trace. It is not the progress log. The agent may quote a line from it into `docs/evidence/<slice>/` and does not point the next session at a log file that `init.sh` truncates.

## 3. Progressive disclosure

`AGENTS.md` is the map. Deep documents are opened when the slice needs them:

| Open this | When the slice |
| --- | --- |
| `docs/constitution.md` | always, it is the map's first link, and it is short |
| `docs/architecture.md` | adds a class, a binding, or a namespace |
| `docs/security.md` | touches auth, a Policy, a migration, or a Prop |
| `docs/reliability.md` | adds a timeout, a retry, a job, or a lock |
| `docs/product-specs/<slice>.md` | implements or verifies that slice |
| `resources/js/types.ts` and the Prop class | changes a page |

The agent refuses a preamble that says "read every file under `app/Domain` before any edit". That sentence is a compensation. The map may say "open the action named in the plan".

## 4. Invariants over style

Strict:

- Dependency direction in `09-modules.md`.
- One Policy per ability, called from the Form Request.
- One Prop allow-list. Notes and secrets stay out of status props.
- One repository binding.
- One error shape and one log shape.
- Validation rules on the server.
- Budgets the constitution has actually filled.

Loose, inside the slice's allowed paths:

- Private method names inside an action.
- Component file split inside `resources/js/Components` when the page's props stay the same.
- CSS approach. The digest does not make Tailwind, or any other utility kit, an invariant. A starter that already uses one is left as it is. A slice does not introduce a second styling system.

Pint and ESLint formatting are sensors so the diff stays readable. A formatting disagreement is not a reason to add a second code style.

## 5. Deterministic outer loop

Anything a program can observe, and that must always be true, is a failing check with a remedy sentence. Examples the agent installs at M1 and keeps:

- Domain files importing Eloquent or HTTP fail `DomainBoundaryTest`.
- A status Prop array containing `notes` fails the feature test's `missing('item.notes')`.
- A route posting an item with an empty title fails the frozen validation code.
- `vendor/bin/pint --test` fails unformatted PHP.
- ESLint with `linterOptions.noInlineConfig: true` fails a suppressed lint, and the config change is a risk-class diff.

An intent nobody wrote into the spec has no sensor. The agent does not add a test for a behavior the spec lists as a non-goal.

## 6. Readers fan out; writers do not

One implementer writes product code for the slice. Lenses read the same frozen diff in parallel and return findings. They do not push fixes onto the branch.

Two slices may be implemented at the same time only when their allowed paths do not overlap, in two working trees. Two agents in one working tree will amend each other's commits. The agent refuses a shared checkout for parallel writers.

The lens report keeps a standards list and a spec list. A Policy that allows every member, and a spec that asked for holder-only close, are two findings. The agent does not average them into one "severity".

## 7. Evidence before opinion

A gate records the command, the version, and the result. For this stack the evidence block names:

```text
php artisan test --version   # or `php artisan test` and the Pest version from composer.lock
npx playwright test --version
node --version
php --version
```

plus the exact test paths. "The tests passed" is not the record. A screenshot path from Playwright is part of the journey record when the spec's steps are visual. The trace id from the log line is part of the record when the slice's budget is a trace.

## 8. Human time is the scarce resource

The steward's minutes go to G1, G2, G3, and G4. They do not go to re-checking a domain-import failure the test already printed.

Under Read, a person other than the author reads the diff, including tests, for a risk class, for a blind spot nobody can name, and on the sample of evidence-only merges. Under Metrics, that person reads the evidence and play-tests the booted app through the browser. They leave the agent diff unread. Both postures still require the forge to refuse the implementer's merge.

## 9. Small, mergeable, recoverable

Each slice ends with:

- `./init.sh` able to migrate and serve.
- `php artisan test` green for the suite that was green before, plus the new seam.
- The core Playwright spec green.
- A commit. `git revert` of that commit is the rollback named in the spec.

A migration that cannot roll forward on the next boot is not a finished slice. Production rollback of schema is a new migration (expand, then migrate, then contract), described in `10-quality-attributes.md`.

## 10. Feedback becomes a rule

A repeated mechanical miss becomes a check the implementer can run:

| Repeated miss | Check to add |
| --- | --- |
| Eloquent in a controller | A test that fails when `app/Http/Controllers` contains `Illuminate\Database\Eloquent\Model::query` or `ItemModel::` |
| `notes` added to a status prop | The existing `missing('item.notes')` assertion, extended to the new page |
| `authorize(): bool { return true; }` on a write | A feature test that the other actor is forbidden, already required on that path |
| `console.log` of props | ESLint `no-console` as an error on `resources/js` |

A repeated judgement ("this action is doing two use cases") stays in the reviewer file the lens reads. The map gains one pointer to that file. The agent does not paste the judgement into `AGENTS.md` as a slogan.

## 11. A compensation expires

A line that tells the model which files to open, in which order, or scolds it for a miss the current model no longer makes, carries `instruction-model`. On a model change the steward removes it, runs the gate it named, and writes it back only if the miss repeats.

Invariants have no date: who may merge, the frozen examples, the domain boundary, the allow and deny cases, a filled budget, the single repository binding.

A fragile command stays exact. `php artisan migrate --force` in the deploy script is exact. A paragraph about why migrations exist is a compensation and can be deleted. `19-evolvability.md` is the pass.

## What an agent does

- Checks a proposed edit against principles 4, 5, and 9 before writing it.
- Puts a new decision in `docs/exec-plans/decisions.md` with the alternative rejected.
- Keeps the session inside one contract.

## What an agent refuses

- A second writer in the same working tree.
- A slice that cannot boot.
- A gate that fails with no remedy sentence.
- Deleting an invariant during the model-change pass because the line "felt like a prompt".

## Review checks

- One feature id in the commit message.
- Evidence names commands and versions.
- New strictness is a test or a linter, and the failure text names the repair.
- `AGENTS.md` grew by a pointer, or it did not grow.

## Traps

- Laravel's service container will resolve a concrete Eloquent model if the controller type-hints it. That is a second storage path even when the interface also exists. Controllers type-hint the action or the repository interface, and the provider binds the interface.
- React context that stores the item after the first render becomes a second source of truth when Inertia refreshes props. The page reads props.
- A "small" copy tweak that changes a validation message the test asserts by English sentence breaks the oracle. Tests assert `code` and `fields` tokens. The page maps tokens to sentences.
