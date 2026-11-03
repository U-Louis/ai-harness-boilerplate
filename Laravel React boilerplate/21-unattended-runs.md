# 21. Unattended runs

**Abstract principle.** A session in the inner loop starts because a person opened it on a valid artifact. An unattended run starts because a forge event, a clock, or a failed check fired, on a machine that does not keep the previous chat. The job that calls the model does not hold the token that writes issues, reviews, or the default branch. On a branch that run opened, a workflow that holds a secret waits until a person who did not write the triggering text allows it. The agent does not approve and does not merge. A second agent's ready label is not a start.

Digest: §21. Evidence: `harness-sources/17-unattended-runs.md`.

Vendors say cloud agent, workflow, flow, minion, or factory. This file says unattended run. The name is not a control.

## What may start a run

Three starts. Each one is a row in `docs/exec-plans/decisions.md` before a workflow implements it. The M1 pipeline stub implements none of them.

1. **A person with write access** assigns the issue or mentions the agent. The run receives the title, the body, and the comments that exist at that moment. A comment added on the issue after assignment is outside that run. Follow-ups go on the pull request or merge request. On GitLab the event runs as the intersection of that person and the service account, and the merge request is attributed to the person, because prompting counts as authorship. A bot actor is rejected unless the workflow lists that bot.
2. **A clock.** A scheduled pipeline has no human in the token. The service account's role is the whole permission. That role cannot merge, cannot approve, and cannot read production secrets. A shared automation the forge attributes to the agent needs one further human approval on a repository that already requires an approval.
3. **A failed check.** The run may patch inside the slice, open a merge request when the fix sits outside the diff, or stop at a comment. It stops at a comment when the log is thin or the failure is security-sensitive: secret scan, a Policy test, an auth case, an oracle, a dependency violation. That stop is the ask-first list applied to CI. The repair is a slice. A new behavior still enters at G1.

A second agent applying a `ready` label, or any label whose job is to start an implementer, is not a start. The breakdown is shown to a person before implementation. G2 stays in front of code.

Before a starter merges, the decision-log row answers five questions:

- what starts the run (the event name, the workflow file, the actor)
- what it reads and writes (paths, and which token)
- what it starts next (nothing, or one named job)
- where a human re-enters (G2, G3, GW, or promote)
- what stops two runs from taking the same feature id (an open pull request for that id, or `docs/progress.md` already naming it)

The questions are the checklist. A list of banned synonyms is not a rule. Session, workflow, and job stay usable words.

## What the run may write

The job that calls the model is read-only.

On GitHub Actions that job sets:

```yaml
permissions:
  contents: read
```

It does not set `contents: write`, `pull-requests: write`, `issues: write`, or `id-token: write`. It does not receive a secret that can push. A later job, after the model has exited, applies a structured request with a scoped token. Secrets for that write stay in the later job.

On GitLab the model job's `CI_JOB_TOKEN` cannot push to the default branch and cannot post an approval. A later job applies the write.

A push to one `copilot/` branch, or a push from a devbox to one feature branch, is a published exception. This folder does not install it. A constitution that wants that path records `unattended branch push` in `docs/exec-plans/decisions.md`. The exception covers that one branch. It leaves out approval, merge, and the integration branch.

The runner is disposable. `vendor/` and `node_modules/` caches are caches. The next run reads `docs/progress.md`, `docs/features.json`, and git history. It does not read the previous chat. Instructions stay the short `AGENTS.md` map plus the numbered file the slice opens. Pasting this whole folder into every unattended prompt fills the window. `04-design-principles.md` is the progressive-disclosure rule.

One implementer per working tree still holds. Two unattended runs do not share a checkout. `routes/web.php` still has one writer for the slice.

## What may run, and what may merge

**GW.** On a branch opened from an issue, a comment, a clock, or another run, a workflow that holds a secret does not run until a person who did not write the triggering text allows it.

Secrets in this stack include the production `APP_KEY`, database credentials, a Composer or npm token, a deploy key, and any environment variable the host marks protected. `GITHUB_TOKEN` used only to checkout the same repository and upload a log artifact is not that secret. A job that injects `secrets.*` into the PHP process is.

The issue body is untrusted input. A workflow with secrets, started by that body, gives the injection the secrets. Secret scanning after the run is a later net. It does not replace the wait.

On GitHub the job that needs those secrets uses an environment whose protection rule requires a reviewer. The person who assigned the issue, wrote the comment, or authored the triggering text is not an eligible reviewer of that environment. The same wait covers the case where one automation opens the pull request that would start the next.

On GitLab the secret-bearing job is `when: manual`, with a user who is not the trigger author, or an environment that requires that approval. The scheduled pipeline's service account is not that approver.

The agent does not approve and does not merge. The person who asked does not approve their own request. The ruleset names the app allowed to report a check. It excludes the agent's identity, and any GitHub App the agent can drive, from the review that satisfies the merge. An approval posted with an installation token has satisfied a required review on a repository that allowed it. An automated review that submits an approval when it finds nothing is the same hole. A prompt in this folder does not close it. `14-cicd.md` is the ruleset.

The Approve button is not G4. The forges require a person before the default branch. They do not define that click as reading the diff. Under Read, the person reads the change, including the Pest file and the Playwright spec. Under Metrics, the person reads the evidence and play-tests the booted app, and leaves the agent diff unread. Metrics leaves the diff unread, which fails the published test that a codebase nobody reads has not been reviewed. The conflict stays. Metrics remains a legal posture. The oracle stays in the spec. G3 stays, because the button cannot see whether the frozen example is real. The agent's pull-request summary is neither posture.

A failing loop returns to a human. One CI run, one repair, a second CI run, then the person, is a published stop for a branch. `16-stop-retry-escalate.md` already stops at the third verifier or auditor rejection. The send-back count stays. The CI stop does not raise it.

## What was not installed

A factory that removes the human writer and the human reader, and that stores scenarios outside the repository so the writing agent cannot see them, is published and is not installed. The frozen examples stay in `docs/product-specs/`. The implementer reads the spec. That holdout is weaker than an unseen suite. `13-testing.md` is why a model that can see the test can special-case it. Metrics is a posture in `05-roles.md`. It is not justified by that unseen suite. A token-spend line and a satisfaction fraction from that page are not gates and are not copied into `config/`.

Also refused:

- A reviewer that commits the fixes it is sure of. Findings, then a send-back. `12-review.md`.
- A scheduled job that edits `docs/constitution.md`, `AGENTS.md`, or this folder. Instruction changes stay a risk-class slice a person accepts.
- A merge by the agent once the checks feel trustworthy.
- Replacing the risk-class table in `05-roles.md` with one-way and two-way doors.
- A repository-wide mutation score. Mutation on the slice's action stays the rule in `11-quality-gates.md`.
- An explore task that marks a feature ready and starts P3.
- A decompose task that writes `features.json` and starts the implementer before the steward has seen the breakdown.

## What an agent does

- Leaves the M1 workflow without `on: issues`, `issue_comment`, `schedule`, a `workflow_run` chained from an agent, or a label filter.
- Records a later starter as its own slice: the five answers, the permissions block, the environment that implements GW, and the human who re-enters. The slice is a risk class because it edits the pipeline.
- Treats a security-sensitive red check as a comment and a stop.

## What an agent refuses

- A workflow whose model job has `contents: write`, `pull-requests: write`, or a deploy key.
- `pull_request_target` that checks out the untrusted head and exposes secrets. An unattended issue body is the same class of input.
- Labeling an issue or merge request `ready` so another session starts.
- Opening an implementer because a bot comment said the plan was accepted.
- Copying a production `APP_KEY` into the workflow so an unattended run can boot like production.
- Treating a green required-review check from an app as G3 or G4.

## Review checks

- No starter exists, or the decision log has the five answers and the steward accepted the slice.
- The model job's permissions are `contents: read`.
- Jobs that reference secrets name an environment with a required reviewer who is not the triggering author.
- The ruleset's required reviewers exclude the agent identity and any app the agent can drive.
- `passes` is still set only by the verifier.

## Traps

- GitHub's default `GITHUB_TOKEN` on a workflow triggered by `issues` sits in the same process as the issue body. `contents: read` is the control. Adding `contents: write` so the job can open the pull request removes it. Opening the pull request is the later job.
- A `schedule` event can skip the write-access check some actions apply to comments. The bot check still applies when the forge attributes the cron to a bot. The M1 stub has no schedule, because that token has no person in it.
- GitLab's fix-pipeline flow can apply an inline suggestion on the same merge request. A suggestion that edits a frozen example is an oracle edit. The run comments and stops.
- Caching `vendor/` on the unattended branch is a cache. Caching `.env` is a secret. The cache key is the lockfile.
- Two scheduled runs that both select the same red feature id will both edit `routes/web.php`. The second run sees the open pull request for that id and stops.
- An agent summary that says the oracle holds is not G3. The evidence file and the rule-change output are G3.
