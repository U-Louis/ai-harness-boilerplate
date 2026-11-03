# 13. Test: how, when, with what

**Abstract principle.** Tests are evidence producers. A test the implementer can quietly weaken is not a gate. Accepted examples are frozen at G2 before implementation. The expected value is a literal from the spec. A check that goes red when the feature is deleted does not prove the oracle. End-to-end means the user-visible path on the booted app.

Digest: §13. Evidence: `harness-sources/11-testing.md`, `harness-sources/14-matt-pocock.md`.

On runs where generated code was already faulty, tests written afterward by a model that could see that code detected the fault about 14% of the time. Tests from the task text alone detected it about 25% of the time. Both rates are low. The practical consequence is the freeze-before-code rule. Test-first generation is not treated as sufficient.

## What exists before code

`docs/product-specs/<slice>.md` holds given / when / then with literals. The spec author writes them. The steward accepts them at G2. After that, the implementer may add cases whose expected values are copied from that file. A new expected value that the spec does not contain waits for a spec change, which is its own slice. Accepted examples are not deleted or loosened inside a feature slice.

Acceptance text that the agent wrote and then checked against its own code is the drift the digest warns about. The steward is the person who accepts the example.

## Ladder

| Level | Proves | Written by | When | Run |
| --- | --- | --- | --- | --- |
| Example | The decision, in words a person can reject | Spec author | Before code | Human at G2 |
| Domain unit | One rule, including a refusal, with the middleware absent | Implementer, literal from the spec | During the slice | Every self-check and CI |
| HTTP seam | Status, error code, prop allow-list, one row in the test database | Implementer, unless a neighbor owns the examples | Slice touches a route | CI |
| Journey | The feature steps, through `init.sh` | Implementer drives; verifier re-runs | End of slice | Orient of the next session, and CI while it stays deterministic |
| Independent cases | Other actor, replay, malformed input, timeout, from the spec | Verifier may add; expected value from the spec | After the slice | GV |
| Regression | Previously green features | Tool | Session start and CI | `e2e/core.spec.ts` and the feature suite |
| Fault | Slow or dead dependency, duplicate submit | Implementer | M3, M5 | CI for those modules |
| Security | Cross-actor, secret scan, named dependency | Tool and verifier | Triggers in `10-quality-attributes.md` | GS |
| Capacity | The named bar under the stated load | Quality slice | M6, hot paths | GC |
| Rollback rehearsal | Promote and return | Release runner | M7 | GR |

A boundary check the implementer writes against the response it just coded is an HTTP seam test. It is a contract test when another system owns the examples and this slice cannot regenerate them. Pact, or any consumer-driven file, is introduced in the slice that has that neighbor. Until then the folder does not add Pact "for later".

## Domain seam

`tests/Unit/Domain/ClaimItemTest.php`. Pest. No `RefreshDatabase`. An in-memory `ItemRepository` implements the port.

```php
it('refuses a second holder', function () {
    $repo = new InMemoryItemRepository();
    $repo->add(Item::open(id: 'item-1', title: 'Lamp'));

    $action = new ClaimItem($repo);

    $action->execute(new Actor(id: 10), 'item-1');

    expect(fn () => $action->execute(new Actor(id: 11), 'item-1'))
        ->toThrow(ItemException::class, 'item.claim.not_open');

    expect($repo->get('item-1')->holderId)->toBe(10);
});
```

The string `item.claim.not_open` and the holder id `10` are copied from the spec. The test does not call a private method. It does not assert that `update` was invoked. A test that replaces the action with a mock of the action does not count.

`tests/Unit` does not use the `TestCase` HTTP kernel. `tests/Pest.php` applies `Tests\TestCase` and `RefreshDatabase` to the `Feature` directory only.

## HTTP seam

`tests/Feature/Items/ClaimItemTest.php`. Real database. Middleware stack left in place. `withoutVite()` in `Tests\TestCase::setUp` so a missing manifest is not the failure.

```php
it('refuses the other member and keeps the holder', function () {
    $holder = User::factory()->create();
    $other = User::factory()->create();
    $item = ItemModel::factory()->heldBy($holder)->create();

    $response = $this->actingAs($other)
        ->from(route('items.show', $item))
        ->post(route('items.claim', $item));

    $response->assertRedirect(route('items.show', $item));
    $response->assertSessionHas('error.code', 'item.claim.not_open');
    expect($item->fresh()->holder_id)->toBe($holder->id);
});
```

`claim` is allowed for any signed-in member. The refusal is the lifecycle (`state` is already `held`), so the observable is the spec's code and the unchanged holder. A non-holder `close` is the Policy denial. `CloseItemRequest::failedAuthorization` writes `item.close.not_holder` into the same error shape. The test asserts that code. A bare `assertForbidden()` is the assertion only when the spec's literal is the 403 status itself.

The happy path asserts `assertRedirect(route('items.show', $item))`, the row, and:

```php
$response->assertInertia(fn (Assert $page) => $page
    ->component('Items/Show')
    ->where('item.state', 'held')
    ->where('item.holderId', $holder->id)
    ->missing('item.notes')
    ->missing('item.notesBody'));
```

`where('item.state', 'held')` is the spec's word. If the show page is reached by a redirect, the test follows the redirect (`$this->followRedirects` or a subsequent `get`) and asserts the props on that response. The agent makes the assertion match the spec's observable, and records the Laravel call in the test. It does not assert `assertOk()` on a redirect response and call that the state.

Missing actor:

```php
$this->post(route('items.claim', $item))->assertRedirect(route('login'));
```

`actingAs` is this seam's way to name the actor. It is not the journey.

## Journey seam

`e2e/claim.spec.ts`. Playwright. `baseURL` is the origin `init.sh` prints.

```ts
test('member B cannot claim an item member A holds', async ({ page }) => {
  await signIn(page, 'a@example.test');
  await page.goto('/items/item-1');
  await page.getByRole('button', { name: 'Claim' }).click();
  await expect(page.getByRole('status')).toHaveAttribute('data-state', 'held');

  await signIn(page, 'b@example.test');
  await page.goto('/items/item-1');
  await page.getByRole('button', { name: 'Claim' }).click();
  await expect(page.getByRole('alert')).toHaveAttribute('data-error-code', 'item.claim.not_open');
});
```

`signIn` types into the login form and waits for the authenticated shell. It does not set a cookie by hand unless the spec names that seam. The error code is the spec's literal.

`e2e/core.spec.ts` contains the journeys already marked passing, and only those. Orient runs it. A new feature joins `core.spec.ts` only after the verifier sets `passes`.

Native `alert()`, `confirm()`, and `prompt()` are refused. Refusals render in the page. Playwright's blindness to native dialogs is a recorded limitation when a third-party widget uses them. The agent does not add `page.on('dialog')` as a substitute for a missing alert region the spec required.

## Rules that keep the ladder honest

- The assertion's expected value is a literal from the spec. An assertion that recomputes the value the way the action does (`expect($item->state)->toBe($action->stateFor($item))`) does not count.
- The implementer has no write access to the accepted spec. CODEOWNERS and the role rules enforce that. Prompts do not.
- Read-only tests stop an edit to the example. They do not stop a special case in the action keyed on a fixture id. GV's `rule-change` is a one-line semantic edit that must turn the frozen test red. The verifier reverts it.
- Scope Infection to the action: `vendor/bin/infection --filter=ClaimItem`. Read survivors. A survivor blocks. There is no repository MSI gate. Killing a mutant by asserting the current return value fails G3.
- Coverage output is a map of unexecuted lines in the slice. `phpunit.xml` does not fail on a percentage. A risk-class branch with no spec-derived case is a hole. CRAP blocks only when the constitution names it and a ceiling.
- `phpunit.xml` sets `beStrictAboutTestsThatDoNotTestAnything="true"`.
- A test that passes and fails with no meaningful change leaves the blocking job the day it flakes. It is listed in `e2e/quarantine.md` and fixed in a slice. The agent does not loop Playwright with `--retries` until green and file that as evidence.
- No bare `sleep` as a wait. Playwright uses locators and web-first assertions. PHPUnit uses the framework's time helpers (`Carbon::setTestNow`) when the spec's rule depends on a clock. A random threshold is refused.
- Failure output names the feature id and the expected observable. Pest's `it('F12 refuses a second holder', ...)` is the pattern.
- One failing test at the seam, then the code. The rest of the suite waits. Refactoring is not this cycle.
- The journey list stays small. Broad browser tests are where flakes cluster. One journey per feature, plus `e2e/core.spec.ts`, is the layer. Branches the journey already covers are not re-asserted at the domain seam unless the branch is a risk class that must fail in isolation.
- An example that asserts nothing fails. An illegally nested Pest dataset that never runs fails GG when the schema check can see it, and fails the verifier's read otherwise.

## When not to add a test

- A non-goal (notifications, payments) gets no test that builds the non-goal "so the suite documents the absence". The spec's non-goals section is the record. A test that the notifications table is absent is a second product.
- A framework tour (`assertTrue(class_exists(ClaimItemController::class))`) does not count.
- A duplicate of the journey at unit level for every branch the journey already covers, unless that branch is a risk class.
- A snapshot of the entire Inertia page HTML. The prop allow-list and the journey's role assertions are the seams. A full HTML snapshot fails when the layout changes and teaches the agent to update snapshots.

## Oracle edit, mechanical response

If the diff changes a literal in `tests/` or `e2e/` that matches an accepted example, and the spec file did not change in a steward-accepted spec slice, the change is reverted. The progress entry says `oracle edit reverted`.

If the diff changes `docs/product-specs` or `docs/features.json` `steps` during P3, the change is reverted. The verifier is the only role that sets `passes`.

## What an agent does

- Copies literals from the spec into the test before writing the action.
- Runs the single new test, sees the failure, then writes the code.
- Follows redirects explicitly and asserts the spec's observable.
- Records Playwright's trace or screenshot path in the evidence when the journey fails.

## What an agent refuses

- `Mockery::mock(ClaimItem::class)` as the system under test.
- `withoutMiddleware()` on an authentication or authorization test.
- `Event::fake()` or `Mail::fake()` on the journey process.
- Updating a Playwright snapshot to make a red step green.
- A test whose expected state is read from the database after the act and then compared to itself.
- `Http::fake()` that returns success for a dependency the fault slice was supposed to prove down.
- Deleting `e2e/core.spec.ts` entries when they fail during orient. Orient repairs the product.

## Review checks

- Every accepted seam has one test file.
- Literals match the spec.
- `missing('item.notes')` is present on every status render the slice added.
- The journey opens a page and uses a role or a `data-error-code`.
- Infection output, or a statement that the slice's decision was not a domain rule, is in the evidence.
- `rule-change` names the edit and the red command.

## Traps

- Laravel's `RefreshDatabase` wraps each test in a transaction. A journey running in another process will not see those rows. The journey uses the working-tree database and its own fixtures. The agent does not point Playwright at `phpunit`'s in-memory SQLite.
- `assertInertia` `where('item.state', $item->state)` copies the model into the expectation. The literal is `'held'`.
- Factory states that call the domain action hide a broken action behind a broken factory. Factories insert columns through Eloquent. Domain tests build `Item::open(...)`.
- Pest's `depends()` chains tests into an order. The agent refuses `depends` for product behavior. Each test arranges its own rows.
- Playwright `page.waitForTimeout` is a bare sleep. The agent uses `expect(locator).toBeVisible()`.
- A feature test that disables `VerifyCsrfToken` proves a different application than the journey, which posts the real form. The testing runtime already skips CSRF. An extra `withoutMiddleware(ValidateCsrfToken::class)` in a single test is noise. Removing CSRF from the `web` group is a risk-class defect.
- TypeScript `as any` on page props hides a `notes` field. `npm run typecheck` uses `strict`. The Prop type is the source the page imports. An `any` on the page props fails the tests lens.
