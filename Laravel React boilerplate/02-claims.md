# 2. Claims: sourced versus chosen

**Abstract principle.** Published claims stay labeled as claims. Where the sources disagree or name no number, the constitution records a choice. The agent does not promote a choice into a lab result, and does not promote a lab anecdote into a Laravel config value.

Digest: §2. Evidence: every file in `harness-sources/`. The choice list in the digest is the list this file binds to the stack.

## Claims that become stack behavior

These are already decisions of the digest. The agent applies them as follows.

| Claim the digest carries | What the agent does in this stack |
| --- | --- |
| Humans steer. Agents execute. A failure asks which capability is missing. | A red journey or a repeated review finding becomes a test, a lint message, or a constitution line in a follow-up slice the steward accepts. The implementer does not "try a different architecture" inside the slice. |
| A short map plus `docs/` is the record. | `AGENTS.md` stays under about 100 lines and links into `docs/`. `06-artifacts.md` is the tree. |
| Boundaries are mechanical. Local code inside them is free. | The domain-import test, the Policy, the Prop allow-list, and the repository binding are the boundaries. Naming inside an action is free. |
| The agent can boot an isolated copy and read its trace. | `init.sh` boots one app. Logs for that boot are queryable. A latency number is a gate only after the constitution fills the five SLO fields and this query works. |
| One feature per fresh session. The feature list starts failing. | `docs/features.json`. One red feature id per implementer session. |
| The walking skeleton links the main parts and stays. | M1 is a real migration, a real page, a real repository, a deployable boot, and a Playwright journey. |
| Someone other than the author reads tests when a person reviews. | Under Read, the reviewer reads the Pest file and the Playwright spec, including expected values. |
| Expected values are frozen before implementation. | They live in the spec. The test copies the literal. Infection survivors on the action are answered from that literal. |
| A feature slice cuts every layer it touches. | A slice that edits only `resources/js/Pages` or only a migration fails the plan gate. |
| The implementer does not propose a different approach after G2. | The controller, action, and page follow the plan's files. |
| A repeated mechanical miss becomes a check. A judgement stays in the reviewer file. | `12-review.md`. |
| The agent does not merge its own change. An agent's approval is not a person's. | Forge rules in `14-cicd.md`. |
| An unattended run starts from a person with write access, a clock, or a failed check. The model job does not hold the write token. A secret-bearing workflow waits for a person who did not write the triggering text. | `21-unattended-runs.md`. The M1 stub installs no starter. A later slice records the five questions before any workflow event is added. A branch push from the model job stays off until `docs/exec-plans/decisions.md` records that exception. |
| A second agent labeling work ready is not a start. | The agent does not apply that label, and does not start P3 because a label appeared. G2 stays. |
| An app's approval can satisfy a required review. | The ruleset excludes the agent's identity and any app it can drive. `14-cicd.md`. A prompt is not that exclusion. |
| A published factory stores unseen scenarios outside the repository and removes the human read. | Left uninstalled. Frozen examples stay in the spec. Metrics stays the posture in `05-roles.md`. |

## Choices the constitution still writes down

The agent leaves these fields empty until the steward sets them in `docs/constitution.md`. An empty field has the effect in the last column.

| Field | Legal values | While unset |
| --- | --- | --- |
| `complexity-score` | `crap` or `cognitive` | Report cyclomatic complexity, CRAP, and cognitive complexity. Neither blocks. |
| Complexity ceiling | a number the steward names | The score does not block. A spoken ceiling from a 2026 interview is history, recorded in the digest, and is not copied. |
| `diff-posture` | `read` or `metrics` | G4 does not merge. |
| `instruction-model` | the model name the compensatory lines target | The deletion pass has no baseline. |
| ASVS level | the level the organization adopts | M6 cannot claim a level. Every-change secret, dependency, and validation checks still run. |
| Storage engine | one of PostgreSQL, MySQL, or SQLite, as the steward confirms | M1 does not start. |
| Numeric bars | request, proportion, threshold, measurement point, window | The latency gate stays off. Page size, payload, timeout, and rate limit still fail closed once the spec names those bounds. |

## Numbers and habits the agent does not install

| Source material | Agent behavior |
| --- | --- |
| "Startup under 800 ms", "no span over two seconds" | Leave them out of `config/`. They are example prompts. They are not p95 and not an SLO. |
| CRAP ceilings 6, 8, 12, and the unused 10 | Record them as history in the evidence of the constitution slice. Block only at the ceiling the constitution names. |
| OpenAI agents squash-merging their own pull requests | The forge forbids that identity from merging. Low-friction human waits are a later constitution change after invariants exist and escapes are measured. |
| AISVS AC.4.1 (a different human reviews every AI-generated change) | The digest's Read and Metrics postures are a recorded departure. The agent does not stamp a pull request "AISVS Level 1" while that departure stands. |
| AC.4.2 (SAST, IAST, DAST, secret scan, IaC scan, SCA on every AI pull request) | Claim it only when the pipeline actually runs that set. M1 runs secret scan, named dependencies, and the allow and validation cases. |
| A global coverage percentage | Coverage is a map of unexecuted lines in the slice. `phpunit.xml` does not set a failing coverage threshold. |
| A global mutation score | Infection runs filtered to the slice's action. Survivors are listed. There is no MSI gate. |
| DORA's 1.5% and 7.2% | They are survey associations. They are not a per-module budget in `config/desk.php`. |
| SWE-bench "resolved" | A green `php artisan test` is not maintainer acceptance. `AUDIT_PASS` means the checklist rows were green. |
| Spec Kit's foundation-then-models-then-services-then-endpoints order | The build unit stays a vertical slice. `07-outer-workflow.md`. |
| Anthropic's November 2025 coding agent setting `passes` | The verifier sets `passes`. The implementer leaves it false. |
| Two send-backs, then the steward | This is the digest's cap. The agent enforces it. The agent does not describe it as a number a lab published. A second CI run, then a person, is a published stop for one branch. It does not replace the cap. |
| A daily token-spend figure, or a satisfaction fraction, from a factory that removes human review | Left out of `config/`. They are not a budget and not a gate. `21-unattended-runs.md`. |

## What an agent does

- When a slice needs a number, it copies the number from the accepted spec or the constitution.
- When the spec says "unchanged" for a budget, it leaves the config key unchanged.
- When it cites a reason in a pull request, it names the digest section and, if the rule is a local choice, it says so in `docs/exec-plans/decisions.md`.

## What an agent refuses

- Writing `800`, `2000`, `6`, `8`, `12`, `10`, or `30` into a complexity or latency config because a source mentioned them.
- Marking `diff-posture` as `metrics` in order to skip a review the constitution still has unset.
- Adding `composer.json` scripts that encode a step order for an older model ("always dump the whole route list before editing"). That line is a compensation. `19-evolvability.md` owns it.
- Claiming the pipeline meets an OWASP level it does not run.

## Review checks

- `docs/constitution.md` fields match the table above.
- `config/desk.php` contains only bounds a spec or the constitution named.
- The pull request body does not treat a green suite as AISVS compliance.
- A new check's message states the remedy and names the rule it enforces.

## Traps

- Laravel's debug error page and React's overlay are development sensors. They are not the product error shape, and they are not evidence that a failure was handled.
- Pest's `expect()->toBeTrue()` on a hard-coded `true` satisfies "the test ran". It does not satisfy the frozen-example rule.
- A starter's `phpunit.xml` coverage threshold, if one is present, is removed or set so it does not fail the build. The slice's unexecuted lines are what the evidence records.
