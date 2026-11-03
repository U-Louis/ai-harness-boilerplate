# 19. Evolvability

**Abstract principle.** The harness stays easy to change when the model changes. A line is a compensation, an invariant, or a fragile command. On a model change, compensations are removed, the gate they named is run, and a line is written back only if the same miss repeats. Invariants stay. A thin tool loop that matches a product harness on issue-shaped tasks is not evidence that this folder's gates can be dropped.

Digest: §19. Evidence: `harness-sources/16-evolvability.md`.

## Three kinds of line

| Kind | Examples in this stack | On a model change |
| --- | --- | --- |
| Compensation | "Before every edit, open every file under `app/Domain`." "Run the full Playwright suite before a copy change." "Always dump `php artisan route:list`." A scolding comment in `AGENTS.md` about a miss the current model no longer makes. A prescribed step order that restates P3's contract as ten micro-steps | Remove it. Run the gate it names. Write it back only if the same miss repeats, dated to the new `instruction-model` |
| Invariant | The implementer does not merge. `passes` is the verifier's. Frozen examples. Domain files do not import Eloquent or HTTP. One repository binding. The Policy is the allow decision. `ItemStatusProps` omits notes. Allow and deny cases on a critical path. A budget the constitution filled. `noInlineConfig`. The forge bypass list. A ready label is not a start. The model job does not hold the write token. GW | Stays. The deletion pass does not include it |
| Fragile command | `php artisan migrate --force` in the deploy job. `composer install --no-interaction` and `npm ci` in CI. The expand/migrate/contract sequence for a column rename. A migration script the spec says must run as one command with no extra flags | The command stays exact. The paragraph that explains why migrations exist may be deleted |

`instruction-model` in `docs/constitution.md` is the model the compensatory lines were written for. A procedural line that names no gate is a compensation. A judgement in `docs/review-judgements.md` stays. A second model is a second test of the same lines, not a second copy of the old prompt pasted underneath.

## What the pass deletes

The steward lists compensatory lines in `AGENTS.md`, in path-scoped rules, in skills, and in the prompt contracts only where a contract has grown a step order the digest's P3 does not contain. The contracts in `17-prompt-contracts.md` are outer policy. Deleting their invariants is out of scope. Deleting a sentence that tells the model which directory to open first, when the plan already names the file, is in scope.

The rebuild is: delete, use the model on a real slice, add a line back only when the same stumble repeats. Guessing the line first is the failure the source notes attribute to over-specifying the path.

An old line that says "run the entire suite before any edit" makes a careful model spend the window on tests the task does not need. The orient rule stays narrower: `e2e/core.spec.ts` before new work, because a red core journey is the digest's dirty-handoff response. That line is an invariant of the session, not a compensation, and the pass leaves it.

A hard "ask the steward before every Form Request edit" written so a weaker model would pause will make a more aligned model stop on a slice the spec already allowed. Ask-first stays the list in `11-quality-gates.md`: new dependency, new stored field, new entry point, retry policy. It does not grow to every file.

## What stays

Safety, permissions, and static analysis stay. In this stack that is:

- Forge rules: the implementer cannot merge, required checks are pinned, stale approvals are dismissed.
- `DomainBoundaryTest`, `ControllersDoNotQueryTest`, ESLint `no-restricted-imports`, `noInlineConfig`.
- Secret scanning and `RequiredConfiguration`.
- The verifier, the frozen examples, and the journey as the done-check for a page.
- Policy registration and the Prop allow-list.
- The unattended-run controls: a person, a clock, or a failed check as the start; a read-only model job; GW; a person on any edit to the constitution. `21-unattended-runs.md`.

Verification is the part the source notes say people leave out. A long session may drop a prescribed micro-order. It still has a way to know the work is done: the spec's steps on the booted origin.

A gate that has become a script (Pint, the domain-boundary test) loses the supervision of a person re-checking it by eye. The script stays. That is the trust dial the source notes describe: supervision comes off a step that now runs itself.

## A thin loop is not a reason to drop the constitution

Issue-shaped benchmarks in the digest show a small tool loop keeping up with a large product harness on pass rate and costing less. The same sources say the result may not hold when requirements evolve and tasks cross sessions. This folder is the cross-session policy: `features.json`, the core journey, the Policy, the oracle. The agent refuses a proposal to delete those because a benchmark matched on SWE-bench.

Search and code execution stay available to the agent. They are not a substitute for the forge rule.

## The pass

Trigger: `instruction-model` changes. Six months with the same name is the backstop. Both intervals come from the source notes. Using them on this folder is the digest's choice.

The steward:

1. Lists compensatory lines and the gate each names.
2. Removes them.
3. Re-runs that gate on the current tree (`e2e/core.spec.ts`, the architecture tests, or the slice the line was meant to protect).
4. Records in `docs/exec-plans/decisions.md`: the model, the line, the command, the result.
5. Writes a line back, dated to the new model, only when the miss repeats. A miss a program can see becomes a check instead of a sentence.

Invariants and reviewer judgements stay off that list. A frozen example and an allow/deny pair stay in force even when a coding benchmark looks saturated.

## What an agent does

- Tags a new `AGENTS.md` sentence with the model name when the sentence is a compensation (`instruction-model: <name>`).
- Leaves invariant sentences untagged so the pass can see the difference.
- Runs the named gate after a deletion the steward asked for, and pastes the result into the decision log.

## What an agent refuses

- Deleting `DomainBoundaryTest` because the new model "understands layers".
- Deleting the forge rule because review bots are more accurate.
- Pasting the previous model's prompt under the new one "to be safe".
- Treating a green core journey after a deletion as permission to delete the journey.
- Shrinking P4 because the model self-checks. Self-check stays P3. `passes` stays P4.
- Editing `docs/constitution.md` from a clock. A repeated miss becomes a slice a person accepts.

## Review checks

- The decision log has one row per removed line.
- A line written back names the repeated miss and the new model.
- The invariant list in this file still matches the tree: the boundary test exists, the bypass list excludes the implementer, status props still omit the account-class fields the spec names.

## Traps

- A skill file that duplicates P3 and then adds twenty folder-specific steps becomes the stale guide. The skill points at `17-prompt-contracts.md`. It does not fork it.
- Laravel upgrade notes ("always clear bootstrap/cache before migrate") are fragile commands when a real failure required them, and compensations when they were fear. The pass runs a migrate on a fresh boot. If it succeeds, the extra sentence stays out.
- TypeScript strictness and Pint are sensors, not compensations. Turning them off because a model finds them noisy is an invariant edit and a risk-class slice.
- Path-scoped rules that load on every `*.php` file and paste this whole folder into the window recreate the giant instruction file. The rule contains a pointer. The agent opens the numbered file when the task matches.
