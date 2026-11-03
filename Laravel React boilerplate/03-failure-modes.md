# 3. Failure modes the harness exists to stop

**Abstract principle.** Each failure has a visible symptom and a response that stops the next session from inheriting it. The agent matches the symptom to the response below and does that response, inside the current slice or by stopping.

Digest: §3. Evidence: `harness-sources/02-failure-modes.md`.

## Failure table

| Failure | What it looks like here | Response |
| --- | --- | --- |
| One-shot | The session adds auth, claim, list, queues, and a design system in one commit. The Vite build or the migration is half-done. | One module, one feature id, one session. The feature's `steps` are one user-visible path. |
| Premature done | `passes` is flipped after `php artisan test` or a `curl` to `/items`. The page still shows the wrong holder. | `passes` stays false until the verifier's Playwright run matches the frozen steps. |
| Dirty handoff | The next session finds `php artisan migrate` failing, a port already bound, or a feature flag half-removed. | Orient runs `init.sh`, then `e2e/core.spec.ts`. A red core journey is the whole session. The commit at the end boots. |
| Self-approval | The same session writes `ClaimItem`, writes the Pest assertion from the model's return value, and marks the feature done. | Spec freezes the literal first. The verifier session has no implementer transcript. The implementer cannot set `passes`. |
| Spec drift | The test expects `"held"` while the spec still says `"claimed"`, or a React enum grew a status the spec never named. | The spec is the contract. Plan alignment looks for a second status string. An underspecified conflict rule stops at G2 with `guess`. |
| Boundary erosion | The slice adds `Gate::before`, a second `ItemRepository` binding, a `fetch('/api/items')` call, or `Log::info($request->all())`. | Constitution edges. The domain-boundary test. One binding. One Prop allow-list. |
| Pattern infection | A controller calls Eloquent, the next controller copies it, and the repository port sits unused. | The plan names the port. The gardener's later slice deletes the copy. The feature slice does not land the copy. |
| Green but unsafe | Feature tests pass with `actingAs` and `withoutMiddleware`. Member B can claim from the booted page. Notes appear in the Inertia payload. | Journey seam plus the allow and deny cases on any security-critical diff. `assertInertia` `missing('item.notes')`. |
| Oracle rot | The agent changes `->toBe('item.claim.not_open')` to `->toBe($item->state)` or deletes the refusal test. | Revert the oracle edit. Read-only tests stop the deletion. They do not stop a special case in `ClaimItem` that returns the expected code only for the fixture id. The verifier's small rule change must still fail. |
| Silent gap | The spec omits the conflict rule. The action picks "last write wins" and the page invents a toast. | Required spec fields. Empty field, stop. The next role does not choose the conflict rule. |
| Unbounded retry | The session rewrites the same Form Request twenty times, then edits the test. | Two verifier or auditor send-backs. The third rejection goes to the steward with the spec, the diff, and the failure output. |
| Stale guide | `AGENTS.md` still says "dump `route:list` and read every Policy before a copy change" for a model that no longer needs it. The session spends the window on that tour and stops before the edit. | Remove the line, run the gate it named, write it back only if the miss repeats. The Policy-as-allow-decision rule stays. |

## Symptoms an agent treats as these failures

| Observation | Failure |
| --- | --- |
| `composer.json` and `package.json` both gain a state library, an HTTP client, and a component kit in one feature | One-shot |
| `docs/features.json` has several `passes: true` written in the implementer commit | Premature done |
| `init.sh` is untracked or points at a port the progress log does not mention | Dirty handoff |
| A Pest test computes the expected holder with the same expression as the action | Self-approval and oracle rot |
| `resources/js/types.ts` contains `notes` and the spec's status read excludes notes | Spec drift and green-but-unsafe |
| `AppServiceProvider` registers two implementations of `ItemRepository` | Boundary erosion |
| `app/Http/Controllers` contains `ItemModel::query()` | Pattern infection |
| Playwright never runs, and the pull request says the feature test is the journey | Green but unsafe |
| The third CI repair edits `tests/Feature/Items/ClaimItemTest.php` | Oracle rot, then escalate |
| The spec's `assumptions` line is missing and the action throws a new exception class | Silent gap |

## What an agent does

- Names the failure in the progress entry when it stops or repairs.
- On a red core journey during orient, repairs that journey and appends the progress entry. It does not start the selected feature in that session.
- On an oracle diff it did not intend, reverts that hunk before any other edit.
- Writes a dead end into `docs/progress.md`: the approach, the command, the result, and why it was abandoned.

## What an agent refuses

- Setting `passes` from a Pest result.
- Deleting a red Playwright spec to make the session end.
- Replacing a domain rule with a fixture id check (`if ($item->id === '01J...') return expected`).
- Starting a second feature "while the suite is already open".
- Carrying an undocumented local server, a manual `.env` tweak, or a migration that ran only in one terminal, as the handoff. The boot path is `init.sh`.

## Review checks

- The commit touches one feature id.
- `passes` is unchanged in an implementer diff.
- `docs/progress.md` records the dead end when the approach changed.
- The core journey file is still in the tree and still expected to pass.
- No new `fetch(` in `resources/js`.

## Tests that exist because of this table

- `e2e/core.spec.ts` covers the journeys already marked passing. Orient runs it.
- `tests/Feature/Architecture/DomainBoundaryTest.php` fails when delivery or Eloquent enters `app/Domain`.
- A feature test on a security-critical slice includes the other actor and the missing actor.
- `phpunit.xml` sets `beStrictAboutTestsThatDoNotTestAnything="true"`.

## Traps

- `actingAs($user)` proves the HTTP seam for that user. It skips the login page. A journey that never types credentials leaves the sign-in feature unproven. M2's journey signs in through the form.
- Inertia's 409 `X-Inertia-Location` on an unauthenticated visit is the login redirect expressed to the Inertia client. The missing-actor journey asserts the login page. A feature test asserts `assertRedirect(route('login'))`.
- `RefreshDatabase` hides leftover rows inside one test process. It does not hide a second store the code started calling. The architecture test and the single binding are what catch that.
- Compaction that summarizes "claim works" and drops "member B must be refused" is the premature-done setup for the next window. The feature `steps` stay in `docs/features.json`, which the summary does not replace.
