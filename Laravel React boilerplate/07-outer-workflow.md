# 7. Outer workflow, from a glimpse to the pipeline

**Abstract principle.** The build moves from a glimpse to a release candidate in a fixed stage order. A stage starts when its input artifact validates. After the feature list exists, work proceeds one vertical slice at a time. The sequence is the digest's synthesis.

Digest: §7. Evidence: `harness-sources/05-outer-workflow.md`.

Vertical slice means one user-visible capability through the page, the route, the Form Request, the Policy, the action, and the store. Spec Kit's published task order (a horizontal foundation, then models, then services, then endpoints) is the counter-pattern. This folder follows the digest.

## Stage order

```text
glimpse
  → G1 intent
  → research the constraints (no code)
  → constitution + architecture map
  → feature list (all red)
  → walking skeleton
  → each later module
  → quality-attribute slices
  → release candidate
  → G4 promotion
  → observe, then gardener
```

| Stage | Prompt | Human | Artifact | Hard stop if |
| --- | --- | --- | --- | --- |
| 0 Glimpse | P0 | G1 | `docs/intent.md` | Outcome or non-goals missing |
| 1 Frame | P1 | G2 on the constitution | `docs/constitution.md`, `architecture.md`, `security.md`, `reliability.md` | Invariants or allowed edges missing |
| 2 Enumerate | P1 | Steward scans for invented scope | `docs/features.json` | Entries are tasks ("add a Policy") rather than observable behavior |
| 3 Slice | P1 → P2 → P3 → P4 → P5 → P6 | G2, then G3 and G4 | spec, plan, code, evidence, `passes` | `08-inner-loop.md` |
| 4 Harden | P3 and P4 on evidence slices | G3 and G4 | security, capacity, efficiency evidence | The release-level set is missing or red |
| 5 Release | P8 | G4 promote | pipeline run, rollback proof | Core journey red, or the posture is unset |
| 6 Garden | P7 | Evidence, unless behavior changes | cleanup slices, grades | A cleanup changes behavior |

Stages 0–2 run once per product, and again when the intent changes. Stage 3 repeats. Stage 6 is a cadence, not a step inside a feature session.

## What each stage is allowed to create

### Stage 0 and 1, no product behavior

The researcher lists actors, outcomes, non-goals, and questions. The researcher does not choose PostgreSQL versus MySQL, Inertia versus an SPA, or a component library. Those choices are steward decisions recorded as `confirmed` assumptions.

The constitution slice writes documents. It does not run `laravel new`, `composer require`, or `npm install`.

### Stage 2, the red list

The spec author writes `docs/features.json` from the accepted intent. Every `passes` value is `false`. The steward reads the list for behavior the glimpse did not ask for (notifications, teams, admin dashboards, API tokens). Invented entries are removed before M1.

A feature description a person can demonstrate in the browser is legal. "Install Pest" is a plan step of M1, not a feature.

### Stage 3, slices

M1 is the first slice that creates the Laravel application. The plan names the exact install command the steward confirmed, including the version constraint for `laravel/framework` ^12. The agent refuses an interactive installer. After install, the tree matches `README.md`. Routes the installer added that the spec does not name are deleted in this slice, or written into `docs/exec-plans/decisions.md` as a temporary seam with a deletion task id.

Later slices do not re-run the installer.

### Stage 4, harden

M6 adds no user feature. It fills `docs/evidence/M6/` with the ASVS level the constitution chose, the trace's round-trip counts, and the load scenario. A miss opens a repair slice.

### Stage 5, release

M7 is the pipeline in `14-cicd.md`. The rehearsal promotes in a non-production environment, runs the core journeys, rolls back, and runs them again.

### Stage 6, garden

One deviation per slice. The journeys stay green. A cleanup that changes a status code, a prop, or a column is a feature and goes through G2.

## Dependency rule

A stage starts when its input artifact validates against `06-artifacts.md`.

Research questions may run in parallel. Implementation of two slices may run in parallel only when their allowed paths do not overlap. Each writer uses a separate working tree (`git worktree`). One checkout, two implementers, is refused.

Lenses on one frozen diff always run in parallel. They share the commit id. They do not share a writable tree.

Module order inside stage 3 is `09-modules.md`. M4 does not start while M2 or M3 is graded D. M1 may sit at C. M7 does not promote below B.

## Install, and what the first commit contains

The M1 plan's first commit, or the commit the plan marks as the skeleton, contains:

- the Laravel 12 skeleton trimmed to the directory contract
- Inertia's root view `resources/views/app.blade.php` and the Vite entry
- Pest, `phpunit.xml` with `beStrictAboutTestsThatDoNotTestAnything="true"`
- `init.sh`
- one migration, one action, one route, one page, one journey
- the domain-boundary test
- `docs/` with the constitution and the red feature list

CI configuration may be a stub that runs Pint, the domain-boundary test, and the feature tests, because the skeleton has to be buildable. The promotion rehearsal waits for M7. A stub that cannot deploy and cannot run the journey is not yet M1, on the Freeman and Pryce bar the digest uses. If the host cannot deploy at M1, the plan records that limit in the evidence, and M1 still boots through `init.sh` and runs the journey against that boot. The agent does not call the stub a finished pipeline.

## What an agent does

- Checks the previous stage's artifact before writing.
- Puts invented scope in front of the steward at stage 2 instead of coding it.
- Names blocked-by ids in the plan and waits when a path overlaps an open slice.

## What an agent refuses

- `laravel new` during the constitution session.
- A feature entry whose steps are file names.
- Implementing M4 because the list page is "just a query" while claim and close are still red, unless the steward reordered the modules in the constitution.
- Adding Passport, Sanctum token issuance, Socialite, Cashier, or a notification channel in M1 because the starter offered it.
- Parallel edits to `routes/web.php` from two working trees. That path overlaps. One writer owns it for the slice, or the plan splits route files by context and the constitution accepts the split.

## Review checks

- Stage 0–1 diffs contain no `app/` changes.
- `features.json` is entirely `passes: false` at the end of stage 2.
- Each stage 3 plan has a cut through page, route, request, policy, action, and storage, or a named exception.
- Extra starter routes are gone or listed as a dated seam with a deletion id.

## Traps

- The official React starter may include login, registration, profile, and appearance settings. Those are product behavior. If M2 is identity, the plan either adopts those pages as the M2 features and the steward accepts that scope at G2, or M1 removes them and M2 adds the sign-in the spec describes. Leaving them in the tree untested makes the next session treat them as the authorization path.
- `php artisan install:api` creates `routes/api.php` and token scaffolding. That is a second client. The agent refuses the command unless the accepted spec is the neighbor-contract slice.
- Breeze, Jetstream, Fortify, and a hand-rolled login are four mechanisms. The constitution names one. A slice does not add another "just for the test".
- Horizontal "set up the repository layer" slices with no page fail the cut. The repository is born inside the first feature that reads or writes.
