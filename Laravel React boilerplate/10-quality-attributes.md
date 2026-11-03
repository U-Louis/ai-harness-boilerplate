# 10. Quality attributes

**Abstract principle.** Each attribute has a bar, a check, and a trigger. A bar is a binary rule, or a number that names the request, the proportion, the threshold, the measurement point, and the window. This folder leaves milliseconds unset. Example prompts from one internal product are not Desk's bars.

Digest: §10. Evidence: `harness-sources/08-quality-attributes.md`.

The three security triggers replace a vague "every trust-boundary slice". A logging change can print a token. A page can add a package. The dangerous diff is often an edit to the one Policy, a migration, or a workflow permission.

## Traffic (capacity)

**Bar.** Until `docs/constitution.md` names the request, the proportion, the threshold, the measurement point, and the window, there is no latency gate. The write path rejects excess work. A read grows with the page, not with the table.

The spec names the integers for payload size, page size, operations per request, and rate limit before the slice that enforces them. This file does not pick those integers.

**Where it lives.**

- Payload: `SubmitItemRequest` rules, `max` on the string, and `ValidatePostSize` / the web server limit the deploy slice records. A body over the spec's size never reaches the action.
- Page size: `ListItemsRequest` rules `'per_page' => ['required', 'integer', 'min:1', 'max:'.$max]` where `$max` is `config('desk.page_size_max')`, and that config value is the spec's integer. The string `all` fails the integer rule.
- Rate: `RateLimiter::for('submit-item', ...)` in `AppServiceProvider`, middleware `throttle:submit-item` on `items.store`. The limiter reads the spec's maximum and decay. A second in-controller counter is a second mechanism.
- Excess writes: the conditional update and the unique lifecycle. The agent does not queue unbounded claim jobs to "smooth" the write.

**Check.** At M6 and before promotion, a repeatable load scenario against the isolated environment the plan names. On the slice that adds the list, a static check: `per_page` cannot be unbounded, an oversized payload is rejected, and the journey trace's storage round-trips do not grow with the page length. Record latency of successes and of failures separately, plus traffic, errors, and saturation, once the trace is queryable.

**Trigger.** The load scenario at M6 and before promotion. The static bounds on the slice that adds the list or the entry point.

**Fail.** The named proportion misses the named threshold in the named window, excess work is accepted, or a read grows with the table.

## Efficiency

**Bar.** The core write does a bounded number of storage round-trips, the number the spec states. A list does not issue a query per row. Payloads over the stated size are rejected. Work the spec marks deferred is a job, not work inside the request after the response should have been sent.

**Where it lives.**

- `SubmitItem` and `ClaimItem` repository methods: one insert, or one conditional update. The agent refuses a loop that runs `User::find` per row inside `ListOpenItems`.
- `Model::preventLazyLoading(true)` in `AppServiceProvider` when `app()->environment('local', 'testing')`. A lazy load throws. The remedy is an explicit column list or a join in the repository method, not a global `loadMissing` in the controller.
- The trace: Laravel's log context includes `db_query_count` for the request when M5's correlation middleware counts queries via `DB::listen` for that request id. Until that count is in the log, a numeric round-trip budget is not a gate. The agent may still reject an obvious per-row query in review.

**Check.** The journey log line for the feature id shows `db_query_count` at or under the spec's bound. The list feature test uses a factory of more rows than the page size and asserts the query count stays flat when the table grows and `per_page` stays fixed.

**Trigger.** M3, M4, M6, and any plan that mentions a loop over records.

**Fail.** Round-trips scale with list length, or one request can read the whole table.

## Security

### Every change

- Secret push protection on `git push`, and a pipeline secret scan. Large diffs are the ones a forge skip can miss, so the pipeline scan stays.
- A new Composer or npm dependency is named in the spec, pinned by the lockfile, and reviewed. An unnamed dependency stops the slice.
- New input is validated in the Form Request: an allow-list, a shape, or a logical limit. The React page may repeat a check for immediate display. The Form Request is the check that fails the HTTP seam.

`config/app.php` debug is false in any shared environment. `APP_DEBUG=true` in a committed `.env` is a secret-class failure because error pages leak the environment.

### The diff touches a security-critical path

Paths: `app/Policies`, authentication and session config, cryptography, `database/migrations`, CI and deploy workflows.

The slice includes one allow case and one deny case for the access this diff changes:

- other actor
- missing actor
- a field that must not come back (`item.notes`)

Under Read, a person other than the author reads that path, including the tests. Under Metrics, those cases are in the evidence and the person play-tests them. The person leaves the agent diff unread.

The missing-actor case on a `web` route with `auth` middleware is `assertRedirect(route('login'))`. The journey shows the login page. A signed-in member who fails the Policy gets the spec's code. The Form Request's `failedAuthorization` writes that code into the error shape (`item.close.not_holder` in the Desk illustration). The HTTP seam asserts the code and the unchanged row. The journey asserts `data-error-code`. A 403 status is the assertion only when the spec's literal is that status.

### Release, once

M6 runs the full requirement set at the ASVS level the constitution chose, with pass or fail per requirement id in `docs/evidence/M6/asvs.md`. That file does not replace the two rows above.

The digest departs from AISVS AC.4.1. The agent does not write "compliant" on the evidence. AC.8.1 is satisfied when the forge refuses the implementer's merge. AC.4.2 is claimed only when SAST, IAST, DAST, secret scanning, IaC scanning, and SCA actually run.

**Fail.** A cross-actor case succeeds on a path the diff touched, a secret scan hits, a new dependency is unnamed, or the release-level set is red.

## Reliability

**Bar.** A timeout becomes a known error code. A retried claim keeps a single holder. A partial failure is visible. The process refuses to start without the configuration a shipped module requires. Retries are bounded. A permanent error is not retried.

**Where it lives.**

- `app/Infrastructure/Boot/RequiredConfiguration.php`, called from `AppServiceProvider::boot`. It throws `RuntimeException` listing missing keys from `config('desk.required')`. M1 requires the application key and the database connection. Later modules append keys only when their spec says the process must refuse to start without them.
- The claim's conditional `UPDATE ... WHERE state = open`. A replay updates zero rows and the action returns the current holder without inserting a second one. The feature test sends the claim twice and asserts one holder.
- Downstream HTTP, when a slice adds it: an explicit timeout on the Laravel HTTP client (`Http::timeout($seconds)`), the seconds coming from the spec. On timeout the action throws the spec's code. The user-visible body is that code. The log holds the exception class.
- Jobs, when the spec says the work is deferred: `$tries` and `$backoff` are the spec's integers. `failed()` logs `feature_id`, `correlation_id`, and `code`. The job calls the same action the controller calls. A permanent exception uses `$failOnTimeout` or `failed()` and does not retry. The agent refuses `tries = 0` meaning infinite.

**Check.** Fault cases: dependency slow, dependency down, duplicate submit. One idempotency case per state change.

**Trigger.** M3 and M5, and any slice that adds a retry or a downstream call.

## Operability

**Bar.** Every request carries a correlation id. Logs are structured. The core journey emits a line the agent can query. A failure names the feature id. Credentials, session cookies, and notes are absent from logs.

**Where it lives.**

- `app/Http/Middleware/AssignCorrelationId.php` reads `X-Request-Id` when it is a UUID, or generates one. It sets the response header, `Log::shareContext(['correlation_id' => $id])`, and shares `correlationId` through Inertia.
- Log calls use the shape `feature_id`, `correlation_id`, `actor_id`, `outcome`, `code`. The message is a stable token (`item.claim.refused`).
- `./init.sh` prints the log command for this boot (`php artisan pail` in development, or the path of the single log channel). Until the agent can query that output, a numeric latency budget is not a gate.

**Check.** The journey's log line contains the correlation id and the outcome. A test or a log assertion fails if the message contains `password`, `cookie`, or the notes fixture string.

**Trigger.** M5, then every later session's core journey.

## Privacy and data

**Bar.** Each stored field has a protection level covering return, logging, retention, and access. The digest's Desk vocabulary is `public`, `account`, and `secret`. A product may keep those names. The status read returns state and holder, and leaves private notes out of the response.

**Where it lives.**

| Field | Level | Return | Log | Retention | Access |
| --- | --- | --- | --- | --- | --- |
| `items.state`, `items.holder_id` | the spec's readable-by-members level | `ItemStatusProps` | id and state only | the spec | any signed-in member, via `ItemPolicy::view` |
| `items.notes`, `items.title` when the spec says account-class | account | omitted from status props. A later spec names who may read notes | never | the spec | the spec's ability, a different Prop class |
| secrets, passwords, tokens | secret | never | never | the framework's user table, hashed | the session guard |

`ItemStatusProps` has no `notes` property. `resources/js/types.ts` repeats that omission. The feature test calls `->missing('item.notes')` on show and on index.

A schema change re-runs the field review. G4 applies the posture. ASVS Level 1 does not require the classification document. Promoting at Level 2 without it does not match that level. The constitution names the level before M7 claims it.

## Change safety

**Bar.** The slice reverts by commit. Migrations are forward-only in shared environments. Once promotion exists, the pipeline can return traffic to the previous candidate.

**Where it lives.**

- Rollback of code: `git revert` of the slice commit, named in the spec.
- A column rename is expand (add the new column), migrate (write both, backfill in batches that stay green), contract (drop the old column in a later slice). `php artisan migrate:rollback` is a local command. It is not the production rollback.
- M7 rehearses promote and rollback on a non-production environment. The first production exposure is the canary in `14-cicd.md`.

**Check.** The spec names the revert. At M7 the rehearsal record shows the core journey green, then red or absent on the rolled-back candidate, then green again on the previous candidate. The exact expected state is the one the previous candidate's features already froze.

## Error shape, one place

`bootstrap/app.php` renders `App\Domain\Desk\Exceptions\ItemException` for Inertia requests as a redirect back (or a redirect to the named page the spec chooses) with session flash:

```text
error.code
error.feature_id
```

`ClaimItemRequest::failedValidation` (or a shared base Form Request) throws a `ValidationException` whose messages are tokens in `error.fields`. The page renders `role="alert"` and `data-error-code={code}`. Playwright asserts the attribute. English sentences live in one map in `resources/js/Components/ErrorText.tsx`. A copy change does not change the oracle.

Unhandled exceptions render a generic code `error.unexpected` and log the exception with the correlation id. The page does not receive the stack, the SQL, or the exception message.

`app/Http/Middleware/HandleInertiaRequests.php` shares `flash.error`.

A React error boundary in `resources/js/Components/ErrorBoundary.tsx` catches render crashes and shows `error.unexpected`. It does not decide authorization and it does not retry the claim.

## What an agent does

- Adds the allow and deny cases in the same slice that touches a critical path.
- Extends `config/desk.php` only with keys the spec named.
- Keeps notes out of `ItemStatusProps` and out of log context.

## What an agent refuses

- A latency key set to `800` or `2000` because a post used those prompts.
- `Log::info($request->all())`, `dd($request)`, or `console.log(props)`.
- Client-only validation as the HTTP seam.
- `RateLimiter` plus a second custom middleware that counts the same submits.
- `migrate:rollback` in the deploy script.
- Catching `QueryException` and returning a 200 with the previous page's props.

## Review checks

- The trigger row for the diff is satisfied (every-change, critical path, or release).
- `config/desk.php` values trace to a spec sentence.
- Show and index assertions both miss `notes`.
- The error flash has `code` and `feature_id`.
- `preventLazyLoading` is on in testing.

## Traps

- Inertia validation errors arrive as the `errors` bag and as the flash, depending on how the exception is rendered. The base Form Request is the one renderer. A controller that also calls `$request->validate()` builds a second bag. Controllers do not call `validate()`.
- `assertSessionHasErrors('title')` passes on Laravel's English message and never locks the token. The assertion reads `error.fields.title` and expects `item.title.required`.
- Sharing the whole user model through Inertia will share hidden attributes the moment `$hidden` is edited. The share casts a small array: `id`, `name`. Email stays out unless the spec's protection level allows it on every page.
- `Http::fake()` in a feature test that is the only fault coverage will pass while the production timeout is unset. The fault test asserts the client was given the spec's timeout, or the journey runs against a stub server that delays. The plan picks one and the evidence names it.
- SQLite in tests and PostgreSQL in production differ on locking. The conditional update is the mechanism because it is one statement on both. A test that uses `lockForUpdate` as the only proof will pass on one engine and deadlock on another. The constitution's engine is the engine the idempotency test runs on when the slice is security-critical.
