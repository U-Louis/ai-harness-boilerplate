# 15. Worked example — Desk

**Abstract principle.** Desk is one illustration of the digest: a signed-in member submits an item, claims an open item, and closes an item they hold. Another member cannot claim or close it. A status read returns state and holder, not private notes. The module order and the gates are the point. The product you are building substitutes its own intent and keeps the rules.

Digest: §15.

This pass names `diff-posture: read` and `complexity-score: crap` with no ceiling, so a reader can see one path. Metrics, and cognitive complexity, stay available. Later modules inherit the intent, including the non-goals.

The integers below (title length 120, and any page size the M4 spec will name) are this illustration's frozen spec. They are not universal bars. A real constitution replaces them before M1 and the agent copies the replacement.

## Intent (G1)

> A signed-in member can submit an item, claim an open item, and close an item they hold. Another member cannot claim or close it. A status read returns state and holder, not private notes. Out of scope: payments, notifications, file attachments, multi-organization hierarchies.

Non-goals stay out of `composer.json`, `routes/web.php`, and `resources/js`. The agent refuses Cashier, a notifications table, an attachment disk, and a teams package in this pass.

## M0 — Constitution (no product behavior)

The steward locks:

- One storage engine (the steward picks PostgreSQL, MySQL, or SQLite). One repository binding. One Policy. One error shape. One log shape.
- Edges in `09-modules.md`.
- Data classes for this illustration: `title` and `notes` are account-class. `state` and `holder_id` are readable by any signed-in member. Secrets are never stored on the item. Passwords stay on `users` and stay hashed.
- Budgets empty until a real bar exists: status-read latency has no five fields yet, so there is no latency gate. Submit and claim will have a round-trip bound once M3's spec states the integer. Page size, payload, timeout, and rate limit are bounded once their specs state the integers. This file's illustration freezes title at 120 characters for M1. It does not freeze a millisecond.
- Risk classes in `05-roles.md`.
- `complexity-score: crap`, ceiling unset, so the check reports and does not block.
- `diff-posture: read`.
- `instruction-model` set to the model the steward is actually running, or left unset so the deletion pass waits.

The researcher may list storage options. The steward picks. The assumption is `confirmed`. A feature session does not reopen it.

No `laravel new` in M0. The diff is `docs/` only.

## M1 — Walking skeleton

Features:

- F1. A member submits an item and reads its status back as `open`, with no holder.
- F2. An empty title is refused with `item.title.required`.

Grade target: C. Allowed. M7 will not promote at C.

### Spec literals

- Given a signed-in temporary member, when they submit the title `Lamp`, then the show page's status region reads state `open` and holder is absent, and the `items` row has `title = Lamp`, `state = open`, `holder_id` null.
- Given a signed-in temporary member, when they submit an empty title, then the alert's `data-error-code` is `item.title.required` and no row is inserted.
- Title longer than 120 characters is the same refusal code `item.title.too_long`, if the spec author includes it in F2's examples. If they do not, the agent does not invent the code.

Seams: domain (`SubmitItem`), HTTP (`items.store`, `items.show`), journey (`e2e/submit.spec.ts`). The temporary member is a seam. `decisions.md` says M2 deletes `TemporaryMemberSeeder` and runs F1 under a real login.

### Plan cut

Page `Items/Create` and `Items/Show`, routes `items.store` and `items.show`, `SubmitItemRequest`, `ItemPolicy::create` and `view` (the temporary member is allowed), `SubmitItem`, `ItemRepository`, the migration, `ItemStatusProps`.

The plan may create the Laravel 12 application with the command the steward wrote into the constitution. Extra starter routes are removed in this slice or listed with a deletion id.

### Files

```text
database/migrations/2026_11_02_000000_create_items_table.php
database/seeders/TemporaryMemberSeeder.php
app/Domain/Desk/Item.php
app/Domain/Desk/Actor.php
app/Domain/Desk/SubmitItem.php
app/Domain/Desk/ItemRepository.php
app/Domain/Desk/Exceptions/ItemException.php
app/Infrastructure/Persistence/Eloquent/ItemModel.php
app/Infrastructure/Persistence/EloquentItemRepository.php
app/Http/Controllers/Items/SubmitItemController.php
app/Http/Controllers/Items/ShowItemController.php
app/Http/Requests/SubmitItemRequest.php
app/Http/Props/ItemStatusProps.php
app/Policies/ItemPolicy.php
app/Providers/AppServiceProvider.php
routes/web.php
resources/js/Pages/Items/Create.tsx
resources/js/Pages/Items/Show.tsx
resources/js/types.ts
tests/Unit/Domain/SubmitItemTest.php
tests/Feature/Items/SubmitItemTest.php
tests/Feature/Architecture/DomainBoundaryTest.php
e2e/submit.spec.ts
e2e/core.spec.ts
init.sh
```

### Migration

```php
Schema::create('items', function (Blueprint $table) {
    $table->ulid('id')->primary();
    $table->foreignId('submitter_id')->constrained('users');
    $table->string('title', 120);
    $table->text('notes')->nullable();
    $table->string('state', 16);
    $table->foreignId('holder_id')->nullable()->constrained('users');
    $table->timestamps();
});
```

`notes` exists so later slices can prove it stays off the status prop. M1's create path does not ask for notes and does not fill them from the request.

### Request and policy

`SubmitItemRequest::rules` returns `['title' => ['required', 'string', 'max:120']]`. `messages()` or the shared failed-validation renderer maps the failures to `item.title.required` and `item.title.too_long`. `authorize` calls `can('create', Item::class)`.

`ItemPolicy::create` allows the signed-in temporary member. `ItemPolicy::view` allows any signed-in member. Both are registered with `Gate::policy`.

### Journey

`e2e/submit.spec.ts` signs in as the temporary member through the login page (the seam may be a seeded password the steward put in the test environment, not in the spec's public examples), submits `Lamp`, and expects `data-state="open"`. The empty-title case expects `data-error-code="item.title.required"`.

`e2e/core.spec.ts` includes F1 and F2 only after the verifier sets `passes`.

### Verify and G4

The verifier drives submit and read on the booted origin. The auditor runs the checklist. G4 applies Read. A person other than the author reads the diff, including the tests, because the first migration and the pipeline stub are risk classes. `passes` flips only in the verifier session. Grade C is recorded with the gap "single seeded member, no second-actor proof".

## M2 — Identity and access

Features: sign in through the page, refuse a missing actor, two members are distinct. The temporary seeder is deleted. F1 runs under a real session.

Independent cases: member B cannot present member A's session. A request with no actor on `items.store` redirects to `login`.

The one login path is the one the constitution named (the starter's session login, or the controllers this module adds). The login controller regenerates the session. A second login controller is a failed plan.

G4 applies Read. A person other than the author reads every diff in this module, including tests. Grade target: B or better before M3. The gap list loses the seeded member. If a known gap remains, it has a feature id.

The verifier searches for `TemporaryMemberSeeder` and finds nothing, and runs F1 through the login form.

## M3 — Claim and close

Features:

- F12. Claim an open item. The holder is the signed-in member. State is `held`.
- F13. A second member is refused with `item.claim.not_open`, and the holder stays.
- F14. The holder closes. State is `closed`.
- F15. A non-holder cannot close. Code `item.close.not_holder`.
- F16. Closing an already closed item returns the spec's single choice. This illustration's spec, once the steward confirms it, uses `item.close.already_closed` and does not change the row. Until that guess is `confirmed`, the implementer stops.
- F17. Replaying claim does not create two holders.

The trace for F12 shows `db_query_count` at or under the integer M3's spec states. A storage timeout returns `error.unexpected` or the spec's `item.storage.timeout` and does not report success. The agent does not pick the timeout code if the spec left it as a guess.

`ClaimItem` uses the conditional update in `08-inner-loop.md`. `CloseItem` updates `where state = held and holder_id = actor`. `ItemPolicy::close` allows only the holder. The action's conditional update is the lifecycle. The Policy is the ability. The plan says that split in one sentence so they do not both reimplement each other.

Lenses run after verify is clean. The security lens looks for a claim button that posts while `authorize` returns true for every member. The tests lens looks for an assertion that a mock was called, or a literal copied from the model's current state.

### One M3 session (F12)

1. Orient: progress, `git log --oneline -20`, `./init.sh`, `e2e/core.spec.ts` green (F1, F2, and M2's sign-in). Pick F12.
2. Spec already accepted. If F16's conflict rule is still `guess`, this session is F12 only and does not "also close".
3. Plan: the file list in `08-inner-loop.md`, reuse of `ItemPolicy` and `EloquentItemRepository`, seams the spec named.
4. One failing domain test with holder `10` and code `item.claim.not_open` for the second call, then the action. Then the HTTP test. Then the page and the Playwright spec.
5. Self-check: Pint, `tsc`, `php artisan test`, `npx playwright test e2e/claim.spec.ts e2e/core.spec.ts`.
6. Commit. Progress note. `passes` stays false.
7. Fresh verifier re-runs the steps, adds the replay case when the spec's expected value requires it (F17 may be its own feature), and sets `passes` only when the frozen examples hold.
8. Lenses on that commit. Auditor re-runs the checklist.
9. Steward reads the evidence and names the blind spot. Read applies, so the steward also reads the change, including the tests, because authorization is a risk class. Merge is a forge user who is not the implementer.

If the verifier finds that member B can claim, the implementer gets that failure. Two send-backs. The third rejection escalates with the spec, the diff, and the outputs.

## M4 — List and filter

Features: a member lists open items, page size honored, filter by state. The list uses `ItemPolicy::view` and `EloquentItemRepository`. It does not open a second connection.

`ListItemsRequest` constrains `per_page` and `state`. The index prop is a list of `ItemStatusProps`. The feature test plants more rows than the page and asserts `notes` is missing on every row and the query count stays flat when the table grows.

The load scenario waits for M6. Capacity here is the static bound.

F1 through F3 (submit, the M2 session, claim) still pass.

## M5 — Operability

Features are observable: the submit response's page carries the correlation id in a `meta` element or a `data-correlation-id` attribute the spec names. A bad title returns `item.title.required` and not a stack trace. Claim and close write one audit row whose columns the spec lists. The N+1th submit inside the window is refused with the spec's code. The window and N are filled in the spec before code. This illustration does not pick them.

`AssignCorrelationId` and `RateLimiter::for('submit-item')` are the mechanisms. A second counter in the controller fails the plan.

## M6 — Quality attributes

No new user feature. The product of the slice is evidence.

- The chosen ASVS level, pass or fail per requirement, in `docs/evidence/M6/asvs.md`. Cross-actor claim, cross-actor close, and cross-actor read of notes are in that set. Every-change secret, dependency, and validation checks have already been running.
- Round-trip counts on submit and claim, from the trace.
- The latency bar only if the five fields are now filled. If they are empty, the latency gate stays off. Page size, payload, timeout, and rate limit still fail closed.
- A miss opens a repair slice. It is not waived inside the session.

Grade moves to A or B, or promotion is forbidden.

## M7 — Release candidate

The pipeline in `14-cicd.md`. Rehearse: promote the candidate in a non-production environment, run F1 and the claim journey, roll back, run them again on the previous candidate. The steward approves production. The first exposure is a partial canary. The agent does not approve that environment and does not merge its own release change.

## Cadence after the first promotion

Gardener slices, separate from features: a second log shape, a module graded below B, docs that disagree with a route, a pattern the constitution already forbids. Each cleanup is a slice with the same gates. Behavior stays green. A cleanup that changes a code or a prop goes through G2 again. The gardener does not automerge.

## What an agent does when copying Desk

- Replaces the intent, the literals, and the integers with the product's accepted spec.
- Keeps the file layout, the single binding, the Prop allow-list, the journey, and the gates.
- Stops when a Desk guess (the close-already-closed code, the rate limit) is still a guess in the product spec.

## What an agent refuses

- Porting Desk's temporary seeder into a product whose M2 has already shipped.
- Adding notifications because the illustration mentioned them as a non-goal.
- Treating grade C at M1 as permission to promote.
- Implementing F12 and F14 in one session because both are "M3".

## Review checks

- The product spec's non-goals are absent from `composer.json` and `routes/web.php`.
- M1 evidence records the seeded-member seam and the deletion id.
- M2's tree has no `TemporaryMemberSeeder`.
- M3's claim test asserts one holder after two posts.
- M4's list assertion misses `notes` and bounds `per_page`.
- M7's record shows two journey runs around a rollback.

## Traps

- Copying the migration's `notes` column into `ItemStatusProps` "because the column exists" breaks F1's privacy example the moment a note is stored. The column is account-class. The prop omits it from the first slice that returns status.
- Using Desk's `held` / `open` / `closed` strings in a product whose spec says `claimed`. The spec's word is the literal. Desk's word is only Desk's.
- Leaving the illustration's Read posture in a product whose constitution says Metrics. G4 reads the product constitution, not this file's sample posture.
