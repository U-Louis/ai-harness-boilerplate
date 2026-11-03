# 11. Quality gates

**Abstract principle.** A gate is a named check with a verdict, an owner, and a consequence. Guides steer before the agent acts. Sensors observe after, and the useful ones say how to comply. A check blocks when it is objective, fast, and reliable, and when the authoring agent cannot satisfy it by editing the checker or posting the status. The implementer cannot waive a hard gate. Human merge friction is what a high-throughput repository may remove later. A red invariant stays blocking under both routing postures.

Digest: §11. Evidence: `harness-sources/09-quality-gates.md`, `harness-sources/15-uncle-bob.md`. GW is specified in `21-unattended-runs.md` and `harness-sources/17-unattended-runs.md`.

The linter is both the constraint and the feedback, and it is present from the first commit. The adoption order is not "constraints, then feedback, then gates" as a vendor slogan. Boundaries are on at M1. Human waits shrink only after the constitution says the repository has earned it. This folder's starting posture still has G3, and still refuses the implementer's merge.

## Constraint layer (feedforward)

These are available before the agent writes. They do not themselves fail the process.

- `AGENTS.md` points at `docs/constitution.md` and the current spec.
- The slice's allowed paths.
- Ask first: a new dependency, a new stored field, a new entry point, a retry policy.
- Refuse in the slice: weaken an oracle, add a second auth path, commit a secret, edit an unrelated module.

## Feedback layer

The implementer runs these and reads a remedy in the failure text.

| Check | Command | Remedy the failure must state |
| --- | --- | --- |
| PHP format | `vendor/bin/pint --test` | Run `vendor/bin/pint` on the dirty files and re-run |
| JS format and lint | the ESLint command in `package.json` | Name the rule and the edit that satisfies it |
| Domain edges | `php artisan test tests/Feature/Architecture/DomainBoundaryTest.php` | Move the import to Http or Infrastructure |
| Controller queries | `php artisan test tests/Feature/Architecture/ControllersDoNotQueryTest.php` | Call the action |
| HTTP seam | `php artisan test tests/Feature/<slice>` | Match the frozen `code`, props, and row |
| Journey | `npx playwright test e2e/<feature>.spec.ts` | Make the booted page show the frozen step |
| Types | `npm run typecheck` | Change the Prop class and `types.ts` together |
| Secret scan | the scanner named in CI | Remove the secret and rotate it |
| Metric gates | below | Below |

Inline suppression is off. ESLint flat config sets `linterOptions: { noInlineConfig: true }` and `reportUnusedDisableDirectives: 'error'`. A second rule that bans `eslint-disable` comments does not close the hole, because the directive is applied first. The switch is `noInlineConfig`. PHP has no equivalent switch for every tool. The agent refuses `// pint-ignore`, `phpcs:disable`, `@phpstan-ignore`, and `/* istanbul ignore */` unless the slice is a constitution change that names the line. A change to `pint.json`, `eslint.config.js`, `phpstan.neon`, or `phpunit.xml` is a risk class. G4 applies the posture.

"Violation detected" is not a finished sensor.

The implementer's green self-check is this loop. The merge gate is a required check on the combined result, reported by a pinned app, with the implementer absent from the bypass list.

## Metric gates

Until `complexity-score` and a ceiling are set, the self-check records cyclomatic complexity, CRAP, and cognitive complexity, and neither score blocks. Green scores do not prove the spec. The verifier does not treat them as the oracle. The steward still does G3.

Any tool that emits the named score is allowed. The constitution names the tool so two sessions run the same command. Bindings that exist and are enough:

| Score | PHP | TypeScript |
| --- | --- | --- |
| Cyclomatic complexity (path count) | PHPMD `CyclomaticComplexity` | ESLint rule `complexity` |
| CRAP, `CC² × (1 − coverage)³ + CC` | the CRAP reporter the constitution names (phpmetrics is a known reporter) | the same formula from the ESLint complexity number and the coverage map, if the constitution chooses a TS gate |
| Cognitive complexity | the Sonar metric, or any tool that emits it | `eslint-plugin-sonarjs` rule `cognitive-complexity` |
| Mutation on the slice decision | Infection, `vendor/bin/infection --filter=ClaimItem` | Stryker, filtered to the changed component, only when the spec's decision lives in TypeScript. Domain decisions are mutated in PHP |
| Dependencies | `DomainBoundaryTest` and `ControllersDoNotQueryTest` | ESLint `no-restricted-imports` forbidding `axios` and a raw `fetch` of `/api` in `resources/js` |
| Near-duplicates | the reporter the constitution names (`jscpd` is a known reporter) | the same run includes `resources/js` |
| Zero-assertion examples | `beStrictAboutTestsThatDoNotTestAnything="true"` in `phpunit.xml` | Playwright tests that lack an `expect` fail the verifier's read of the spec |

The agent records the command and the version in the evidence. It does not install a repository-wide coverage percentage or a mutation MSI.

### When a score blocks

**CRAP,** once the constitution names that score and a ceiling. Measured on each changed function. Blocks at or above the ceiling. The failure names the function, the path count, the coverage, and the score, and tells the agent to add a spec-derived assertion for the untested decision or to split the function. Deleting tests is not a repair.

**Cognitive complexity,** once the constitution names that score and a ceiling. The failure names the function and the score, and tells the agent to flatten the control flow. Deleting tests is not a repair. This folder sets no number.

**Mutation.** A survivor in the code this slice changed blocks. There is no score percentage. The failure names the edit (flipped comparison, flipped constant) and tells the agent to add an assertion whose expected value comes from the spec. Asserting the code's current return value does not clear it. An equivalent mutant needs a reason in the evidence, and that reason still does not pass the frozen-example check at G3.

**Dependencies.** A forbidden import or a cycle blocks. The failure names the edge. Adding the edge to the allow-list is a constitution slice. Instability, abstractness, and distance-from-the-main-sequence may be reported. They do not block. A picture of the layers is not the block.

**Near-duplicates.** A reported pair with no answer fails the self-check. The answer extracts the shared behavior or says why the two forms are different rules. A tool default such as 0.82 is not a cutoff this folder adopts.

**Zero-assertion.** An example that asserts nothing fails. An `expect(true)->toBeTrue()` still needs the frozen-example rule. Strictness about empty tests is the floor.

## Enforcement

| Id | Gate | Start | Hard? | Owner | Consequence |
| --- | --- | --- | --- | --- | --- |
| G1 | Intent complete | yes | hard | Steward | Research stays questions |
| G2 | Spec schema valid, guesses resolved or accepted, examples and seams frozen | yes | hard | Steward | No plan |
| GP | Plan reuses Policy, repository, error, log. Feature cut is a tracer bullet or a named exception | yes | hard | Planner output; steward if a new mechanism appears | No implementation |
| GO | Core journey green | yes | hard while the command stays fast and deterministic | Implementer, Playwright | Repair before new work |
| GI | Self-check commands green | yes | hard as feedback; merge is the pinned re-run | Tool | No verify |
| GM | Metric gates | yes | hard for dependency violations, cycles, and mutation survivors once they apply. Complexity blocks only after a named ceiling. A duplicate needs an answer | Tool | No verify |
| GV | Each done-means line evidenced against frozen examples | yes | hard | Verifier | Back to implementer, two send-backs |
| GL | Lens findings triaged | after GV is clean | soft for low, hard for high | Auditor | High findings block |
| GA | Auditor checklist re-run | yes | hard | Auditor | Reject with evidence |
| G3 | Frozen example fails a small rule change | yes | hard | Steward | No merge |
| G4 | Routing posture | yes | hard | Steward | Unset does not merge |
| GS | Secret, dependency, auth cases on the §10 triggers | yes, when triggered | hard | Tool and verifier | No merge |
| GC | Capacity and efficiency budgets | M6 and hot paths | hard at M7, soft before | Tool | No promote |
| GR | Release rehearsal and rollback | M7 | hard | Release runner and steward | No promote |
| GG | Doc and `features.json` schema | yes | hard | Tool | No session start |
| GW | Secret-bearing workflows on a branch an unattended run opened | when the start is an issue, a comment, a clock, or another run | hard | A person who did not write the triggering text | No workflow that holds a secret |

A journey that flakes comes off the hard path that day. Quarantine it in a list the constitution names (`e2e/quarantine.md` plus removing it from the required CI job) and fix it in a slice. The agent refuses to re-run a flake until it is green and then call that evidence.

**High finding:** cross-actor access, data loss, oracle weakened, unbounded read, secret, pipeline permission widened.

**Low finding:** naming, local structure, missing commentary. Pre-existing nits become gardener work. New complexity this slice added blocks unless the steward defers it with a tracked feature id.

The implementer cannot waive a hard gate, forge a required status, or sit on the bypass list. A downed check pauses the gate. A named steward may bypass a stuck combined-result queue on a recorded pull request, and may not use that bypass to skip a secret, an auth case, an oracle failure, a dependency violation, a cycle, or a mutation survivor.

GW is the wait in `21-unattended-runs.md`. The sensor is the workflow file and the environment protection rule. A job that maps a secret into the PHP process on such a branch fails review when that environment has no required reviewer, or when the only reviewer wrote the triggering text. The M1 stub has no such job. A later starter adds the environment in the same slice as the trigger.

## Audit checklist

The auditor fills pass or fail on the latest tree. `AUDIT_PASS` means the rows were green.

```text
Requirements        pass/fail
Scope paths         pass/fail
Architecture edges  pass/fail
Metric gates        pass/fail
Error behavior      pass/fail
Security cases      pass/fail
Edge cases          pass/fail
Oracles intact      pass/fail
Tests               pass/fail
Boot                pass/fail
Prior journeys      pass/fail
Budgets (if due)    pass/fail
Docs touched        pass/fail
Rollback named      pass/fail
```

Each `pass` line in `docs/evidence/<slice>/audit.md` cites the command. A row the auditor cannot run is `fail`, not a copy of the verifier's sentence.

## Schema gate

A small committed script `scripts/check-harness.php` (or a Pest test) fails when:

- `docs/features.json` is missing a required key
- a product spec is missing a required heading
- `AGENTS.md` exceeds the line budget the constitution sets (default 120, so the digest's "about 100" has room for the boot commands)

The failure names the file and the field. GG runs this at session start and in CI.

## What an agent does

- Runs the feedback layer during the slice, not only at the end.
- Writes remedy text into any new test's failure message (`$this->fail('Domain file imports Eloquent. Move the query to EloquentItemRepository.')` or an assertion message).
- Keeps a new check off the blocking CI job until it is fast and has been seen green on the existing tree, unless the tree is empty and the check is a boundary M1 fixes everywhere.

## What an agent refuses

- `eslint-disable` after `noInlineConfig` is on, by turning the option off in the same pull request.
- A PHPUnit warning suppression for a test that asserts nothing.
- Marking a flaky Playwright spec `test.fixme` and merging the feature it was supposed to prove.
- Lowering an Infection filter or deleting a mutant by changing production code to a no-op that returns the fixture.
- Publishing a commit status from the implementer's token.
- An `on` clause for issues, comments, a schedule, or a ready label inside a feature slice.
- An environment with no required reviewer on a job that reads `secrets`.

## Review checks

- The required CI jobs are pinned to the app that reports them.
- The implementer is absent from bypass actors.
- New lint rules fail the process (`error`, Pint non-zero), or they are explicitly reported and non-blocking with a promotion date.
- GM output is in the evidence for the slice.
- The checklist has a command per pass row.
- GW is satisfied when a starter exists: the secret-bearing job names an environment whose reviewer did not write the triggering text. No starter means the workflow has none of those triggers.

## Traps

- ESLint `overrides` in a nested config re-enable inline comments for `resources/js`. One flat config at the repository root is the sensor. A package-local config that sets `noInlineConfig: false` is a risk-class edit.
- Infection will time out on a full-suite run. The command filters to the action this slice changed. A full-suite mutation percentage is the gate this folder does not install.
- PHPMD and ESLint `complexity` count different functions than a human's "this method feels large". The constitution's tool is the one that blocks. A second tool's complaint is a low finding unless the constitution named it.
- `php artisan test --parallel` can fail the domain-boundary scan if the test reads the working tree while Pint rewrites it. CI runs Pint first, then tests.
- A green `composer audit` does not replace the spec line that names a new dependency. The audit is the scanner. The spec line is the human decision to take the dependency.
