# 18. How to know the harness is working

**Abstract principle.** Measure the harness, not the volume of code. The scoreboard below is the digest's. It is not DORA's five metrics. Lines of code, prompt counts, and "AI suggestions accepted" are not success. A repeated escape becomes a check. A model change re-runs the same signals after compensatory lines come out.

Digest: §18. Evidence: `harness-sources/13-measurement.md`.

## Scoreboard

| Signal | Healthy direction | What to count in this stack |
| --- | --- | --- |
| Slice resolution | A larger share of slices reach `AUDIT_PASS` without a steward rewrite of the spec or the action | The denominator is slices that entered P3. The numerator is those whose auditor checklist passed and whose spec literals did not change after G2. A green `php artisan test` is not the numerator. SWE-bench "resolved" is a different score |
| Rework | A smaller share of a slice's lines replaced within two weeks | `git log -L` or a short script over the slice's paths: `app/Domain`, `app/Http`, `app/Policies`, `resources/js/Pages`, `database/migrations`, `tests`, `e2e`. This is line churn. It is not DORA's deployment rework rate. GitClear's 3.05% (2020) and 5.67% (2024) are context, not a gate |
| Escape | Defects found after production exposure, tagged by module | A journey failure in CI before merge is a catch, not an escape. An escape is a production hotfix or a rollback after users can hit the candidate. Tag it M1–M7 |
| Verification tax | Steward time per slice, mostly at G2 | Minutes the steward spends on the spec and the evidence, recorded in the progress entry. Diff-reading time under Read is recorded separately. These notes do not include a published benchmark for that split |
| Oracle integrity | Attempts to weaken an accepted example are caught | Count diffs that change a frozen literal or a `steps` array and were reverted, plus any that merged. The target is caught, and merged stays at zero |
| False-green | Verifier rejects where the implementer's tests were already green | Count GV rejects whose self-check log shows Pest green. A high count means the HTTP seam is standing in for the journey, or the literals match the code and miss the spec |

Review the table on a cadence the constitution names. A repeated escape becomes a fitness test or a constitution line. A repeated mistake becomes enforceable: a mechanical miss becomes a check, a judgement stays in `docs/review-judgements.md`, and `AGENTS.md` gains a pointer.

## DORA, recorded beside the scoreboard

The digest keeps DORA's definitions so a team does not mix them into the rows above.

| Metric | Meaning |
| --- | --- |
| Change lead time | Commit on the slice to production deploy |
| Deployment frequency | How often a candidate is promoted |
| Failed deployment recovery time | Time to restore after a bad promotion |
| Change fail rate | Share of production deployments that need an immediate hotfix or rollback |
| Deployment rework rate | Share of deployments that were unplanned production fixes |

The 22 October 2024 survey estimated about a 7.2% reduction in delivery stability and about a 1.5% reduction in throughput per 25% higher AI adoption. The 23 September 2025 report turns the throughput association positive and leaves instability unfavorable. It publishes no new 7.2% figure. Instability still harms product performance and burnout in that report's checks. The gates exist so a throughput gain does not silently buy that instability. Neither percentage is a threshold in `config/desk.php`.

## What the agent records each slice

In `docs/evidence/<slice>/score.md`, four lines, or "n/a" with a reason:

```text
audit: AUDIT_PASS | AUDIT_REJECT
spec-rewritten-after-g2: yes | no
verifier-reject-while-pest-green: yes | no
oracle-edit-reverted: <count>
```

Module escapes and two-week churn are computed on the cadence, not invented inside the feature session.

## What an agent does

- Fills `score.md` from the commands that actually ran.
- When the same false-green appears twice, adds the missing journey assertion or the missing deny case in a follow-up slice.
- On a model change, runs the scoreboard again after `19-evolvability.md`'s deletion pass.

## What an agent refuses

- A dashboard of lines added, commits per day, or tokens spent, presented as the harness result.
- Marking a slice resolved because `php artisan test` passed and Playwright did not run.
- Treating a CI rejection as a change failure in the DORA sense.
- Lowering the oracle-integrity target so a merged literal edit counts as healthy.

## Review checks

- `score.md` agrees with `audit.md` and `verify.md`.
- The cadence review names at least one signal and one action, or names that no signal moved.
- Escapes are tagged with a module id.

## Traps

- `AUDIT_PASS` with a checklist copied from the verifier inflates slice resolution. `17-prompt-contracts.md` P6 says a row the auditor can run and does not run is fail. The measurement inherits that.
- Two-week churn from Pint formatting the whole tree is noise. The churn script limits itself to the slice's allowed paths, or the gardener records a format-only commit outside the feature's numerator.
- A play-test under Metrics that finds a bug is an escape only after production exposure. Before promotion it is a GV or G3 miss, and it still counts as false-green if Pest was green.
