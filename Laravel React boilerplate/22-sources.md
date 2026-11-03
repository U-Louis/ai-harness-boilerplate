# 22. Sources and tool bindings

**Abstract principle.** The digest's bibliography stays in `Abstract boilerplate/Abstract-boilerplate.md` §22. The notes in `Abstract boilerplate/harness-sources/` (01 through 17) are the check for conflicts and for claims that were not carried into the digest. This file binds those rules to commands and files. It does not add sources.

Digest: §22.

When a tool and a rule disagree, the rule wins and the tool is replaced with one that can fail the run. The agent does not weaken the rule to fit a starter-kit default.

## Bindings

| Digest rule | Binding in this stack | File that specifies it |
| --- | --- | --- |
| Short map, deep `docs/` | `AGENTS.md`, `docs/constitution.md`, `docs/product-specs/` | `06-artifacts.md` |
| Feature list starts failing, verifier sets `passes` | `docs/features.json` | `06-artifacts.md`, `08-inner-loop.md` |
| Boot the next session can find | `init.sh` | `06-artifacts.md` |
| Domain independent of delivery and storage | `tests/Feature/Architecture/DomainBoundaryTest.php` | `09-modules.md` |
| One storage path | One `bind` in `AppServiceProvider` | `09-modules.md` |
| Authorization in a trusted layer | `app/Policies`, Form Request `authorize` | `05-roles.md`, `10-quality-attributes.md` |
| Field allow-list on a status read | `app/Http/Props/ItemStatusProps.php`, `->missing('item.notes')` | `10-quality-attributes.md`, `13-testing.md` |
| Input validated on the server | Form Request `rules`, error tokens | `10-quality-attributes.md` |
| One error shape | `bootstrap/app.php` renderer, flash `code` and `feature_id` | `10-quality-attributes.md` |
| One log shape and a correlation id | `AssignCorrelationId`, `Log::shareContext` | `10-quality-attributes.md` |
| Journey is the done-check for a page | Playwright `e2e/` | `13-testing.md` |
| HTTP seam | Pest feature tests, `php artisan test` | `13-testing.md` |
| Frozen literal, mutation on the decision | Spec examples, Infection `--filter` on the action | `11-quality-gates.md`, `13-testing.md` |
| No empty tests | `beStrictAboutTestsThatDoNotTestAnything` | `11-quality-gates.md` |
| Format and lint the agent cannot suppress | `vendor/bin/pint --test`, ESLint `noInlineConfig: true` | `11-quality-gates.md` |
| Complexity score | The tool the constitution names. PHPMD cyclomatic and ESLint `complexity` are valid path-count sensors. CRAP and cognitive complexity block only after a ceiling is set | `11-quality-gates.md` |
| Near-duplicates need an answer | The reporter the constitution names. `jscpd` is a known option. No 0.82 cutoff | `11-quality-gates.md` |
| Secret scan on push and in CI | The host's push protection plus the CI scanner | `14-cicd.md` |
| Required checks pinned, implementer cannot merge | GitHub rulesets or the GitLab equivalent | `14-cicd.md` |
| Unattended start is a person, a clock, or a failed check | No starter in the M1 workflow. A later slice records the five questions in `docs/exec-plans/decisions.md` | `21-unattended-runs.md` |
| Model job does not hold the write token | That job's `permissions` are `contents: read`. A later job applies a structured write | `21-unattended-runs.md` |
| GW: secret-bearing workflows wait | Environment protection on jobs that inject secrets. The approver did not write the triggering text | `21-unattended-runs.md`, `11-quality-gates.md` |
| N+1 fails in testing | `Model::preventLazyLoading(true)` in local and testing | `10-quality-attributes.md` |
| Process refuses to start without required config | `app/Infrastructure/Boot/RequiredConfiguration.php` | `10-quality-attributes.md` |
| Harness schema | `scripts/check-harness.php` or `HarnessSchemaTest` | `11-quality-gates.md` |
| Evidence is the command, the version, and the result | `docs/evidence/<slice>/` | `06-artifacts.md` |

## Commands the evidence may cite

These commands are stable on the versions `README.md` pins. The evidence records the version the lockfile actually installed.

```text
./init.sh
php artisan test
php artisan test tests/Feature/Architecture
php artisan test tests/Feature/Items/<Name>Test.php
vendor/bin/pint --test
vendor/bin/infection --filter=<ActionClass>
npm run typecheck
npx playwright test e2e/core.spec.ts
npx playwright test e2e/<feature>.spec.ts
```

`npm run typecheck` is `tsc --noEmit`, defined in `package.json` by the M1 slice if the skeleton omitted it.

Infection and a CRAP reporter are added in the slice that first makes GM blocking for that sensor. They are not required to exist on the M0 document-only tree. Until they exist, the evidence says the sensor is absent, and a risk-class slice does not pretend the survivor list was read.

## What this folder does not bind

- A specific SaaS APM vendor. The log line is the trace until the constitution names a query tool.
- Laravel Telescope, Horizon, Pulse, or Pennant. Each is a dependency a slice's plan must name. Pennant is the flag mechanism when flags are needed. The others stay out until a spec asks.
- A global coverage gate, a global MSI, a latency of 800 ms, or a CRAP ceiling of 6, 8, 10, or 12.
- Sanctum personal access tokens, Passport, or a SPA origin for the first-party UI.
- An issue, clock, or failed-check starter. `21-unattended-runs.md` is the rule for a later slice. The binding above is the absence of that starter on the M1 tree.
- A token-spend figure or a satisfaction fraction as a gate.

## What an agent does

- Uses the binding in the table when a digest rule needs a place to live.
- Records the installed version next to the command when the check is evidence.
- Adds a tool in a slice whose plan names the dependency, and wires it to the rule in the same slice.

## What an agent refuses

- A new tool that duplicates a binding (a second architecture-test package beside `DomainBoundaryTest`, a second linter beside Pint and ESLint) without retiring the first in a constitution slice.
- Citing `harness-sources/` as if those research notes were product requirements. The digest already chose which claims became rules. `02-claims.md` is the list. `harness-sources/17-unattended-runs.md` is the evidence for §21. The rule the agent executes is `21-unattended-runs.md`.
- Inventing a scanner flag these files do not contain. The constitution names the scanner's real command after the steward confirms the binary.

## Review checks

- Every new CI job maps to a row in the table, or the plan explains the new row and the constitution accepts it.
- `composer.json` `require-dev` changes match a binding that was missing.
- The evidence command is one of the commands above, or a constitution-named replacement.

## Traps

- The Laravel skeleton's `composer test` script may be `php artisan test` already. A second script named `test:all` that silently skips Playwright will be the command agents run. `AGENTS.md` lists Playwright separately so the shorter script is not mistaken for the journey.
- `vendor/bin/phpunit` bypasses Pest's `uses()` configuration if the suite was written for Pest. The command is `php artisan test`.
- A browser test helper inside Pest, if the installed Pest version ships one, is an alternative journey runner only after the constitution names it and the evidence shows it driving the booted origin. This folder's named runner is Playwright. The agent does not switch runners inside a feature slice.
