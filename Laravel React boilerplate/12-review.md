# 12. Review: how, when, by whom

**Abstract principle.** Review is several activities. Collapsing them into "someone reads the diff at the end" lets intent errors survive. The implementer does not approve. An agent's approval never counts as a person's. The verifier re-runs the journey. Lenses read one frozen diff and keep a standards list and a spec list separate. Never skip G1, G2, or G3.

Digest: §12. Evidence: `harness-sources/10-code-review.md`, `harness-sources/14-matt-pocock.md`.

A clean lens report does not prove the spec. The best single LLM review pass the digest cites on SWR-Bench is F1 19.38%. Giving the verifier the spec supplies an intent that benchmark often lacked. It does not turn a silent verifier into a human reading of the tests.

## When

| When | Question | Who | Reads the diff? | Output |
| --- | --- | --- | --- | --- |
| After the glimpse | Worth doing? | Steward | No | G1 |
| After the spec | Are done-means observable, seams named, guesses labeled? | Steward | No | G2 |
| After the plan | Any second mechanism, one-layer cut, or scope creep? | Planner, then steward if a new mechanism is proposed | No | GP |
| During implementation | Did the commands go green? | Implementer and tools | No | GI |
| After the slice | Does behavior match the frozen spec? | Verifier, fresh | The frozen diff, and re-runs the journey | GV |
| After GV is clean | What did the six lenses see? | Lens reviewers, parallel | The same commit | Findings |
| After lenses | Did the checklist pass on the latest tree? | Auditor | No, unless a row failed and the command must be found | GA |
| Before merge | Is the evidence real, and what would it miss? | Steward | Under Read: risk class, unnamed blind spot, or the sample. Under Metrics: no. The person reads the evidence | G3, G4 |
| On a cadence | Is the codebase drifting? | Gardener | Its own small diffs | Cleanup slices |
| On a model change, and every six months if the name has not changed | Which lines compensated for an older model? | Steward | No. The gate each line names, with that line removed | A decision-log row |
| Before promote | Is this candidate fit to expose? | Steward | Under Read: risk classes and the rehearsal. Under Metrics: evidence, rehearsal, play-test | GR |

Skip lenses only when GV already failed.

## What each lens looks at in this stack

One lens per session. Input is the frozen diff and the spec. The lens is read-only: no edits, no `php artisan migrate`, no commits. It may cite a file and a line. It may name the command that would confirm the finding. It does not run a repair.

| Lens | A finding here looks like |
| --- | --- |
| Correctness | The conditional update is missing and two holders can exist. The page shows `state` from React state that was set before the redirect finished |
| Boundaries | `app/Domain` imports Eloquent. The controller calls `ItemModel`. The page calls `fetch`. A second binding appears in a service provider |
| Security | `authorize` returns true. `notes` is on `ItemStatusProps`. A log line includes the request body. A migration adds a column the Prop then returns. `APP_DEBUG` is committed true |
| Tests | The expected value is computed by the code under test. The journey is `request.post` with no page. `withoutMiddleware` wraps an auth case. The refusal example is gone |
| Simplicity | The slice added a service class, an event bus, and a queue for a synchronous claim the spec said was one request. New complexity the author introduced |
| Operability | The new failure returns a 500 with the SQL in the Inertia error modal. The correlation id is not on the log line. The rate limiter was added in the controller and also in a middleware with two different integers |

Findings cite the constitution line or the spec line. Standards findings and spec findings stay in two lists. The lens does not rank them into one winner. A finding without a hunk is omitted. A requirement the spec does not state is omitted.

High findings block. Low findings go to the gardener queue unless this slice introduced the complexity, in which case they block until the steward defers them with a feature id.

The judgement list is not re-run until it is empty. A silent "looks fine" on one axis is allowed. An empty list is allowed.

## What a person reads under Read

The reviewer starts with the spec delta and with what the evidence would miss. Then they read the change, including the tests. For this stack that means:

- The Form Request `authorize` and `rules`, and the Policy method.
- The action and the repository method, including the `where` clause on state.
- `ItemStatusProps` and the matching TypeScript type.
- The Pest literals and the Playwright assertions.
- The migration, if any, and whether it is expand-only.

Style narration is not the job. Stopping at the Policy and skipping the test that hard-codes the wrong code is an unfinished review. Tests do not test themselves.

A UI change is weakly reviewed by reading TSX. The evidence includes the Playwright output. Races and double submits are read in the action, because running the page once will not show them.

## Under Metrics

The person does not read the agent diff. They read the evidence record, including commands, frozen examples, and metric results, and they play-test the booted system. A metric gauntlet can pass and a play-test can still find specification misses. The digest records that outcome. The play-test follows `features.json` steps in the browser, including the refusal.

Green Pint, green `tsc`, and a low CRAP score are not G3.

## Fresh context

The verifier and each lens start a session that does not contain the implementer's transcript. The parent that launches them passes the spec path, the commit, and the command list. A fork that inherits the implementer's chat is the wrong tool for GV and for lenses.

The repair session is the opposite. It receives the failure output.

## Repeated misses

A repeated mechanical miss becomes a check in `11-quality-gates.md`. A judgement stays in `docs/review-judgements.md` (the file the lens reads). `AGENTS.md` gains one pointer to that file when the judgement is new. The pointer names when to open it ("when editing `app/Policies`").

## Who does not review

- The implementer does not approve and does not merge.
- The verifier does not edit product code.
- The auditor does not fix and then pass.
- A bot review on the pull request does not satisfy G3 or G4.
- The forge Approve button is not G4. Under Read the person reads the diff, including the tests. Under Metrics the person reads the evidence and play-tests. The agent's summary is neither. `21-unattended-runs.md`.
- Managed code review that only reads the diff and does not boot the app is a lens-shaped reader. It is not the verifier. This folder's verifier boots and drives.

## What an agent does

- Captures one commit and points every lens at it.
- Writes findings as `file:line`, severity, list (`standards` or `spec`), citation, and the confirming command.
- Sends high findings back as a GV or GL reject with the output.
- After two identical review comments, adds the mechanical check or the judgement file in a follow-up the steward accepts, rather than a third comment with the same sentence.

## What an agent refuses

- A review session that pushes a "fix" commit onto the implementer's branch.
- Blending "Policy is wrong" and "spec never mentioned holders" into one medium finding.
- Treating `assertInertia` component name as proof the button is visible.
- Skipping G3 because the diff is small.
- Using Metrics as a way to merge with no evidence file.

## Review checks

- Six lens outputs, or a recorded skip because GV failed.
- Each high finding has a hunk and a citation.
- The steward's G3 note names the blind spot or says none is named.
- Under Read, the evidence `human-read` matches the paths.
- The merging identity differs from the implementer.

## Traps

- Inertia diffs hide behavior in `HandleInertiaRequests::share`. A slice that adds `notes` to the shared user or the item share affects every page. The security lens reads that middleware when the diff touches props.
- A Pest `actingAs` test and a Playwright test can assert different codes. The reviewer checks they encode the same literal from the spec.
- Laravel's `assertOk` on a redirecting controller is the wrong assertion (`post` follows redirects in some client versions and not in others). The spec freezes the status the test must see. The agent uses `assertRedirect(route('items.show', $item))` for the happy path and asserts the database row. The journey asserts the visible status.
- Comment threads that re-flag a pre-existing line inflate the send-back count. Lenses cite lines in the frozen diff. Pre-existing nits are gardener entries, not a reject of this feature.
- `REVIEW.md` or a pull-request template that the branch itself edits is not the reviewer instruction. Reviewer instructions are taken from the base branch, matching the digest's note on managed review.
