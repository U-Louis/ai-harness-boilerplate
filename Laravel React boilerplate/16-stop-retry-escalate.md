# 16. Stop, retry, escalate

**Abstract principle.** Every condition has one next action. The third verifier or auditor rejection leaves the loop for the steward, with the spec, the diff, and the failures. The next role does not invent a missing field. The question at escalation is which capability or rule is missing. That question is asked because this digest's cap was hit. The answer is written into the constitution or a check, in a follow-up slice the steward accepts.

Digest: §16. Evidence: `harness-sources/02-failure-modes.md`.

The cap is the digest's. The closer published stops are three attempts then raise, and three review rounds then reconsider the approach. The agent still uses two send-backs.

## Conditions

| Condition | What the agent does |
| --- | --- |
| A required spec, plan, evidence, or feature field is missing | Stop. Name the field. Do not let the controller, the page, or the next role choose the value |
| Core journey red at orient | Repair that journey only. Set its `passes` to false if it had been true. Append progress. Do not start the selected feature |
| Self-check red (Pint, `tsc`, Pest, the slice's Playwright spec) | Repair inside the slice. This does not consume a send-back |
| Verifier or auditor reject | Return the failure output to the implementer. First and second time are send-backs |
| Third reject | Stop. The steward gets the spec path, the commit, and the failure outputs. The implementer does not open a fourth repair |
| The slice needs a new dependency, a new stored field, a new route the plan did not name, or a retry policy | Stop at ask-first. The steward confirms, or the plan is updated in a spec session |
| The diff edits an accepted example, a feature `steps` array, or a frozen literal | Revert that hunk. Record `oracle edit reverted` in progress. A special case in the action is still possible; GV's rule-change is what catches it |
| The gate's tool is down (Playwright browser missing, CI queue stuck, scanner timeout) | Pause. Do not skip the check, do not weaken it, do not set `continue-on-error` |
| The session starts doing a second feature or a non-goal | Cut back to the feature id, or stop for a new spec |
| Another run labels a ticket, issue, or merge request ready so an implementer starts | Stop. That label is not a start. A person with write access, a clock the decision log already accepted, or a failed check is. `21-unattended-runs.md` |
| `init.sh` cannot migrate | Repair the migration or the env example. Do not point the app at another database to get a green journey |
| The same Pest failure remains after two edits that did not change the approach | Stop the self-check loop. Record the dead end. Escalate if a send-back cap is also spent. A third edit that changes the test is an oracle edit |

## What escalation writes

The steward's follow-up slice, which an agent may draft and may not merge alone, does one of:

- Adds a fitness test whose failure names the repair (a second repository binding, a `notes` prop, a `fetch` in `resources/js`).
- Adds a constitution sentence and a pointer in `AGENTS.md`.
- Splits the feature because the spec held two behaviors.

The progress entry quotes Lopopolo's question as the digest uses it: which capability is missing, and how the check will make it enforceable. The entry also says the trigger was this cap, which is the digest's trigger.

## Laravel and React symptoms that mean stop

| Symptom | Action |
| --- | --- |
| `composer require` for a package the plan does not list | Stop. Revert `composer.json` and the lockfile |
| A new key in `.env` that `RequiredConfiguration` does not know, and the spec does not mention | Stop. Ask first. A new required key is a constitution or spec line |
| `ItemPolicy` method added for a second product concept (organizations, invoices) | Stop. That is a new intent |
| React context or Zustand introduced to hold the item | Stop. Props are the state. Revert the dependency if one was added |
| `routes/api.php` route added so the page can `fetch` | Stop. Revert the route and the client call |
| `passes: true` in the working tree during P3 | Revert the JSON change |
| Test file deleted because it failed | Restore the file. Repair the product, or escalate |
| `php artisan migrate:fresh` against a database that is not the working tree's | Stop. That is data loss. The Replit lesson in the source notes is the reason the agent has no production database in development |

## Retry inside the slice

The implementer may retry the self-check as many times as the edits still follow the accepted approach. Each dead end is a progress paragraph: what was tried, the command, the output, why it was abandoned.

A retry that edits the expected literal to match the code is not a retry. It is an oracle edit, and it is reverted.

A retry that wraps the assertion in `try/catch` and forces a pass is an oracle edit.

## Pause when tooling is down

If `npx playwright test` cannot launch a browser, the agent installs the browser with the Playwright command the repository documents (`npx playwright install --with-deps` only when the plan or `AGENTS.md` already lists it) or pauses. It does not delete `e2e/` and point GV at Pest.

If CI is queued and the local self-check is green, the agent waits for the pinned check. It does not merge on the local result.

If the secret scanner times out, the push stays blocked. The agent does not add the secret path to an allow-list.

## What an agent does

- Counts send-backs in `docs/progress.md`.
- Stops with a named field when the spec is incomplete.
- Writes the escalation bundle as paths and command output, not as a paraphrase.

## What an agent refuses

- A fourth GV repair.
- Applying a ready label, or starting implementation because another run applied one.
- Improvising the close-already-closed behavior while F12 is the session's feature.
- `git push --no-verify` to skip a hook that runs Pint or the schema check.
- Resetting the send-back count by opening a new branch with the same diff.
- Answering escalation by "trying a microservices split" or a new frontend stack.

## Review checks

- Progress entries match the table's actions.
- Reverted oracle hunks are absent from the branch tip.
- Escalation commits do not contain product code written after the third reject.
- `composer.json` changes trace to the plan.

## Traps

- Opening a "fix lint" commit that also changes `ClaimItem` hides a send-back inside a chore commit. The commit message stays on the feature id, and the progress entry still counts the reject.
- `git commit --amend` after a verifier has already fetched the commit splits the evidence from the sha. A repair is a new commit. The evidence names the new sha.
- A flaky journey retried inside CI until green consumes no send-back and produces false evidence. Quarantine is the action. The feature does not pass on a flaky command.
- The agent that "stops" by writing a TODO in the controller and setting `passes` has not stopped. `passes` stays false and the TODO is a progress dead end, or the code is reverted to the last green commit.
