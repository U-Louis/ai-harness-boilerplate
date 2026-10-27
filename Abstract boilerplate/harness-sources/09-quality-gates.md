# Quality gates — primary sources

Scope: gate design for an AI coding harness. What blocks, who may waive, and the order in which a check becomes blocking. This note covers the dry run’s [§11](../ai-harness-state-of-the-art.md) (with the merge-friction sentence in §14). It does not restate the testing ladder or the pipeline shape.

Pages below were read on 27 October 2026. Living docs (GitHub, GitLab, ESLint, Google’s eng-practices) have no issue date on the page; the date given is the retrieval date unless a publication date is printed.

Rank is by how directly the page should govern this harness. A vendor synthesis that restates these pages is listed after the rank, even when the dry run cites it.

## Ranked sources

### 1. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI, Ryan Lopopolo
- **Date:** 11 February 2026
- **URL:** https://openai.com/index/harness-engineering/
- **Why primary:** First-party account of the repository the dry run treats as the mature end state. The two policies that look like a contradiction are adjacent sections of this post.
- **Gate rule:** Architectural boundaries and a small set of taste invariants (structured logging, schema and type names, file-size limits, platform reliability rules) are enforced with custom linters and structural tests. The lint text is written to inject the remedy into the agent’s context. Those constraints are an early prerequisite for speed. Separately, once agent throughput far exceeds human attention, the merge policy is minimal blocking gates: short-lived pull requests, flakes handled by a follow-up run rather than an indefinite block, human review not required, agents may merge. The post’s sentence is: “corrections are cheap, and waiting is expensive.” The next sentence limits it: that tradeoff “would be irresponsible in a low-throughput environment.” The end-to-end behavior “should not be assumed to generalize without similar investment—at least, not yet.” The post never says the taste lints are “hard CI failures, not warnings,” and it never names a defect-escape metric as the condition for loosening merges.

### 2. Harness engineering for coding agent users

- **Org:** Thoughtworks, Birgitta Böckeler, published on martinfowler.com
- **Date:** 2 April 2026. The article says it supersedes her 17 February 2026 memo and that the memo URL now redirects here.
- **URL:** https://martinfowler.com/articles/harness-engineering.html
- **Why primary:** This is the control-theory writeup behind “feedforward” and “feedback.” Augment’s three layers are a later vendor arrangement of a distinction she states, and she cites the OpenAI post rather than the other way around.
- **Gate rule:** Guides (feedforward) steer before the agent acts. Sensors (feedback) observe after, and are strongest when the message is written for the model, “a positive kind of prompt injection.” Guides alone never tell you whether the rule worked. Sensors alone let the agent repeat the mistake. Each control is either computational (tests, linters, types, structural checks: deterministic, cheap enough to run on the change) or inferential (rules files, LLM review: probabilistic). Place a check by cost and criticality: fast computational sensors before commit; expensive ones after integration; slow drift sensors on a cadence, outside the change. A prompt file is an inferential guide. It does not block.

### 3. My AI Adoption Journey

- **Org:** Mitchell Hashimoto
- **Date:** 5 February 2026
- **URL:** https://mitchellh.com/writing/my-ai-adoption-journey
- **Why primary:** He coins “harness engineering” in the post Augment said it could not recover. The dry run’s “when the agent makes a mistake, change the harness” line points here, second-hand. The post is a personal practice note, not a merge-gate design.
- **Gate rule:** On each repeated mistake, add a control so that mistake is harder to repeat. Two forms only. (1) A line in `AGENTS.md` for repeated wrong commands or APIs; he reports that this resolved that class of mistake. (2) A programmed tool (screenshot, filtered test) when the agent must be told it is wrong, plus a line telling the agent the tool exists. He does not describe a third “quality gate” layer, an adoption sequence of constraints-then-feedback-then-gates, or a merge policy.

### 4. Software Engineering at Google, chapters 20 and 23

- **Org:** Google; Caitlin Sadowski (static analysis), Rachel Tannenbaum (continuous integration); book edited by Titus Winters, Tom Manshreck, Hyrum Wright
- **Date:** O'Reilly, 2020. Free HTML on abseil.io. The chapter pages do not reprint the year.
- **URLs:** https://abseil.io/resources/swe-book/html/ch20.html and https://abseil.io/resources/swe-book/html/ch23.html
- **Why primary:** The operational rule for when a check is allowed to block a submit, from the organization that industrialized presubmit. This is the page that decides “block everything” versus “block the fast reliable subset.”
- **Gate rule:** A finding developers can ignore is not a gate. Review-time warnings are optional; a presubmit check blocks the commit. Promote a check onto that blocking path only when it is fast and reliable. Slow or flaky tests stay off presubmit. Catch what slips through after submit, and accept some rollbacks. A compiler error is the stronger form, and only for checks that are actionable, have no effective false positives, and concern correctness rather than style. Google’s practice is to fix every existing instance, then turn the check on, so the new rule does not break unrelated work. Compiler output is an error or it is silent. A check that cannot honestly break the build is suppressed or shown in review, not left as a warning people learn to skip. Two green changes can still fail together (a “mid-air collision”); serializing submits removes the gap between the tested tree and the tree that lands.

### 5. The Standard of Code Review, with How to write code review comments and Handling pushback

- **Org:** Google, eng-practices
- **Date:** living public guide, retrieved 27 October 2026
- **URLs:** https://google.github.io/eng-practices/review/reviewer/standard.html , https://google.github.io/eng-practices/review/reviewer/comments.html , https://google.github.io/eng-practices/review/reviewer/pushback.html
- **Why primary:** The human half of “what must block.” Official Google review standard, not a blog recap.
- **Gate rule:** Approve once the change improves the code health of the system, even if it is imperfect. Prefix nits so they do not block. Nothing in the standard allows a change that worsens code health, except an emergency. If the author introduced new complexity, require the cleanup before submit. “A later change” is how codebases decay, unless the mess was already there, in which case the author files a tracked bug. Label required comments differently from optional ones so every remark does not become a gate.

### 6. Rethinking software governance (fitness functions)

- **Org:** Thoughtworks. Rebecca Parsons and Neal Ford, interviewed by Birgitta Böckeler and Scott Shaw. The definition comes from *Building Evolutionary Architectures* (Ford, Parsons, Kua; first edition 2017, second edition 2022).
- **Date:** podcast 17 November 2022
- **URL:** https://www.thoughtworks.com/insights/podcasts/technology-podcasts/rethinking-software-governance-reflecting-second-edition-building-evolutionary-architectures
- **Companion excerpt:** https://www.thoughtworks.com/content/dam/thoughtworks/documents/books/bk_building_evolutionary_architectures_en.pdf (a forbidden dependency fails a unit test before the code is committed)
- **Why primary:** Parsons states the pass/fail rule in her own words. An architectural gate is an objective function. An adjective is not a gate.
- **Gate rule:** “Be maintainable” cannot be a fitness function. “Cyclomatic complexity below five” can, because two people cannot disagree on the result. Once that check fails the build, humans stop re-reviewing the violation. The remaining human decision is the conflict: two fitness functions that cannot both pass. That conversation may soften one of them. It is the waiver, and it is about a real tradeoff, not a style preference.

### 7. GitHub rulesets, required status checks, and merge queues

- **Org:** GitHub Docs
- **Date:** retrieved 27 October 2026
- **URLs:**
  - https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets
  - https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/creating-rulesets-for-a-repository
  - https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue
  - Evaluate mode, Enterprise Cloud: https://docs.github.com/en/enterprise-cloud@latest/admin/enforcing-policies/enforcing-policies-for-your-enterprise/enforcing-policies-for-code-governance
- **Why primary:** The host’s definition of a merge block, a bypass, and a re-check of the combined result. Better than a blog explaining “branch protection.”
- **Gate rule:** A required status check must pass before merge. Anyone with write can set a commit status, so a required check should name the GitHub App that is allowed to report it; a status from anyone else does not satisfy the rule. The bypass list is roles, teams, or apps, not the pull-request author by default. “For pull requests only” forces the bypass onto a pull request and into the audit log, instead of a direct push. A merge queue waits for required checks on a `merge_group` commit, which is the combination with the latest base, not the author’s branch alone. You can require every queued pull request to pass, or only the head of the group. A check that does not report before the timeout is treated as failed. Enterprise Cloud adds an Evaluate status: the ruleset records what would have failed and does not block, which is the trial period before Active. The public repository guide also tells you to use Evaluate before turning on commit-metadata restrictions. Do not assume Evaluate exists for every required check on every plan; a non-required status check is the portable version of the same trial.

### 8. GitLab job control, pipeline-must-succeed, and merge trains

- **Org:** GitLab Docs
- **Date:** retrieved 27 October 2026
- **URLs:**
  - https://docs.gitlab.com/ci/jobs/job_control/
  - https://docs.gitlab.com/user/project/merge_requests/auto_merge/
  - https://docs.gitlab.com/ci/pipelines/merge_trains/
- **Why primary:** The other host’s hard/soft switch and the named role that may skip the combined-result queue.
- **Gate rule:** `allow_failure: true` is a soft job: its failure does not fail the pipeline. A blocking manual job (`allow_failure: false`) stops the pipeline, and with “Pipelines must succeed” the merge request cannot merge. That setting is turned on by a Maintainer or Owner, and it also blocks a merge request that has no pipeline. A skipped pipeline blocks unless a second setting treats skips as success. Merge trains re-run the pipeline on the combined result. “Merge immediately” / skip-the-train is a Maintainer action. GitLab’s docs say the skipped change is not verified against the other merge requests on the train, and that this is for a critical patch, not routine use.

### 9. ESLint Configure Rules, and the known hole in a “ban disable comments” rule

- **Org:** ESLint; the hole is documented by eslint-community on `eslint-plugin-eslint-comments`
- **Date:** current docs, retrieved 27 October 2026. The plugin page is undated.
- **URLs:**
  - https://eslint.org/docs/latest/use/configure/rules
  - https://eslint-community.github.io/eslint-plugin-eslint-comments/rules/no-use.html
- **Why primary:** The linter vendor’s own severity model and the switch that makes inline suppression a no-op. No lab paper was found that measures agents inserting `eslint-disable`. This page is the mechanism. The plugin page is the reason a second lint rule is not the countermeasure.
- **Gate rule:** `"error"` fails the process (exit code 1). `"warn"` reports and leaves the exit code alone, and the docs say that is how you introduce a rule you later promote to `"error"`. Configuration comments outrank the config file. `linterOptions.noInlineConfig: true`, or the CLI flag `--no-inline-config`, makes those comments ineffective. `reportUnusedDisableDirectives` defaults to `"warn"`, so at the default it does not block; set it to `"error"` if unused suppressions should fail the run. It does not stop a suppression that is actually silencing a violation. The community rule that bans directive comments documents a bypass: `/* eslint @eslint-community/eslint-comments/no-use: off */` works, because ESLint applies the directive before the rule runs. Closing the hole is `noInlineConfig` or `--no-inline-config`, plus a config and a workflow the authoring agent is not free to edit. A CLI flag stops comments. It does not stop the agent from setting the rule to `"off"` in the config file CI loads.

### 10. Minions: Stripe’s one-shot, end-to-end coding agents (parts 1 and 2)

- **Org:** Stripe, Alistair Gray
- **Date:** 9 February 2026 (part 1) and 19 February 2026 (part 2), as listed on Stripe’s author index
- **URLs:**
  - https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents
  - https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2
- **Why primary:** A first-party agent harness that already had millions of tests. It states where feedback sits relative to CI, and it caps the repair loop. Böckeler cites this series for “shift feedback left.”
- **Gate rule:** Any lint that would fail CI should fail in the local loop first (their local lint pass is under five seconds). Deterministic steps (run the linters, push) are code in the workflow, not a hope that the model remembers. CI is at most two rounds: autofix what the tooling can autofix, send the rest back once, then stop and hand the run to a human. Further CI rounds have diminishing returns. Human review of the pull request remains; the agent does not merge itself in this account.

## What blocks, who waives, in what order

These ten pages support one design. The sentences are the rules. The sources are the numbers above.

**A check blocks only when all of these are true.**

- The result is objective. Two readers get the same pass or fail (6). An adjective (“maintainable,” “clean”) is a review comment, not a gate.
- It is computational: a linter, a type checker, a structural test, a required status. A line in a prompt is a guide (2, 3).
- It is fast, and it does not fail for reasons unrelated to the change. Flaky or slow checks stay off the blocking path; the miss is caught afterward and may roll back (4, 1).
- The failure text tells the agent the remedy (1, 2).
- The authoring agent cannot satisfy it by editing the checker, posting the status, or suppressing the finding (7, 9).
- It has been shown on this codebase, or the tree is empty and the check is one of the few boundaries you are willing to fix everywhere before turning it on (4, 9).

**What that set contains at the start.** Dependency direction and other boundaries, validation at the edge, the secret and auth cases the dry run already calls high findings, and the schema of the harness artifacts. OpenAI’s taste list (structured logging, names, file size, reliability) belongs here when each item is an objective lint with a remedy, because those are what keep later agent runs able to read the tree (1). Formatting and naming nits that a human would prefix with “Nit” do not (5). Augment’s sample caps (complexity 10, 50 lines per function, and the rest of that block) are Augment’s illustration. OpenAI does not publish those numbers.

**What blocks later, or off the merge path.** A journey or a load run blocks a slice only while it stays fast and deterministic. The moment it flakes, it teaches the agent to retry until green, which both Google and the dry run’s own test rules treat as a broken signal (4). Capacity budgets can gate promotion without gating every slice. That matches the dry run’s GC row.

**The merge block is a re-run on the combined tree.** A green slice branch is the author’s evidence. The host blocks on required checks of the merge queue or the merge train (7, 8). Google’s mid-air collision is the same fact (4).

**Who may waive.**

| Waiver | Who | What they may waive | Record |
| --- | --- | --- | --- |
| Soft finding | Steward, as the dry run says | A low finding that was already in the tree, or a budget not yet at the promotion gate | Reason plus a follow-up slice (5: pre-existing mess gets a bug; new complexity the author added does not) |
| Conflict of two real invariants | Steward | Soften one fitness function when both cannot pass | The tradeoff, in the decision log (6) |
| Emergency skip of the combined-result queue | A named human who is not the implementer | The wait for the queue, for a critical patch | GitHub: bypass list, “pull requests only,” so it is an audited pull request (7). GitLab: Maintainer, merge-immediately, and the docs say the skipped change is not verified against the rest of the train (8) |
| The authoring agent | Nobody | Nothing. Not the status, not the config, not `eslint-disable`, not the bypass list | A failed or missing check stays failed (7, 9) |

“Hard means no one may ever waive” is stricter than either host. Keep that strictness for the implementer. Add one logged steward bypass for a downed queue or a production patch, and do not give it to the agent. A downed check pauses the gate. It does not become a skip the agent can take. GitHub’s timeout already fails closed (7). That matches the dry run’s “tooling is down: pause, do not skip.”

**Order of adoption.**

1. On an empty repository, put the short objective boundary list in as computational checks with remedy text before feature speed (1). Do not wait for the first invented auth path. Hashimoto’s “add a control after the mistake” rule (3) is for the next surprise, not for boundaries you already know.
2. The same day, the agent runs those checks and reads the remedy. A failure that exists only at merge time is a late sensor (2, 10).
3. A new check on a tree that already violates it: fix or baseline the existing hits, watch the false positives, then fail the process. ESLint’s own path is `"warn"`, then `"error"` (9). GitHub Enterprise’s path is Evaluate, then Active (7). Google’s path is clean up the corpus, then break the build (4).
4. Bound the repair loop. Two CI rounds, then a human (10). The dry run’s two verifier rejections match this. Do not hold the merge open on a flake (1, 4).
5. Human review of every diff is the expensive wait OpenAI removed, after the invariants existed and throughput made waiting cost more than a correction (1). Until that is true, a human still merges, and copying the removal early is the case the post calls irresponsible.

Augment’s “constraints, then feedback, then gates” matches step 5 if “gates” means broad human merge friction. It does not match steps 1–2. The linter is both the constraint and the feedback, and it is present from the first commit.

## Conflicts

**“Block everything” versus “corrections are cheap, waiting is expensive.”** They describe different waits.

- OpenAI still blocks the agent, inside the run, on boundary and taste lints whose text says how to comply (1). The wait they removed is a human, or an indefinite hold for a flake, after those lints exist and the team is merging faster than people can read (1).
- Google blocks the submit on the fast reliable subset and refuses to block on flakes, slow end-to-end tests, or warnings people click through (4). Reviewers block a change that worsens code health or that adds complexity the author should clean up now, and they do not block on a nit (5).
- Thoughtworks blocks the build on an objective fitness function and spends the human on the conflict between two of them (6).
- The hosts block the merge on required checks of the combined result, with a privileged, audited bypass (7, 8).

A young harness follows the low-throughput sentence in the OpenAI post: keep the merge block. It also follows Google: the block is the small objective set, not every check you can imagine. The mature OpenAI posture (agents merge, humans not required) is an outcome of the invariant layer plus measured throughput, and the post says not to assume it travels.

**Feedforward, then feedback, then gates.** Böckeler requires guides and sensors together (2). Hashimoto adds whichever of the two forms the mistake needs, when the mistake recurs (3). Augment’s sequence is Augment’s. See the demoted section.

**File size and naming.** OpenAI enforces them as taste invariants because later agents must read the tree (1). Google’s compiler bar excludes style and best-practice noise (4). Both fit if a cap is justified as an agent-legibility invariant with a remedy, and a human stylistic preference stays a nit (5).

**Low findings never block.** Google agrees for nits, and disagrees for new complexity this change introduced (5). Pre-existing mess is a follow-up. Fresh mess blocks.

**Warnings as a kindness.** ESLint and Google both use a non-failing report as a temporary observation state (9, 4). Google then deletes warnings that people ignore, because a permanent warning is a signal the culture has agreed to skip. A warn that lives forever is not a soft gate. It is an unenforced wish.

## Corrections to the dry run

The dry run file was not edited. These are the changes §11 and the one §14 sentence should absorb.

1. **Re-attribute the layer model.** §11’s “Implement in this order (Augment)” should name Böckeler for feedforward versus feedback, Hashimoto for “a repeated mistake becomes a prompt line or a tool,” and OpenAI for “boundaries first, merge friction last.” Augment is a synthesis dated after those three, and it misstates the OpenAI post (next item). Keep Augment’s warning that over-constraining is a failure mode: complexity caps set too low flag legitimate refactors. That warning also follows from Google’s “no effective false positives” (4).

2. **§14 overstates the OpenAI condition.** “Until defect escape is measured, a red check blocks” is the dry run’s inference. The post’s condition is: low throughput makes minimal merge gates irresponsible; high throughput plus an existing invariant harness makes waiting more expensive than a correction; the result should not be assumed to travel. Defect escape is a sensible extra measurement (the dry run’s §18). It is not what Lopopolo wrote. Also split the two OpenAI policies in §11 so a reader cannot collapse them: taste lints block the agent’s loop from the start; human merge approval is what they later stopped requiring.

3. **The constraint layer in §11 is a set of guides, not a gate.** The map, the allowed paths, the ask-first list, and the never-list are inferential feedforward (2). They do not block. The block is the computational check with remedy text. §2 already says instruction files are probabilistic. §11 should not then present those lists as the layer that stops the agent.

4. **A gate has to earn the block.** The “start / hard” column marks G1, G2, GP, GO, GI, GV, GA, G3, G4, GS, and GG hard immediately. Keep the human schema gates (G1, G2, G3, G4, GG) hard: a missing field is objective and cheap. For tool gates (GO, GI, GS, and GC when it is on), add Google’s test: the command is fast, deterministic, and not flaky, and the agent cannot forge the result. A journey that has started to flake comes off the hard path the day that happens. GC soft before M7 and hard at promotion already matches “slow checks do not block every submit.”

5. **GI is feedback unless a reporter the agent does not control re-runs it.** “Implementer self-check green” is Stripe’s shift-left loop (10), which is right and is not yet a merge gate. The merge gate is a required check on the merge-group or merge-train commit, reported by a pinned app, with the implementer absent from the bypass list (7, 8). Say that in the enforcement table so GI and the host rule are not the same row.

6. **GL’s low findings.** Send pre-existing nits to the gardener. Block new complexity this slice added, unless the steward explicitly defers it with a tracked slice (5). “Low” should not mean “the author may leave the mess they just made.”

7. **Name the steward emergency bypass.** Hard gates stay unwavable by the implementer. A named steward may bypass a stuck combined-result queue, on a recorded pull request, and may not use that bypass to skip a secret, auth, or oracle failure. Host docs already have this role (7, 8). §11’s “hard gates may not” currently has no emergency actor, which will be reimplemented as someone turning the check off.

8. **Inline suppression.** §2 attributes “disable inline suppression, or the agent will silence the rules” to Augment. Keep the conclusion, change the source. ESLint documents the escape hatch and the off switch (`noInlineConfig`, `--no-inline-config`). A lint rule that forbids `eslint-disable` can be disabled by an `eslint` comment, and the plugin authors say so (9). The CI command has to pass `--no-inline-config`, and a change to the linter config or the workflow stays on the human-read risk list the dry run already has. There is no published lab count behind the behavioral claim. Practitioner blogs repeat it; they are listed under rejected pages.

9. **Do not import Augment’s sample numeric caps as the OpenAI taste set.** OpenAI names file-size limits and does not publish a threshold. If a cap is adopted, it needs a legibility reason, a remedy string, and a false-positive watch (1, 4, 9), the same as any other new check.

## Not primary

**Augment Code, “Harness Engineering for AI Coding Agents,” Molisha Shah, 16 April 2026, updated 29 June 2026.** https://www.augmentcode.com/guides/harness-engineering-ai-coding-agents

Useful as a map, and it is the page the dry run cites for the three-layer order, for “over-constraining,” and for disabling `eslint-disable`. It is not the origin of those ideas. Feedforward versus feedback is Böckeler (2 April, itself after a 17 February memo). The repeated-mistake rule is Hashimoto (5 February); Augment says that URL had not been recovered, and it is the Hashimoto post above. The taste-invariant paragraph says OpenAI enforces them “as hard CI failures, not warnings.” Those words are not in the OpenAI post. The sample ESLint caps are not OpenAI’s published thresholds. Rank Augment under the pages it summarizes, and do not let it adjudicate a conflict with Lopopolo.

**Augment Code, “Quality Gates in Software Development.”** https://www.augmentcode.com/guides/quality-gates-software-development

A five-station manufacturing analogy (intake, spec, build, verify, ship) with owning roles. It restates “ambiguous work does not proceed” and “ship needs a rollback and a named owner,” which the dry run already has as G1, G2, and GR. No finer rule about what is hard, who waives, or when a check earns a block.

## Rejected pages

| Page | Why it was set aside |
| --- | --- |
| https://futureagi.com/blog/harness-engineering/ | Recap of Augment’s three layers. |
| https://www.engineering.fyi/article/harness-engineering-leveraging-codex-in-an-agent-first-world and the Stripe mirrors on the same host | Mirrors. The OpenAI and Stripe originals are the sources. |
| https://latent.space/p/harness-eng/ | Interview. The 11 February post is the citable policy. The transcript was not used to extend it. |
| https://developertoolkit.ai/en/workflows/test/code-quality/ | Method site. Restates ESLint severity, required checks, and “don’t let the agent edit the config.” The dry run already treats this site as a method source. |
| https://axonbuild.com/blog/linters/ (3 October 2026) | Practitioner essay. The suppression survey is useful color. The rule it depends on is the ESLint page. |
| https://medium.com/@albro/eslint-as-ai-guardrails-the-rules-that-make-ai-code-readable-8899c71d3446 | Anecdote that one model wrote `eslint-disable-next-line` and another edited the config. Not a study. |
| https://paulwade.dev/blog/eslint-rule-refuses-agent-write | First-person hook recipe. Agrees that ESLint honors disable comments unless `allowInlineConfig` is false. The ESLint docs are the source for that fact. |
| https://github.com/motchalini-llc/suppress-ratchet | Tool README. “Agents silence lints to get CI green” is the motivation blurb, not evidence. |
| https://gigazine.net/gsc_news/en/20260211-my-ai-adoption-journey-6-steps/ | News recap of Hashimoto. |
| SonarQube quality-gate docs | Product conditions for a pipeline. Gate mechanics are the GitHub and GitLab pages. A Sonar condition would belong in a CI dossier. |

No paper or lab post was found that counts how often coding agents insert `eslint-disable`. The behavioral claim stays a practitioner report. The countermeasure does not: it is ESLint’s `noInlineConfig` / `--no-inline-config`, a config the agent cannot rewrite, and a required check whose status the agent cannot set.
