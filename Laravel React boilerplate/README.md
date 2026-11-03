# Laravel + React recommendations

This folder translates `Abstract boilerplate/` into operational rules for a coding agent working on one Laravel application whose pages are React. The digest in `Abstract boilerplate/Abstract-boilerplate.md` stays the statement of the harness. These files say where each rule lives in the tree, which command proves it, and which edits the agent refuses.

The notes in `Abstract boilerplate/harness-sources/` stay the evidence. This folder does not restate that research.

## Architecture

One Laravel 12 application. Inertia v2 renders React 19 pages written in TypeScript. The browser talks to Laravel with Inertia visits on the same origin, and the session cookie is the credential. Laravel owns the route, the actor, the authorization decision, input validation, domain rules, persistence, and the error code. React renders the props and the URLs that decision already computed.

This is the architecture for the whole folder. Later files apply it. They do not reopen it.

The digest describes one bootable product. Domain rules are independent of delivery and storage. A walking skeleton links the page, the action, and the store. Authorization sits in a trusted server layer. The user-visible journey is the done-check. A separate JSON API plus a token-bearing SPA would make two clients and two sessions. The digest does not ask for that split. A later slice may add a neighbor-owned HTTP contract, with its own spec, its own examples, and its own seam. Until that slice is accepted, `routes/api.php` carries no product route, and React pages do not call Axios, `fetch`, or a bearer token.

Version assumptions, pinned so an agent does not invent APIs. Late 2026:

- Laravel 12, using the Laravel 11 and 12 application skeleton: `bootstrap/app.php`, `bootstrap/providers.php`, no `app/Http/Kernel.php`, no `app/Console/Kernel.php`. PHP 8.3 or newer, inside the range Laravel 12 supports.
- `inertiajs/inertia-laravel` v2 and `@inertiajs/react` v2. The calls these files rely on are `Inertia::render`, the `HandleInertiaRequests::share` method, `<Link>`, `useForm`, and `router` visits.
- React 19 function components. TypeScript with `strict` enabled. An error boundary is a class component because that is the React mechanism that implements `componentDidCatch`.
- Pest as the PHP test runner, the runner shipped by the Laravel application skeleton, on top of PHPUnit. The command is `php artisan test`.
- Playwright drives the user-visible journey. A Pest feature test is a different seam.
- Vite builds the assets. Playwright loads the Laravel origin. Vite is the asset server behind that origin.

A Laravel 11 application uses the same directories and the same `bootstrap/app.php` shape. A starter-kit flag or a package minor version that differs between those lines is written into the slice plan after the steward confirms the command. This folder does not describe Laravel 13 APIs.

## How an agent uses this folder

1. Read this file.
2. Read the product `AGENTS.md`, `docs/constitution.md`, the current slice spec, `docs/features.json`, and `docs/progress.md`.
3. Open the numbered file for the concern you are about to change. Read it before the edit.
4. The product constitution fills the choices left open here: storage engine, `complexity-score`, `diff-posture`, `instruction-model`, the ASVS level, and every numeric bar.
5. Run one prompt contract from `17-prompt-contracts.md` per session.

The accepted constitution wins over this folder. This folder wins over a starter-kit habit the constitution has not accepted. The digest wins as the reason for a rule when a translation is unclear. A session that needs a rule the numbered file does not state stops and names the gap. It does not invent a second architecture.

## Directory contract

```text
app/Domain/<Context>/                  pure PHP: entities, actions, repository interfaces, domain exceptions
app/Http/Controllers/<Context>/        invokable adapters, one class per use case
app/Http/Requests/                     Form Requests
app/Http/Props/                        readonly Inertia props, the field allow-list
app/Http/Middleware/                   correlation id, Inertia shared props
app/Policies/                          the allow decision for each ability
app/Infrastructure/Persistence/        Eloquent models and the one repository implementation
app/Infrastructure/Boot/               required-configuration check
app/Infrastructure/Listeners/          a side effect the spec names
app/Jobs/                              deferred work the spec names
app/Models/User.php                    the session user's record
app/Providers/AppServiceProvider.php   bindings, policy map, rate limiters
bootstrap/app.php                      middleware and exception rendering
routes/web.php                         product routes
resources/js/Pages/                    Inertia pages, one file per rendered component name
resources/js/Components/               presentation
resources/js/Layouts/
resources/js/types.ts                  prop types that match the Prop classes
resources/views/app.blade.php          the single HTML document
database/migrations/
database/factories/
tests/Unit/Domain/
tests/Feature/
e2e/                                   Playwright specs
playwright.config.ts
docs/                                  digest artifact tree
init.sh
AGENTS.md                              short map into docs/
```

`app/Domain` does not import `Illuminate\Http`, `Inertia\Inertia`, `Illuminate\Database\Eloquent`, or `Illuminate\Support\Facades`. The repository interface returns domain objects. The Eloquent model stays under `app/Infrastructure/Persistence`.

## Request lifecycle

1. `routes/web.php` names the route. State-changing routes use `web`, and routes that require an actor also use `auth`.
2. The invokable controller type-hints the Form Request.
3. `authorize()` calls the Policy. `rules()` constrains the input. A failed rule responds with the validation error shape.
4. The controller builds `Actor` from `$request->user()->id` and calls one domain action.
5. The action talks to the repository port. It returns a domain result, or throws a domain exception that carries `code` and `feature_id`.
6. The controller maps the result through one Prop class and returns `Inertia::render`, or redirects to a named route.
7. The page renders props. A mutation uses `useForm` and posts to a URL the prop already contains.

Shared props (`auth.user` id and name, flash error, correlation id) are composed in `HandleInertiaRequests`. A page reads them with `usePage().props`.

## Vocabulary

| Digest word | This stack |
| --- | --- |
| Trusted service layer | Form Request, Policy, and domain action |
| Delivery adapter | Controller, Prop class, Inertia page |
| Storage adapter | The one Eloquent repository bound in `AppServiceProvider` |
| HTTP seam | Pest feature test hitting the route |
| Journey seam | Playwright against the booted Laravel origin |
| Oracle | Accepted given / when / then in `docs/product-specs/`, values frozen at G2 |
| Error shape | `{ code, feature_id, fields? }` |
| Log shape | `feature_id`, `correlation_id`, `actor_id`, `outcome`, `code` |

## What an agent does on every slice

- Keeps the edit inside the spec's allowed paths.
- Reuses the Policy, the Prop allow-list, the repository binding, the error shape, and the log shape.
- Writes one failing test at each seam the spec accepted, with the expected value copied from the spec, then the code that makes that test pass.
- Leaves `passes` false. The verifier is the role that sets it.
- Ends with a bootable tree, a commit, and a `docs/progress.md` entry.

## What an agent refuses

- A second UI stack (a Blade feature page, Livewire, Filament) beside Inertia.
- A product route in `routes/api.php`, a bearer token, or an Axios client for the first-party pages.
- A business rule that exists only in `resources/js`.
- A second authorization mechanism, a second repository binding, a second error shape, or a second log shape.
- An edit to an accepted example, a feature `steps` array, or `passes`, unless the role is the verifier setting `passes` after the journey matches.
- A new Composer or npm dependency the plan did not name.
- Merging, approving, or deploying the change it authored. A ready label whose job is to start another run.
- A model job whose token writes issues, reviews, or the default branch. `21-unattended-runs.md`.

## Map

| File | Digest | Read it before |
| --- | --- | --- |
| `01-what-this-is.md` | §1 What this is | changing the harness itself |
| `02-claims.md` | §2 Claims | treating a blog number as a Laravel bar |
| `03-failure-modes.md` | §3 Failure modes | a session that is about to "finish the app" |
| `04-design-principles.md` | §4 Design principles | any prompt, artifact, or gate |
| `05-roles.md` | §5 Roles | deciding who may edit, approve, or merge |
| `06-artifacts.md` | §6 Persistent artifacts | adding or renaming a harness file |
| `07-outer-workflow.md` | §7 Outer workflow | starting a product or a module |
| `08-inner-loop.md` | §8 Inner loop | running one slice |
| `09-modules.md` | §9 Modules | ordering work or placing a class |
| `10-quality-attributes.md` | §10 Quality attributes | security, privacy, capacity, reliability, operability |
| `11-quality-gates.md` | §11 Quality gates | making a check blocking |
| `12-review.md` | §12 Review | reviewing a diff |
| `13-testing.md` | §13 Testing | adding or changing a test |
| `14-cicd.md` | §14 CI/CD | the pipeline, promote, or rollback |
| `15-worked-example-desk.md` | §15 Desk | copying a concrete shape |
| `16-stop-retry-escalate.md` | §16 Stop, retry, escalate | a red check or a growing diff |
| `17-prompt-contracts.md` | §17 Prompt contracts | opening a session |
| `18-measurement.md` | §18 Measurement | deciding whether the harness works |
| `19-evolvability.md` | §19 Evolvability | a model change or a six-month pass |
| `20-limits.md` | §20 Limits | treating a rule as a lab result |
| `21-unattended-runs.md` | §21 Unattended runs | a forge event, a clock, or a failed check starting work |
| `22-sources.md` | §22 Sources | binding a tool to a check |
