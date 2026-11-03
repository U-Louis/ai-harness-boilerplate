# 5. Roles and human gates

**Abstract principle.** Roles are prompts plus permissions. The author and the judge run in different sessions. A human blocks the pipeline at intent, spec, and evidence. The constitution chooses whether a person also reads the diff. The agent does not approve or merge its own change. An agent's approval never counts as a person's.

Digest: §5. Evidence: `harness-sources/03-roles-human-gates.md`.

The four gates and the risk list are the digest's recipe. GitLab's published point that the plan is the heavy checkpoint is why G2 is expensive. AISVS AC.4.1 is stricter than G4. AC.8.1 is met when the forge, the CI system, and the registry reject the implementer's approval, merge, signature, and deploy. A sentence in this file does not meet AC.8.1.

## Who may touch what

| Role | Product code | Tests and oracles | `passes` | May approve | Session |
| --- | --- | --- | --- | --- | --- |
| Steward (human) | No | Accepts the spec | No | G1–G4 | — |
| Researcher | No | No | No | No | Fresh |
| Spec author | Spec and examples only | Drafts examples; steward accepts | No | No | Fresh |
| Planner | Plan file only | Names the checks | No | No | Fresh |
| Implementer | Allowed paths for one feature | May add cases. Leaves accepted examples, their expected values, and their steps intact | Leaves `false` | No | Fresh, one feature |
| Verifier | No | May add a failing case whose expected value is copied from the spec | Sets `true` only when every frozen step holds | Recommends | Fresh, no implementer transcript |
| Auditor | No | No | No | Pass or reject on the checklist | Fresh |
| Lens | No | No | No | Findings | Fresh, parallel, one frozen diff |
| Release runner | Pipeline files only when that is the slice | No | No | No | Fresh |
| Gardener | One behavior-preserving refactor in its own slice | Existing suite stays green | No | No | Scheduled |

The implementer identity is the git author and the forge user that pushes the implementation commits. That user is absent from the ruleset bypass list and is not a required-review approver of their own pull request.

## Paths by role

| Path | Implementer, inside the spec's allowed paths | Other writers |
| --- | --- | --- |
| `app/Domain`, `app/Http`, `app/Policies`, `resources/js`, `routes/web.php`, `database/migrations` | Yes, for this feature | Gardener, when the slice is a cleanup |
| `docs/product-specs` | Spec author, before G2. After G2, only a new spec slice the steward accepts | — |
| `docs/features.json` `steps` and `description` | Spec author | Verifier may change `passes` only |
| `tests/`, `e2e/` | Implementer adds cases at accepted seams | Verifier may add a spec-derived case |
| `docs/constitution.md`, `bootstrap/app.php`, `.github/workflows`, `phpunit.xml`, `eslint.config.js` | Only when the slice is that change and it is a risk class | Release runner for pipeline slices |
| `vendor/`, `node_modules/` | Never hand-edited | — |

## The four gates

| Gate | Steward decides | Steward looks at | If the gate is skipped |
| --- | --- | --- | --- |
| G1 Intent | Worth building, and the non-goals | One paragraph: outcome, actor, non-goals | The wrong product, built as a careful Laravel app |
| G2 Spec | Done-means, seams, which guesses are accepted | Acceptance examples, test seams, assumptions, allowed paths | Invented business rules in an action or a page |
| G3 Evidence | The frozen example still holds, and a small rule change fails it | Spec delta, command results, oracle diff, Playwright output | A test that only goes red when the route is deleted |
| G4 Posture | Apply `diff-posture` | Changed paths, the blind spot, the evidence. Under Read, the sample | Unset posture, or a merge the posture forbids |

G2 is the expensive human moment. A wrong assumption fixed there is a sentence in the spec. The same assumption fixed after `ClaimItem` exists is a new slice.

Non-goals are a hard stop at G1. The Desk illustration's non-goals (payments, notifications, attachments, multi-organization) stay out of `composer.json` and out of the pages until a later intent accepts them.

## Risk classes

Under Read, a person other than the author reads the diff, including the tests. Under Metrics, the allow case, the deny case, the evidence packet, and a play-test still happen, and the person leaves the agent diff unread.

| Risk class | Paths that trigger it |
| --- | --- |
| Authentication and authorization | `app/Policies`, `app/Models/User.php`, login and session routes, `auth` middleware, `config/auth.php`, `Authorize` calls |
| Value, quotas, irreversible user data | jobs that delete or bill, rate limiters, any code the spec marks irreversible |
| Stored shape and migrations | `database/migrations`, Eloquent `$fillable` / casts, Prop field lists |
| Oracles, linters, pipeline | `docs/product-specs`, `tests/`, `e2e/`, `phpunit.xml`, `pint.json`, `eslint.config.js`, `playwright.config.ts`, CI workflow files, `bootstrap/app.php` |
| A slice no automated check covers | the spec says the journey seam is missing |
| Production promotion | the release candidate and the deploy workflow |

AISVS Level 2 also elevates cryptography, IAM, deployment manifests, and sandbox or network policy. Those paths join the table when the product has them. Schema and migrations stay in the list because the digest keeps them.

`CODEOWNERS` names a human owner for every row. The coding-agent user is not that owner.

## Routing posture

The constitution's `diff-posture` is `read` or `metrics`.

**Read.** A person other than the author reads the change, including Pest tests and Playwright specs, when any risk-class path changed, when nobody can name what the checks would miss, and on a sample of merges that were cleared on evidence alone. The evidence record's `human-read` is `yes` or `sample`.

**Metrics.** The person sets intent and spec, reads the evidence (commands, frozen examples, metric results), and play-tests the booted system in a browser. `human-read` is `metrics`. Green CRAP or a green typecheck is not the spec.

Until the field is set, the release runner and the implementer both stop before merge.

Low-risk slices may ship behind a flag once a progressive rollout with automatic rollback exists. The one flag mechanism, when a slice needs flags, is Laravel Pennant, named in that slice's plan. Until a rollout exists, a human presses promote. The agent prepares the candidate and does not flip production traffic.

## What an agent does

- Stops at G2 when any assumption that would change authorization, a data class, or a budget is still `guess`.
- Puts the implementer forge user outside the bypass list in the same slice that adds the ruleset, if that slice is M7, or records the absence as a release blocker if the host is not ready earlier.
- Treats a lens finding as a hypothesis until the citation (constitution line or spec line) is checked.
- On the third verifier or auditor rejection, stops and hands the steward the spec path, the commit, and the command output.

## What an agent refuses

- Approving the pull request with the implementer's credentials, or with an app the agent can drive.
- Labeling an issue or merge request so another run starts, or treating that label as G2.
- Counting a Bugbot comment, a Claude review, an app approval, or a green check run as G3 or G4. `21-unattended-runs.md`.
- Using the steward's bypass of a stuck merge queue to skip a secret, an auth case, an oracle failure, a dependency-direction failure, or a mutation survivor.
- Editing `docs/constitution.md` inside a feature session to move a path out of a risk class.
- A Form Request `authorize` that returns `true` on a state-changing route "so the test can reach the action".

## Review checks

- The role of the session matches the files in the diff.
- `passes` changes only in a verifier commit.
- Risk-class paths have the posture the constitution named, recorded in `docs/evidence/<slice>/`.
- `CODEOWNERS` still covers the paths the diff touched.
- The author of the implementation commit is not the user who merged.

## Traps

- Laravel's Policy auto-discovery guesses `App\Policies\ItemModelPolicy` from the model class name. A model that lives in `app/Infrastructure/Persistence` will silently miss `App\Policies\ItemPolicy` unless `AppServiceProvider` registers `Gate::policy`. An unregistered Policy that "looks correct" is a missing allow decision. The feature test for the other actor is what makes that visible. Register the Policy explicitly.
- `Gate::before(fn () => true)` in a local service provider, or `Gate::before` left from a starter, allows every ability. The M2 slice deletes that callback. The deny test fails while it remains.
- Inertia shared `auth.can` booleans are display hints. A page that hides a button and a controller that never calls `authorize` is an allow. `authorize()` runs on the write.
- A verifier that opens the implementer's chat and then edits `ClaimItem` has left the role. Findings go back as a reject. The implementer repairs.
- GitHub "dismiss stale approvals" must be on. An agent push after a human approval invalidates it. An approval that sticks to an older commit is a stale gate.
