# 8. Inner loop (every module, every slice)

**Abstract principle.** Every slice follows one order: orient, specify, G2, plan, implement, self-check, fresh verify, lenses if verify is clean, audit, G3, G4, merge or reject. Verify or audit may send the slice back twice. The third rejection goes to the steward with the evidence.

Digest: §8. Evidence: `harness-sources/06-inner-loop.md`, and the seam and cut rules in `harness-sources/14-matt-pocock.md`.

The name Plan-Execute-Verify is a vendor's. The split below is the digest's. Anthropic's November 2025 coding agent implements and self-verifies in one session and sets `passes`. This loop keeps the verifier as a separate session.

```text
orient → specify → G2 → plan → implement → self-check
      → verify (fresh) → lenses if verify is clean
      → audit → G3 → G4 → merge or reject
```

## Orient

New work starts after the history, the boot, and a green core journey. The session then takes the single highest-priority red feature whose module is unlocked (`09-modules.md`).

The session that continues the work reads the progress log and git history. Withholding the build transcript applies to the verifier. It does not apply to orient or to a repair.

1. `pwd` and confirm the working tree is this repository.
2. Read `AGENTS.md`, `docs/progress.md`, and `git log --oneline -20`.
3. Read `docs/features.json`. Select the single highest-priority object with `passes: false` whose module grade allows it.
4. Run `./init.sh`. Wait until the origin prints.
5. Run `npx playwright test e2e/core.spec.ts`. If it fails, set that feature's `passes` back to `false` if it had been `true`, repair the journey, commit, append progress, and stop. No new feature in that session.
6. Only then implement.

`php artisan test` during orient is additional. It does not replace the core journey. A red unit test on untouched code is a dirty handoff: repair that, and the new feature waits.

## Specify

The spec author writes `docs/product-specs/<slice>.md` with every field in `06-artifacts.md`. Guesses are labeled. Expected values are literals (`state` is `open`, error `code` is `item.title.required`, holder id is null).

The session asks the steward the questions. It does not pick the conflict rule, the page size, or the role name. Implementation is forbidden in this session. G2 freezes examples and seams. The spec stays the contract after the slice ships. A small change still gets a spec. The agent refuses "it's only a label, skip the spec".

Each seam records the boundary, what it catches, and what it misses, using the seam table in `06-artifacts.md`.

## Plan

The planner writes `docs/exec-plans/active/<slice>.md`.

Plan alignment fails, before code, when the plan introduces:

- a second Policy or a `Gate::before`
- a second repository binding or a query in the controller
- a second error shape (a JSON body on some routes, a flash string on others)
- a second log channel for the same event
- a `fetch` or Axios call from a page
- a feature cut that lists only one layer

The plan names the files. For a claim slice the list is the concrete set, adjusted to the product's context name:

```text
routes/web.php
app/Http/Controllers/Items/ClaimItemController.php
app/Http/Requests/ClaimItemRequest.php
app/Policies/ItemPolicy.php
app/Domain/Desk/ClaimItem.php
app/Domain/Desk/ItemRepository.php
app/Infrastructure/Persistence/EloquentItemRepository.php
app/Http/Props/ItemStatusProps.php
resources/js/Pages/Items/Show.tsx
resources/js/types.ts
tests/Unit/Domain/ClaimItemTest.php
tests/Feature/Items/ClaimItemTest.php
e2e/claim.spec.ts
```

A file not on the list waits for a plan update the steward sees when the update changes a mechanism. Adding a test file at an accepted seam is inside the list when the seam was named.

A wide mechanical change names expand, migrate, contract. M0, M6, and M7 record the named exception.

## Implement

Contract P3. One feature. `passes` stays false.

Order at each accepted seam:

1. Write one failing test whose expected value is the literal from the spec.
2. Run that test and see it fail for the expected reason.
3. Write the code that makes it pass.
4. Move to the next seam.

The agent writes the domain test before the action, the feature test before the controller, and the Playwright spec before declaring the page done. It does not write the rest of the suite first. It does not recompute the expected value by calling the action and asserting the call against itself.

Refactoring a neighboring class is a gardener slice. Inside this cycle the agent leaves neighboring code untouched.

If the accepted spec cannot be built as written, the agent stops and names the field. It does not substitute a different status machine.

Fast checks during the slice:

```text
vendor/bin/pint --test
npm run typecheck
php artisan test tests/Unit/Domain/ClaimItemTest.php
php artisan test tests/Feature/Items/ClaimItemTest.php
```

Once at the end:

```text
php artisan test
npx playwright test e2e/claim.spec.ts e2e/core.spec.ts
```

Metric gates from `11-quality-gates.md` run in this self-check. If `complexity-score` is unset, record both scores and treat neither as a block.

A send-back includes the failure output in the progress entry. The repair session starts from that output. It does not start from a blank prompt.

The session commits a bootable tree and appends `docs/progress.md` even when `passes` is still false.

### Shape of the adapter

`ClaimItemController` is invokable. It type-hints `ClaimItemRequest` and `ClaimItem`. It builds `Actor` from the authenticated user's id. It calls the action. It redirects to `items.show` or renders through `ItemStatusProps`.

`ClaimItemRequest::authorize` calls `$this->user()->can('claim', $this->route('item'))`. `rules` returns the input rules the spec froze, or an empty array when the claim has no body.

The page posts with `useForm` to `item.claimUrl`. The component does not concatenate a path.

### Shape of the write

The repository method that claims an open item performs one conditional update:

```php
$updated = $this->items->newQuery()
    ->whereKey($id)
    ->where('state', 'open')
    ->update([
        'state' => 'held',
        'holder_id' => $actorId,
    ]);
```

`updated === 0` is the refusal. The action throws the domain exception with the spec's `code`. A second query that first reads and then writes without the condition is a lost update. The agent uses the conditional update for that rule.

The controller does not catch the exception and return a 200. `bootstrap/app.php` renders the domain exception as the error shape: redirect back with `code` and `feature_id`. Validation failures use the same shape's `fields` map, with stable tokens (`item.title.required`).

## Self-check

The implementer runs the commands above and the new journey, repairs failures, and leaves `passes` false. A green self-check is the feedback loop. It is not GV and it is not merge.

Output lands in `docs/evidence/<slice>/self-check.txt` by redirecting the commands, including a failing run that was then fixed. The last run's exit codes are what G3 reads, beside the verifier's re-run.

## Verify

Contract P4. A fresh session. Inputs: the spec, `features.json`, the frozen diff, the commit, the commands, and any failure already recorded. The implementer's chat is not an input.

For each done-means line, the verifier runs the journey or marks the line unchecked. An unchecked line rejects.

The verifier may add a case only when the expected value is already in the spec: the other member, the replay, the malformed title, the missing actor. The verifier copies that literal. The verifier does not read the current JSON and freeze it.

The verifier sets `passes` to `true` only when every feature step holds on the booted app. Then it writes `docs/evidence/<slice>/verify.md` including `rule-change`: the small edit that makes the frozen example fail, the red command, and the revert.

The verifier does not edit `app/` or `resources/js`. A product bug is a reject.

## Lenses

Lenses run only after verify is clean. Six lenses, one session each, same commit: correctness, boundaries, security, tests, simplicity, operability. `12-review.md` is the procedure. They are read-only. High findings block. The agent skips lenses when GV failed.

## Audit

Contract P6. The auditor re-runs boot, prior journeys, this journey, the domain-boundary test, and the attribute checks the slice triggered. The auditor fills the checklist in `11-quality-gates.md` on the latest tree. The auditor does not copy the verifier's word for a row it can run. A reject returns the failing rows and the output to the implementer. The auditor does not patch.

## Merge

Merge when audit is pass, the steward has accepted G3, and G4 has applied the constitution's posture. The commit message contains the feature id. The merging user is not the implementer. Until the posture is named, nobody merges.

## Revision cap

| Event | What happens |
| --- | --- |
| Self-check red | Implementer repairs inside the slice. This is not a send-back. |
| Verifier or auditor reject | Return the failure. Count one send-back. |
| Second reject | Return the failure. Count two. |
| Third reject | Stop. Steward receives the spec path, the diff, and both failure outputs. |

The cap is the digest's rule. The agent does not bargain a fourth loop because the last failure was "small".

A second CI run, then a handoff to a person, is a published stop for one branch. It is a CI stop. It is not this cap. A red CI log stays a repair inside the current send-back until the third rejection. `14-cicd.md` and `21-unattended-runs.md`.

## What an agent does

- Follows the order. A missing spec is a stop, not an invitation to code.
- Keeps `e2e/core.spec.ts` green before new work.
- Records send-backs in `docs/progress.md` with the command output.

## What an agent refuses

- Implementing from a plan that fails alignment.
- `passes: true` in an implementer commit.
- A Playwright test that calls `request.post` and never opens the page, then treats that as the journey.
- Repairing a verifier reject by editing the expected literal.
- A third send-back.

## Review checks

- The orient note says the core journey command and its result.
- The diff's files are the plan's files.
- Tests appeared for each accepted seam, and their literals match the spec.
- The verifier commit is a different session: it changes `passes`, evidence, and at most a spec-derived test.
- Send-back count is in the progress log.

## Traps

- `php artisan test` boots the framework and can pass while Vite's manifest is missing, once `withoutVite()` is set in `TestCase`. The journey still needs the built or dev assets. `withoutVite()` belongs in the HTTP seam setup. It does not belong in `init.sh`.
- Playwright against `php artisan serve` shares one database with the developer's `.env`. The journey's users and rows are created in the test and removed afterward, or `init.sh` for the agent uses a database name dedicated to that working tree (`desk_wt_<branch>`). Two worktrees do not share that database.
- Inertia `useForm().post` follows redirects. A test that only waits for the POST response and not for the show page's text will pass on a redirect to an error flash the assertion never read. The journey asserts the visible state the step names.
- React 19 Strict Mode double-invokes effects in development. A claim posted from `useEffect` will claim twice. The claim is the button's submit handler.
- Pest tests that use `Event::fake()` or `Queue::fake()` on a journey seam hide the side effect the spec made observable. Fakes stay on the seam the spec assigned them to. The journey runs the real boot path.
