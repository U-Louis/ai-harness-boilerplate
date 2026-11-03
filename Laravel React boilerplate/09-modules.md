# 9. How to build each module

**Abstract principle.** A module is a set of slices that share one reason to exist. Each module is exercisable through public behavior before a later module depends on it. Dependency direction, validation at the trusted service layer, and secret checks are on from the skeleton. The order M0–M7 and the A–D grades are the digest's. They are not an OpenAI standard.

Digest: §9. Evidence: `harness-sources/07-modules-and-fitness.md`.

The illustration is Desk: a person submits an item, a member claims it, a member closes it, a status can be read. `15-worked-example-desk.md` names the classes. Any other product keeps the module types and replaces Desk.

## Where code goes

```text
app/Domain/Desk/Item.php
app/Domain/Desk/Actor.php
app/Domain/Desk/SubmitItem.php
app/Domain/Desk/ClaimItem.php
app/Domain/Desk/CloseItem.php
app/Domain/Desk/ListOpenItems.php
app/Domain/Desk/ItemRepository.php          interface
app/Domain/Desk/Exceptions/ItemException.php
app/Http/Controllers/Items/SubmitItemController.php
app/Http/Controllers/Items/ShowItemController.php
app/Http/Controllers/Items/ClaimItemController.php
app/Http/Controllers/Items/CloseItemController.php
app/Http/Controllers/Items/ListItemsController.php
app/Http/Requests/SubmitItemRequest.php
app/Http/Requests/ClaimItemRequest.php
app/Http/Requests/CloseItemRequest.php
app/Http/Requests/ListItemsRequest.php
app/Http/Props/ItemStatusProps.php
app/Policies/ItemPolicy.php
app/Infrastructure/Persistence/Eloquent/ItemModel.php
app/Infrastructure/Persistence/EloquentItemRepository.php
app/Providers/AppServiceProvider.php        binds ItemRepository to EloquentItemRepository
resources/js/Pages/Items/Create.tsx
resources/js/Pages/Items/Show.tsx
resources/js/Pages/Items/Index.tsx
```

A new context (`Billing`, `Identity`) gets its own `app/Domain/<Context>` and `app/Http/Controllers/<Context>`. It does not grow a `Services` folder that every context shares. Shared code exists when a second slice would copy a rule. The gardener extracts it. A feature slice does not create `app/Support/helpers.php`.

`app/Models/User.php` stays the session record. Domain code receives `Actor`, a value object with the user's id. The action does not type-hint `User`.

## Allowed edges

The digest's Desk edges are the hexagonal ones. In this tree:

| From | May depend on | Must not depend on |
| --- | --- | --- |
| `app/Domain` | other domain types in the same context, PHP scalars | `Illuminate\Http`, `Inertia`, `Illuminate\Database\Eloquent`, facades, `app/Http`, `resources/js` |
| `app/Http` (controllers, requests, props) | domain actions and interfaces, Policies, Prop classes | Eloquent models, `DB::` |
| `app/Policies` | the domain object or the model id the provider registered | a second copy of the lifecycle rule that `ClaimItem` already owns |
| `app/Infrastructure/Persistence` | Eloquent, the domain interface and entities | controllers, React, Inertia |
| `resources/js` | prop types, `useForm`, `<Link>` | a duplicated state machine, `fetch` to a product API |

`AppServiceProvider` is the composition root. It is the one place that names both the interface and the Eloquent class.

Lifecycle rules (open, held, closed) live in the action and the conditional update. "Who may attempt this" lives in the Policy. Those are two sentences in the spec, one mechanism each. A Policy that also checks `state === 'open'` and an action that also checks a role name have crossed. The plan assigns each rule once.

The fitness test `tests/Feature/Architecture/DomainBoundaryTest.php` scans `app/Domain` recursively and fails when a file contains a forbidden import. The failure lists the file and the import, and tells the agent to move the framework call to `app/Http` or `app/Infrastructure`.

A second test `ControllersDoNotQueryTest.php` fails when a controller file contains `ItemModel`, `DB::`, or `::query(`. The remedy sentence tells the agent to call the action.

## Module order

| Order | Module | Why this order | Unlocks | What the slice adds in this stack |
| --- | --- | --- | --- | --- |
| M0 | Constitution | Edges and bars before speed | Plan alignment | `docs/` only |
| M1 | Walking skeleton | Submit and read-back, deployed or at least booted and journey-tested | Boot, journey, pipeline stub | Laravel 12 + Inertia + React, one table, `SubmitItem`, `ShowItem`, one seeded member if identity is still M2 |
| M2 | Identity and access | Later writes need an actor | Sessions, two members | Login through the page, `auth` middleware, Policy, deletion of the seeded-member seam |
| M3 | Core state changes | Claim and close, with authorization | The lifecycle | `ClaimItem`, `CloseItem`, conditional update, allow and deny |
| M4 | Query | List and filter on the same auth and the same store | Read model, page size | `ListOpenItems`, `Index.tsx`, `per_page` rule |
| M5 | Operability | Errors, limits, audit, correlation | Debuggability and abuse bounds | `AssignCorrelationId`, log shape, rate limiter, audit record the spec names |
| M6 | Quality attributes | Release-level security plus efficiency and traffic evidence | A candidate that is more than one happy path | Evidence slices, no new page unless a check needs a fixture |
| M7 | Release | Repeatable build, promote, rollback | CI/CD end | Workflow, rehearsal, canary plan |

Dependency direction, Form Request validation, and the secret scan are present from M1. They do not wait for M6.

M4 waits for M3 when a list would freeze the wrong lifecycle. M6's load scenario waits for M1 because there is nothing to measure. M2 is not last: every slice after identity calls the one Policy.

## Inside a module

1. At session start, the core journeys of earlier modules. A red journey is repaired before new work.
2. The smallest new end-to-end behavior, happy path and one refusal, in the same slice. The digest pairs the refusal into the first slice. The Elephant Carpaccio sample put a refusal later. This folder follows the digest.
3. Boundary cases (empty, duplicate, conflict, timeout) as their own features, still vertical.
4. The same core journeys again before the session commits.

## What "built" means

- Every feature in the module is `passes: true` with evidence, or deferred in the spec with a steward decision.
- Earlier core journeys still pass.
- The dependency tests still pass.
- No new secret, no new repository binding, no new error shape, unless the constitution changed in its own slice and was reviewed.
- `docs/quality-score.md` has a grade for the module, including the gaps.

## Grades

| Grade | Meaning in this stack |
| --- | --- |
| A | Edges hold, journeys pass, filled budgets have evidence, no known high gap |
| B | Journeys pass, one known gap with a tracked feature id |
| C | The demo path works. Budgets or auth are not evidenced |
| D | The next module does not build on this one |

M1 may ship at C inside the repository. M7 does not promote anything below B. M2 and M3 are A or B before M4, because a list on a weak Policy copies the weakness into every row.

The grade is a judgement for the gardener. It is not a fitness function. The domain-boundary test is the fitness function. The agent does not block a merge on a letter. The agent does block a merge on a red edge.

## Quality against the rest of the code

| Check | Question | When | Command or place |
| --- | --- | --- | --- |
| Journey regression | Do already-green features still pass? | Start and end of every session | `npx playwright test e2e/core.spec.ts` |
| Dependency fitness | Do edges hold? | Every slice, CI | `DomainBoundaryTest`, `ControllersDoNotQueryTest` |
| Plan alignment | Did it reuse Policy, repository, error, log? | Plan gate and verifier | The plan's "reused mechanism" lines, and a diff that adds a second one fails review |
| Contract at the boundary | Do neighbors still see the same shapes? | A slice that touches a boundary | The neighbor's examples, when a neighbor exists. Otherwise the HTTP seam |
| Data fitness | Is new stored data classified and validated on the server? | Any write | Spec field plus Form Request rules plus Prop allow-list |
| Budget fitness | Does the named bar hold, and are page size, payload, and fan-out bounded? | Hot path and M6 | `10-quality-attributes.md` |
| Oracle fitness | Were accepted steps or assertions weakened? | Every slice | Diff of `docs/product-specs` and expected literals |
| Grade | What is still known-bad? | Auditor, then gardener | `docs/quality-score.md` |

A slice can be locally green and still fail the module gate when a row is red.

## Temporary seams

M1 may authenticate a single seeded member when M2 has not shipped. The plan says so. `docs/exec-plans/decisions.md` records the seam and the deletion feature id. The seeder is `database/seeders/TemporaryMemberSeeder.php`. M2's first implementer session deletes that class, deletes the call in `DatabaseSeeder`, and runs F1 under a real session. The verifier searches the tree for the seeder name.

A fake `Actor` inside a domain unit test is the domain seam's adapter. It is not the temporary member. It stays.

## What an agent does

- Places a new use case in the directories above.
- Registers `Gate::policy` for the model or the domain type the controller actually passes.
- Binds the repository once in `register()`.
- Updates the grade's gap list when it knowingly defers a case.

## What an agent refuses

- `app/Services/ItemService.php` that every controller calls for every rule.
- A repository that returns `ItemModel` to the controller.
- Building the list module on a D-graded access module.
- A new Composer package "to implement the repository" (a custom ORM, a criteria library).
- Deleting the domain-boundary test because the new code needs Eloquent in the action.

## Review checks

- The new class sits in the module's context namespace.
- `AppServiceProvider` still has one binding for the port.
- The grade in `quality-score.md` matches the journeys that actually pass.
- The temporary seeder is gone once M2's deletion feature passes.
- The cut in the plan matches the files in the diff.

## Traps

- Eloquent accessors that encode "if the viewer is not the holder, hide notes" put authorization in the storage adapter. The Prop class is the allow-list. The model returns the column to the repository. The repository maps a domain `Item` that may still hold notes. `ItemStatusProps` drops them. A status query never selects a notes column into a prop array "for convenience".
- Global scopes on `ItemModel` (`where submitter_id = auth id`) are a second authorization path and they break the list-for-any-member rule. The agent refuses a global scope for tenancy or ownership. The action receives `Actor` and the repository method takes the filter the spec named.
- `ItemModel::unguarded()` or `$guarded = []` lets a request mass-assign `holder_id`. The repository assigns columns explicitly. `$fillable` lists only fields a create path sets on purpose, and `holder_id` is set by the claim update, not by `SubmitItemRequest`.
- Traits in `app/Domain` that use `Illuminate\Database\Eloquent\Concerns` pull storage into the domain. The boundary test catches the import.
- React folders named `features/claim` that call the server and also decide the next state duplicate the module. Pages stay under `resources/js/Pages/Items`. Components under `resources/js/Components` render what they are given.
