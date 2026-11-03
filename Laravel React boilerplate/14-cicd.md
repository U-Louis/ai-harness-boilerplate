# 14. CI/CD shape

**Abstract principle.** The pipeline is a product slice (M7), built with the same loop, after the walking skeleton can boot and run its end-to-end check. Promote is a human action. The agent prepares the candidate and the rehearsal record. A red invariant stays blocking. The implementer does not merge. Flakes are quarantined. A red check that stops a change before production is the pipeline working.

Digest: §14. Evidence: `harness-sources/12-cicd.md`.

OpenAI's first commit included CI. This digest holds the promotion rehearsal until M7 and still requires M1 to boot and to run the journey. A pipeline stub at M1 may run the fast sensors. It does not flip production.

## Job order

```text
change
  → secret push protection at git push
  → schema of docs and features.json
  → format, lint, structural fitness
  → domain unit + HTTP seam
  → boot isolated instance
  → core journeys + slice journey
  → secret scan (second look) and dependency review
  → [if triggered] fault, security cases, capacity
  → evidence bundle stored with the commit
  → audit status
  → G3 / G4
  → merge to the integration line, by a user who is not the implementer
  → release candidate
  → rehearsal: promote, core journey, roll back, core journey
  → steward approves the production environment
  → canary, then widen
```

The journey job stays on the blocking path only while it is fast and deterministic. The day it flakes, it leaves that job. `11-quality-gates.md` is the quarantine rule.

## What each job runs

| Job | Command | Blocks merge? |
| --- | --- | --- |
| Harness schema | `php artisan test tests/Feature/Architecture/HarnessSchemaTest.php` or `php scripts/check-harness.php` | Yes, from M1 |
| Pint | `vendor/bin/pint --test` | Yes |
| ESLint and types | `npm run typecheck` and the ESLint script | Yes |
| Domain boundary | `php artisan test tests/Feature/Architecture` | Yes |
| Unit and feature | `php artisan test` | Yes |
| Secret scan | the scanner the constitution names, a second time in CI | Yes |
| Dependency review | the host's dependency review on the lockfile diff | Yes when the diff changes `composer.lock` or `package-lock.json` |
| Journey | `npx playwright test e2e/core.spec.ts e2e/<feature>.spec.ts` against a booted app in the job | Yes, while deterministic |
| Metric report | the complexity command the constitution names | Blocks only after a ceiling is set. Mutation survivors on the slice block once Infection is in the job for risk-class slices |
| Evidence upload | the job uploads command output as an artifact | The upload is not the check. The exit code of the previous jobs is the check |

A file the agent writes under `docs/evidence/` is not a required status. The agent can write that file. The host's check, reported by a pinned app, is the status.

## Isolated boot in CI

The journey job creates a database for the job, runs `php artisan migrate --force`, builds assets with `npm run build`, and starts the application on a port the job owns. Playwright's `baseURL` is that origin. The job does not reuse a developer database and does not call a production URL.

`php artisan serve` is acceptable in that job. A more production-shaped PHP process is a later slice if the rehearsal needs it. The agent records which process served the journey.

Two pull requests do not share that database.

## Forge rules

GitHub rulesets are the digest's substance. GitLab's equivalent is a protected branch, pipelines-must-succeed, and a merge train. The agent configures the host the repository already uses. It does not add a second host.

Required:

- Pull request required to merge to the integration branch.
- Required status checks, each pinned to the app allowed to report it. A status from the implementer's token does not satisfy the rule. An approval from that identity, or from a GitHub App the agent can drive, does not satisfy a required review. An installation token has done so on a repository that allowed any app. The ruleset is the exclusion. A prompt is not.
- The coding agent's user is absent from the bypass list.
- Dismiss stale approvals when new commits are pushed.
- Strict checks: the branch is up to date before merge, or a merge queue re-runs the required jobs on the combined result. Concurrent slices need this. A green branch against a stale base is not the merge gate.
- Code owners on the risk-class paths in `05-roles.md`.
- "Require review from someone other than the last pusher" when the constitution's posture says a person reads.

Zero required approvals is a choice the constitution makes explicitly. The agent refuses to arrive there by leaving a default untouched. This folder's starting rule is: the implementer does not merge, and G3 has a human. That is stricter than OpenAI's mature repository and looser than AISVS AC.4.1, which wants a different human on every AI-generated diff. `05-roles.md` records the departure.

SLSA Source Level 4, if the constitution claims it, requires two trusted persons on the final revision. The coding agent is not a trusted person. A bot exception is an explicit waiver for something like a dependency bot. This folder does not grant that exception at the start. Two agent approvals are not Source Level 4.

History on the protected branch stays append-only. Force-push is off for the integration branch.

`21-unattended-runs.md` is the rule for a branch an unattended run opened. The model job does not hold the write token. A workflow that holds a secret waits for GW. The M1 stub installs no issue, clock, or failed-check starter.

## Promote

The release runner (P8) builds the candidate from the integration line and rehearses in a non-production environment:

1. Deploy the candidate.
2. Run `e2e/core.spec.ts` against that environment.
3. Roll back by the rehearsed path.
4. Run `e2e/core.spec.ts` against the previous candidate.

The steward approves the production environment. The agent does not run the production deploy command. The first production exposure is a partial canary against a control, one step at a time. A bad signal rolls back by the rehearsed path before a drive-by rewrite. A repair slice follows.

Watching budgets after a full promote is a later stage. The rollback path is the one the rehearsal used.

Migrations in that deploy are forward-only. The rollback of a bad release returns the previous application bytes. A migration that the previous bytes cannot read is not yet expanded. `10-quality-attributes.md` is the expand/migrate/contract rule. The agent refuses a contract migration in the same release as the code that stops writing the old column, unless the rehearsal proved the previous bytes still run. They will not, once the column is gone. Contract is a later release.

## After a bad canary

Roll back. Open a repair slice. The repair's allowed paths are the ones the steward names. The agent refuses a cleanup of unrelated modules in that slice.

## What blocks, restated

A red invariant check stays blocking under Read and under Metrics. OpenAI's sentence that corrections are cheap is about waiting on a human, or on an indefinite flake stall, after mechanical checks exist and throughput is high. It does not say a red domain-boundary test may land. This folder does not start at that throughput. G3 stays.

DORA's 2025 report associates higher AI adoption with higher throughput and higher instability. Extra throughput does not cancel the harm of instability. The five delivery metrics (change lead time, deployment frequency, failed deployment recovery time, change fail rate, deployment rework rate) are defined in `18-measurement.md`. They are not CI jobs. A change the journey job rejects is a successful control, not a change failure.

## Local CI repair cap

One CI run, one repair, a second CI run, then a person, is a published stop for a branch. This folder's slice cap is two verifier or auditor send-backs. The CI stop does not replace that cap, and the cap does not rewrite the CI stop. CI repair inside a send-back is the implementer reading the log and fixing the slice. The agent refuses an unbounded loop of empty commits to retrigger CI. A second red CI run on the same failure is the moment to stop and escalate if the verifier cap is already spent. If the failure is a new sensor, the progress entry quotes the log. A security-sensitive red check stops at a comment. `21-unattended-runs.md`.

## What an agent does

- Adds the fast jobs in the M1 pipeline stub: schema, Pint, ESLint, types, architecture tests, `php artisan test`.
- Pins required checks when the host is connected, in the M7 slice or earlier if the steward says the host is ready.
- Stores the rehearsal record at `docs/evidence/release/<id>.md`.
- Prints the candidate identifier (commit sha) in that record.

## What an agent refuses

- A workflow `pull_request_target` that checks out the pull request and exposes secrets, added to make the journey install packages.
- `on: issues`, `issue_comment`, `schedule`, or a label filter in a feature slice. A starter is its own slice under `21-unattended-runs.md`.
- `permissions: contents: write` on the job that calls a model.
- `continue-on-error: true` on the journey, the secret scan, or the architecture tests.
- A deploy key stored in the repository.
- The implementer identity in the GitHub environment protection bypass, or a GitLab Maintainer token in the agent's `.env`.
- `migrate:fresh` in a deploy job.
- Pointing Playwright at production during GV.

## Review checks

- Required checks name the reporting app.
- The workflow diff is a risk class and has the posture applied.
- The rehearsal record includes both journey runs and the rollback.
- Production environment protection requires the steward.
- The candidate sha matches the commit G3 reviewed.

## Traps

- GitHub Actions `permissions: write` on the default token lets a job push commits and sometimes influence checks. The workflow sets the narrowest permissions that upload artifacts and post nothing else. A job that needs `id-token` for a deploy is the release job, not the test job.
- Caching `vendor/` across branches can hide a lockfile change. The install step uses the lockfile (`composer install`, `npm ci`).
- `npm run dev` inside CI never exits. The journey job runs `npm run build` and serves the built assets. `init.sh`'s dev servers are for the local session.
- A merge queue that does not run Playwright will merge a combination neither branch ran. The required journey check is on the queue's combined commit when the journey is still blocking.
- Laravel `APP_KEY` generated inside the job is fine for CI. The rehearsal environment uses its own key from the host's secret store. The agent refuses to copy the production key into the workflow file to "make encryption work".
