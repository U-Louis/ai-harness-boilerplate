# 12 — CI/CD sources for agent-authored changes

Research note for [§14](../ai-harness-state-of-the-art.md) of the harness dry run. The dry run file was not edited.

Pages below were read on 27 Oct 2026. Living docs (GitHub, GitLab, SLSA, DORA guides) have no publication date on the page; the date column says so. Rank is by how directly the page should constrain this harness, not by fame.

The pipeline rule is the sentence to encode. A prompt that says “do not merge” or “do not promote” is not that rule.

## Ranked sources

### 1. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI (Ryan Lopopolo)
- **Date:** 11 Feb 2026
- **URL:** https://openai.com/index/harness-engineering/
- **Why primary:** This is the team’s own account of a repository where agents wrote the product, the tests, the CI configuration, and the release tooling, then merged. About 1,500 pull requests, three engineers at the start. No second-hand write-up replaces it.
- **Pipeline rule:** The agent may squash-merge its own short-lived pull request when agent reviewers are satisfied and a human is not required. Flakes get another run; they do not stall the line indefinitely. That posture is stated as irresponsible until throughput is high and the repository already has mechanical invariants (custom linters, structural tests, CI). The same post’s isolated run is a per-worktree boot with logs and metrics torn down when the task ends. It is the inner loop, not the CI runner.

What the post actually says, and what it leaves out:

- Humans steer. They are not required reviewers. “Humans may review pull requests, but aren’t required to.” Agents “often squash and merge their own pull requests.” The end-to-end list ends in “Merge the change,” and “Escalate to a human only when judgment is required.”
- “The repository operates with minimal blocking merge gates. Pull requests are short-lived. Test flakes are often addressed with follow-up runs rather than blocking progress indefinitely. In a system where agent throughput far exceeds human attention, corrections are cheap, and waiting is expensive. This would be irresponsible in a low-throughput environment.”
- The scaffold’s first contents included CI configuration. Later, “dedicated linters and CI jobs” check the knowledge base, and custom linters plus structural tests enforce dependency direction. Agents also write “CI configuration and release tooling.” Minimal gates means few gates, and the gates that exist are mechanical.
- The post does not say a consistently red check may be merged. It does not publish a change-fail rate. It does not say who is allowed to flip production traffic. It says the product “ships, deploys, breaks, and gets fixed,” and that this loop “should not be assumed to generalize” without the same repository investment.
- Small cleanup pull requests are “reviewed in under a minute and automerged.” Low friction covers garbage-collection merges, not only feature merges.

### 2. State of AI-assisted Software Development 2025

- **Org:** DORA (Google Cloud)
- **Date:** 2025, report version 2025.2. Survey 13 Jun–21 Jul 2025. DORA’s own year-in-review places the release in September 2025.
- **URL:** https://dora.dev/research/2025/dora-report/
- **Why primary:** Current DORA report on AI and delivery. Nearly 5,000 respondents. It supersedes the direction of the 2024 throughput result. The March 2025 “Impact of Generative AI” PDF restates 2024 and is the wrong citation for the current finding.
- **Pipeline rule:** Record throughput and instability separately. Higher AI adoption is associated with higher software delivery throughput and higher software delivery instability. Do not treat “we can fix it in the next merge” as a reason to skip a pre-production block. The report tests that argument and says it does not hold: instability still harms product performance and burnout, so faster fixes do not cancel it.

Precise claims, so the dry run does not keep the 2024 sign:

- 2024, as restated in this report: about a 1.5% reduction in throughput and a 7.2% increase in instability for every 25% increase in AI adoption.
- 2025: the throughput association reverses, from negative to positive. Instability does not reverse. “AI adoption not only fails to fix instability, it is currently associated with increasing instability.” The report gives direction and standardized effects. It does not publish a new “percent per 25%” figure. Do not invent one.
- AI is an amplifier. Local speed is lost downstream when the platform, tests, and feedback loops are weak. A high-quality internal platform is where AI’s effect on organizational performance becomes positive.
- Individual effectiveness is the wrong success metric. The report’s small-batch capability is fewer lines per change, fewer changes per release, and shorter tasks. That is the measurement behind OpenAI’s short-lived pull requests.
- Rollback shows up as a moderator of team performance under AI, because large batches need a fast undo. The report does not claim rollback by itself reduces instability.

### 3. DORA’s software delivery performance metrics

- **Org:** DORA
- **Date:** undated living guide; read 27 Oct 2026. The fifth metric was added in the 2024 research. Failed deployment recovery time replaced the old “time to restore” wording in 2023.
- **URL:** https://dora.dev/guides/dora-metrics-four-keys/
- **History of the rename:** https://dora.dev/guides/dora-metrics/history/
- **2024 definitions and the production-only failure rule:** https://dora.dev/research/2024/dora-report/2024-dora-accelerate-state-of-devops-report.pdf (version 2024.3)
- **Why primary:** This is the metric definition the 2025 report uses. “The four keys” is the historical name. The current model is five metrics in two factors.
- **Pipeline rule:** Measure three throughput metrics and two instability metrics. Loosen human merge friction only after the two instability metrics and recovery time are actually collected. A red check that stops a change before production is a successful control, not a change failure.

| Factor | Metric | Meaning on this harness |
| --- | --- | --- |
| Throughput | Change lead time | Commit on the slice to production deploy |
| Throughput | Deployment frequency | How often a candidate is promoted |
| Throughput | Failed deployment recovery time | Time to restore after a bad promotion. Not mean time to recover from any outage |
| Instability | Change fail rate | Share of deployments that need an immediate hotfix or rollback after users can hit them |
| Instability | Deployment rework rate | Share of deployments that were unplanned and exist to fix a user-facing bug |

The 2024 report’s scope note: a deployment is a change failure only if it causes an issue after it lands in production. A change stopped on the way is the pipeline working.

The dry run’s “escape” maps to change fail rate. Its “rework” maps to deployment rework rate only when the replacement is an unplanned production fix. Lines replaced inside a slice before merge are not this metric.

### 4. Available rules for rulesets

- **Org:** GitHub
- **Date:** undated; read 27 Oct 2026
- **URL:** https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets
- **Why primary:** Current GitHub control for required checks, who may bypass them, and how an unattributed agent pull request is treated. Classic branch protection is the older surface for the same ideas.
- **Pipeline rule:** Required status checks must pass before merge. Pin each required check to the GitHub App that should publish it. Leave the coding agent off the bypass list. Bypass can be a role, a team, or a GitHub App, and a bypass on a push ruleset covers the fork network. An agent identity with write access can set a commit status; if the rule accepts “any source,” that agent can paint its own check green.

Other rules on the same page that this harness should turn on deliberately:

- Require a pull request. Require approval from someone other than the last pusher when a human must look. Dismiss stale approvals when the diff changes, so an agent cannot push after the approval and keep it.
- Require review from code owners on the risk-class paths (auth, data, pipeline, oracles).
- “Require an additional approval for unattributed Copilot pull requests” is on by default. If Copilot opens the pull request as itself, the ruleset demands one more approval than configured. The setting does nothing when the ruleset requires zero approvals. Zero approvals is a choice, and it is the switch that makes an app-authored pull request merge with no human. Do not arrive there by leaving a default untouched.
- Strict checks (“branch up to date before merging”) are the default. Loose checks allow a green result against a stale base, which then fails after merge. Concurrent agent pull requests need the strict setting. Classic branch protection also offers a merge queue so the combined result is tested before it lands: https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule

### 5. SLSA source track, v1.2 — producing source

- **Org:** SLSA (OpenSSF / industry specification)
- **Date:** specification version 1.2; page undated; read 27 Oct 2026
- **URL:** https://slsa.dev/spec/v1.2/source-requirements
- **Spec index:** https://slsa.dev/spec/v1.2/
- **Why primary:** The supply-chain specification for who may move a protected branch, what evidence the forge must record at that moment, and when a robot may skip review. GitHub and GitLab implement the controls. This page says which controls a consumer is entitled to trust.
- **Pipeline rule:** On the integration line, history is append-only (no force-push), controls stay on continuously, and the forge records who changed the ref and which controls were enforced. Source Level 4 requires two trusted persons on the final revision. A coding agent is not a person. The only spec path for an unreviewed bot merge is an explicit trusted-robot exception, which the organization chooses and which the spec illustrates with import bots and Dependabot. Do not grant that exception to the authoring agent at the start. Turning a control off resets its continuity; a later attestation must not claim the control covered the gap.

Level 3 is the level that matches this dry run before any friction is loosened: the organization names the protected refs, enforces the controls in the forge, and can show they were on for this revision. “All revisions on `main` were tested” is a Level 3 claim only if the forge actually rejects an untested update.

### 6. Deployments and environments

- **Org:** GitHub
- **Date:** undated; read 27 Oct 2026
- **URL:** https://docs.github.com/en/actions/reference/workflows-and-actions/deployments-and-environments
- **How to configure:** https://docs.github.com/actions/deployment/targeting-different-environments/using-environments-for-deployment
- **Why primary:** Official split between merge permission and deploy permission, including self-approval.
- **Pipeline rule:** Production is an environment with required reviewers, prevent-self-review, and admin bypass off. The job cannot read production secrets until a reviewer who did not start the deploy approves it. Restrict deployment branches to the release line. The coding agent is not a required reviewer and is not on the bypass list. One approval is enough, so the reviewer list has to be humans. A prompt in the release-runner contract does not implement this.

The same page states that a job on a self-hosted runner is not isolated, environment or not. Environment protection is an authorization gate, not a clean room.

### 7. Canarying releases

- **Org:** Google SRE (Alec Warner, Štěpán Davidovič, and others)
- **Date:** *The Site Reliability Workbook*, chapter 16 (2018). Official web edition, read 27 Oct 2026.
- **URL:** https://sre.google/workbook/canarying-releases/
- **Rollback sentence from the SRE book:** https://sre.google/sre-book/service-best-practices/ (“Progressive Rollouts,” Ben Treynor Sloss, *Site Reliability Engineering*, 2016)
- **Why primary:** Documented production practice from the organization that runs it: what a canary is, when it is evaluated, and when it is rolled back. A product blog about a canary feature is not this.
- **Pipeline rule:** Promotion into production is partial and time-limited. Compare the canary with the control on a pre-declared signal. If the canary is bad, pause and roll back, then diagnose. The SRE book states the same order: roll back first, diagnose afterward. Run one canary at a time. Size the duration to the deploy rate; a daily or faster line cannot wait a week. A non-production rehearsal proves the path exists. It is not the canary. Blue/green rollback is reversing the router back to the previous color, which is the rehearsed path the dry run already wants, used on a fraction of production traffic first.

The chapter also separates release engineering principles the dry run should keep: reproducible builds, automated tests of those artifacts, automated deploys, and small artifacts because they are cheaper to roll back. “Deployments should be performed by computers, not humans” is about the mechanics of the rollout. The decision to proceed still depends on the evaluation. That fits a human (or a protected-environment rule) allowing the production environment, and a controller moving traffic.

### 8. SLSA build track, v1.2 — producing artifacts

- **Org:** SLSA
- **Date:** specification version 1.2; page undated; read 27 Oct 2026
- **URL:** https://slsa.dev/spec/v1.2/build-requirements
- **Why primary:** The specification that separates a test log from build provenance, and that defines an isolated build.
- **Pipeline rule:** The promoted artifact has provenance that names the artifact by digest and describes how it was built. Build Level 2: the provenance is signed by the build platform’s control plane, on hosted infrastructure, not on a developer workstation. Build Level 3: the signing key is unreachable from the user build steps, every provenance field is generated or verified in the control plane, and each build gets an ephemeral environment. Overlapping builds must not be able to touch each other’s memory. One build must not poison a cache another build trusts. The output must be identical whether the cache is used or not. Level 3 does not make an insecure build script safe. It makes a well-intentioned build hard to tamper with during the run.

OpenAI’s worktree does not meet this. A worktree isolates the agent’s app and its metrics. SLSA isolation is a property of the builder that produces the bytes you promote.

### 9. Artifact attestations

- **Org:** GitHub
- **Date:** undated; read 27 Oct 2026
- **URL:** https://docs.github.com/en/actions/concepts/security/artifact-attestations
- **Why primary:** The forge’s own provenance feature, and its own limits. It names SLSA, Sigstore, and what not to sign.
- **Pipeline rule:** Attest the release artifact, then verify the attestation before promotion (`gh attestation verify` or an equivalent policy check). An attestation binds the bytes to the workflow, repository, environment, commit, and triggering event. It is not a statement that the journeys passed, and GitHub says it is not a statement that the artifact is secure. Do not attest every test build, and do not attest source files or docs. GitHub’s page maps attestations alone to SLSA v1.0 Build Level 2, and says a reusable workflow that isolates the build from the caller is what they count as Build Level 3. Design against SLSA v1.2 (source 8). Treat GitHub’s “v1.0” sentence as their implementation claim, not as the current spec number.

### 10. Protected branches, and the merge checks that sit on top of them

- **Org:** GitLab
- **Date:** undated; read 27 Oct 2026
- **Who may merge or push:** https://docs.gitlab.com/user/project/repository/branches/protected/
- **Which checks block the merge:** https://docs.gitlab.com/user/project/merge_requests/auto_merge/
- **Who may deploy:** https://docs.gitlab.com/ci/environments/protected_environments/
- **Why primary:** Official GitLab split between push, merge, and deploy, plus the project setting that makes a pipeline a merge gate.
- **Pipeline rule:** On the integration line, Allowed to push and merge is No one, so the agent cannot push the branch directly. Allowed to merge is the role or group that may complete a merge request, and that identity is not a Maintainer of the production branch unless a person holds it. Users who can push a protected branch skip merge-request approval, including Code Owners. Turn on Pipelines must succeed, which also refuses a merge when there is no pipeline. A retried job that then succeeds may auto-merge; a new commit cancels auto-merge so the new head is reviewed. That is the flake policy: one successful retry may pass, a rewrite may not inherit the old approval. For a merge request between protected branches, protected CI variables and runners are available only if the user can update both branches. Do not give the agent that permission to “make CI work.” Production deploy is a protected environment with its own Allowed to deploy list. GitLab administrators can use every environment, so admin is not a harmless back door.

Wildcard branch rules use the most permissive match. A loose `*` rule undoes a strict rule on `main`.

### 11. Dependency review

- **Org:** GitHub
- **Date:** undated; read 27 Oct 2026
- **URL:** https://docs.github.com/en/code-security/concepts/supply-chain-security/dependency-review
- **Why primary:** Official control for a new or bumped dependency at pull-request time, which is the moment the dry run’s “ask first” list cares about. Dependabot alerts find vulnerabilities already on the default branch. They are the wrong gate.
- **Pipeline rule:** Run the dependency review action on pull requests. It fails by default when a newly introduced package version is vulnerable. That failure blocks the merge only if the check is required (source 4) or the organization enforces the workflow through a ruleset. The dry run’s dependency scan belongs on the pull request, before merge, and it fails the required check. A report stored in `docs/evidence/` does not.

License allow/deny and a severity floor are configuration. The default is any known vulnerability. For this harness, a new dependency the plan did not name fails closed even if the advisory database is clean: the action’s deny list, or a separate check, has to encode “none” from the spec. The GitHub feature by itself only knows about vulnerable versions, not about an unplanned package.

### 12. About push protection

- **Org:** GitHub
- **Date:** undated; read 27 Oct 2026
- **URL:** https://docs.github.com/code-security/secret-scanning/protecting-pushes-with-secret-scanning
- **Alert-only sibling, not the gate:** https://docs.github.com/en/code-security/secret-security/about-secret-scanning
- **Why primary:** Official control that stops a secret before it is in history. Secret scanning alerts run after the commit, including on historical branches, issues, and pull-request text. By then the secret has already been pushed.
- **Pipeline rule:** Enable push protection on the repository. A push that matches a secret is rejected, including pushes from the API and from the GitHub MCP server on public repositories. A bypass raises an alert; it is not silent. The pipeline’s later secret scan remains useful as a second look, and it blocks merge only when that job is a required check. It does not replace push protection. The dry run’s order (scan after boot, journeys, and tests) is late for this control.

## The tension: blocking checks and low-friction merges

These are different gates. Collapsing them into “CI is strict” or “CI is loose” is how the dry run would mis-copy OpenAI.

| Gate | Start of this harness | What OpenAI loosened | What stays blocking after any loosening |
| --- | --- | --- | --- |
| Human reads the diff | On for the risk classes already in §5 | Humans are not required reviewers | Risk-class paths, ruleset edits, and production promotion |
| Human approves production | On, as an environment reviewer who is not the deployer | Not described in the OpenAI post | Stays. Merge permission does not grant it |
| Invariant checks (schema, structural fitness, journeys, auth cases) | Required, publisher pinned to the CI app | Still present as linters, structural tests, and CI. The post never says a red invariant may merge | Stay required. The agent is not a bypass actor and cannot publish the check |
| Flakes | Retry once. A second red blocks | “Follow-up runs rather than blocking progress indefinitely” | A passing retry may merge (GitLab documents this). A new commit needs a new run |
| Two human approvals on every change | Not required by the dry run for low-risk slices | Not used | SLSA Level 4 wants two trusted persons. Do not pretend two agents are that. Do not grant the trusted-robot exception yet |
| Post-merge fix of a user-facing break | Count it | Described as cheap | It is change fail rate or rework rate. DORA 2025 says the harm remains even when the fix is fast |

OpenAI’s own condition matches the dry run’s caution and is narrower than “turn CI off later.” The sentence is that minimal blocking gates are irresponsible in a low-throughput environment, and that the merge loop depends on tooling that should not be assumed to generalize. The invariants are the part they say to build first. The human review queue is the part they say became the expensive wait.

DORA draws the line in a different place from OpenAI, and the dry run should keep DORA’s line until it has numbers:

- Stopping a bad change before production is not instability.
- An unplanned production fix is instability, even if the next agent merges the fix the same day.
- 2025 finds AI associated with more throughput and more instability. 2024 had found less throughput and more instability. Citing only “AI raises instability” is still directionally right for 2025. Citing 2024’s throughput drop as the current result is wrong.
- The report explicitly rejects the idea that instability is an acceptable trade for AI speed because users are repaired quickly.

So the harness can copy short-lived pull requests, agent merge of low-risk slices, and a single flake retry without copying “corrections are cheap, so a red journey may land.” The second of those is not what the OpenAI post says. The first is.

## Corrections to the dry run

§14 is the target. One §18 sentence is included because it is the DORA claim this research replaces.

1. **Split “red check” from “human wait.”** §14 says the first CI is boring and blocking, and that OpenAI later loosened merge friction because corrections were cheap, until defect escape is measured. Change that paragraph so a red invariant check blocks whether or not escape has been measured. What waits on measurement is dropping the human diff read for low-risk slices. The measurements are change fail rate, deployment rework rate, and failed deployment recovery time (sources 2 and 3). “Defect escape” should mean a failure after production exposure, plus an unplanned deploy to fix a user-facing bug. A red CI job is not escape.

2. **OpenAI did not remove CI.** The same post that describes minimal merge gates also describes CI from the first scaffold, linters, and structural tests, and agents merging only after agent reviewers are satisfied. §1’s “few blocking merge gates” matches the post. §14 should not be read as “they later let red builds through.” The post does not say that. Flakes are what get a follow-up run.

3. **Name two isolations.** §14 defines isolated as “cannot see another slice’s data or another run’s metrics” and points at OpenAI’s worktree. Keep that for the inner-loop boot. Add a second requirement for any job whose artifact can be promoted: ephemeral builder, no cross-job influence, no cache poisoning, provenance key outside the job (source 8). A self-hosted runner is not that, even with a GitHub environment (source 6). A green journey on a shared database is not an isolated run.

4. **The evidence bundle is three objects.** `docs/evidence/<slice>/` is the steward’s record. It is not the merge gate, because the authoring agent can write it. The merge gate is a required check whose expected publisher is the CI app (source 4). The release record is provenance for the promoted digest, verified before traffic moves (sources 8 and 9). Do not attest test builds and call that the evidence bundle. Do not treat a signed attestation as proof the core journey passed.

5. **Promotion is a canary, after a rehearsal.** Keep the non-production rehearsal (promote, core journey, roll back, core journey) and keep the human as the one who may approve the production environment. Add that the first production exposure is a fraction of traffic, compared with the control, one canary at a time, and that a bad signal rolls back before anyone diagnoses (source 7). “Watch the budgets after a full promote” is a later stage. The rollback path has to be the one that was rehearsed, which for a router cutover is reversing the route, not a drive-by commit.

6. **Who may merge is a forge identity, not a role name in the prompt.** The implementer identity may open a pull request. It may merge the integration line only when it is on the merge list, not on the bypass list, and not allowed to push the protected branch (sources 4 and 10). It must not be able to edit the ruleset, dismiss a blocking review, or unprotect the branch. The release identity must not approve its own production deploy (source 6). GitLab push permission skips Code Owner approval; do not grant it to the agent. SLSA’s trusted-robot exception is how an unreviewed bot merge would be represented honestly. Leave it off until the instability metrics exist.

7. **Secrets and dependencies are earlier than the journey stage.** Push protection runs at `git push` (source 12). Dependency review runs on the pull request and blocks only if required (source 11). §14’s sequence can keep a scan job beside the journeys as defense in depth. It should not be the first time a secret or a new vulnerable dependency is able to stop the change. A secret-scanning alert after the commit is already too late to call a block.

8. **§18’s DORA sentence.** “Higher AI adoption can raise delivery instability” still matches 2025. Add the 2025 reversal on throughput, and stop calling the current set the four keys. Recovery time is failed deployment recovery time. Instability is change fail rate plus rework rate. The dry run’s warning that throughput must not silently buy instability is the 2025 finding, including the part the finding adds: fast repair does not make the instability harmless.

Nothing in §14’s stage order (schema, lint, unit and contract, isolated boot, journeys, scans, evidence, audit, G3/G4, merge, candidate, rehearsal, human promote) is contradicted. The corrections are the identity that may satisfy each arrow, the meaning of “isolated” and “evidence,” and the production step after the rehearsal.

## Rejected pages

Read or retrieved, and not used as a ranked source.

| Page | Why it is not a source for this note |
| --- | --- |
| https://understandingdata.com/posts/throughput-inverts-merge-philosophy/ | Paraphrase of the OpenAI post. Adds a claim the post does not make: that a flake which passes on retry is merged and only a consistent failure is real. Useful as commentary. Not a primary. |
| https://concurrent.cd/quotes/lopopolo-merge-conflicts-super-miserable | Auto-caption quote from a talk. The post is the primary for merge philosophy. |
| https://latentspace.podhood.com/a2307cee-fd83-419d-966f-82cbddd41d60 | Podcast summary. “Humans still cut release branches” is not in the OpenAI post. It is not used above. |
| https://ai.engineer/talks/am_oeAoUhew-harness-engineering | Talk page, not the write-up. |
| https://dora.dev/ai/gen-ai-report/dora-impact-of-generative-ai-in-software-development.pdf | DORA’s March 2025 guide. It repeats the 2024 signs (throughput −1.5%, stability −7.2% per +25% AI adoption). The September 2025 report reverses the throughput sign. Cite 2025 for the current result and 2024 for the historical percentages. |
| https://dora.dev/insights/dora-2025-year-in-review/ | DORA’s own summary. Used only to place the 2025 report in September. The report is the primary. |
| https://dora.dev/insights/balancing-ai-tensions/ | DORA essay on the verification tax. Consistent with the report. Not the measurement source. |
| https://www.faros.ai/blog/5th-dora-metric-rework-rate-track-it-now | Vendor interpretation of the fifth metric. |
| https://redmonk.com/rstephens/2025/12/18/dora2025/ | Commentary on the 2025 report. |
| https://www.thoughtworks.com/content/dam/thoughtworks/documents/report/tw_report_state_of_ai_assisted_software_development_2025.pdf | Mirror of the DORA report. Cite dora.dev. |
| https://dl.acm.org/doi/fullHtml/10.1145/3190566 | Google’s Canary Analysis Service paper. Real primary, and narrower than the workbook chapter used here. Not needed once the workbook was read. |
| https://cloud.google.com/blog/products/devops-sre/canary-analysis-lessons-learned-and-best-practices-from-google-and-waze | Product walkthrough (Spinnaker, Waze). The workbook is the practice source. |
| https://github.com/actions/dependency-review-action/blob/main/action.yml | Action inputs, including `warn-only`, which would defeat the gate. Policy is the docs page ranked above. |
| https://docs.github.com/en/code-security/secret-security/about-secret-scanning | Official, and not a merge block. Alerts after the secret is already in the repository. Ranked page is push protection. |
| https://docs.gitlab.com/user/application_security/policies/merge_request_approval_policies/ | Scanner-result approval policies. GitLab states they do not check the integrity of the scan artifacts. The general merge gate is protected branches plus Pipelines must succeed. |
| https://www.oreilly.com/library/view/the-site-reliability/9781492029496/ch16.html | Paywalled copy of the same canary chapter. The sre.google edition is the one cited. |
