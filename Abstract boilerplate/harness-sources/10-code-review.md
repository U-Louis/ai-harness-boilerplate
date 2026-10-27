# 10 — Reviewing agent-written code

Sources for dry-run [§12](../ai-harness-state-of-the-art.md): when a change is reviewed, who reviews it, and what that reviewer reads. The dry run was not edited.

Pages were fetched on 27 Oct 2026. Ranking follows how directly a source constrains the rule “a human reads evidence, and reads the diff only when a path hits the risk list.” A living product doc ranks below a report of practice, because the product and the vendor’s own repositories disagree.

## Ranked sources

### 1. What to look for in a code review; Navigating a CL in review; The Standard of Code Review

- **Org:** Google, engineering practices (canonical code-review guide)
- **Date:** Repository created 4 Sep 2019. Last push 19 Sep 2024. Archived read-only 21 Nov 2025. Pages still served.
- **URL:** [What to look for](https://google.github.io/eng-practices/review/reviewer/looking-for.html), [Navigating a CL](https://google.github.io/eng-practices/review/reviewer/navigate.html), [The standard](https://google.github.io/eng-practices/review/reviewer/standard.html). Overview, including the definition of review: [Introduction](https://google.github.io/eng-practices/review/).
- **Why primary:** Google’s own normative guide. The overview calls it “the canonical description of Google’s code review processes and policies.” It is the conventional baseline the later risk-tier pages are arguing with.

**When.** Every change is reviewed before it lands. The reviewer starts with the description: if the change should not exist, reject it immediately and say what to do instead, rather than after a full read. Then the main part of the change. Design problems go back immediately, before the rest of the files, because the author is often already building on that change. Only after the design holds does the reviewer go through the remaining files. Reading the tests before the production code is an allowed order.

**By whom.** “Someone other than the author(s).” If the assigned reviewer is not qualified for privacy, security, concurrency, accessibility, or internationalization, another reviewer is added for that part and the split is written down. The reviewer owns the code health of what they approve. Approval means the change improves the health of the system, including when it is not perfect. Nits are labeled and do not block.

**What is read.** Design first, then functionality (edge cases, concurrency, user-visible behavior), complexity and over-building, tests, names, comments, style-guide compliance, documentation, and context outside the hunk (the whole file, and the health of the system). “In the general case, look at every line of code that you have been assigned to review.” Data files and generated code may be scanned. A human-written class, function, or block may not. “Tests do not test themselves, and we rarely write tests for our tests — a human must ensure that tests are valid,” including whether a test would fail if the code broke. For a UI change, reading is a weak substitute for running it or watching a demo. For races and deadlocks, running is the weak substitute, and both author and reviewer have to think the interleaving through.

### 2. Modern Code Review: A Case Study at Google

- **Org:** Google and University of Zurich. Caitlin Sadowski, Emma Söderberg, Luke Church, Michal Sipko, Alberto Bacchelli.
- **Date:** ICSE-SEIP, 27 May 2018. DOI [10.1145/3183519.3183525](https://doi.org/10.1145/3183519.3183525).
- **URL:** Author version: [icse2018seip.pdf](https://www.ifi.uzh.ch/dam/jcr:d3de75f7-1464-4463-a539-dde7714f0035/icse2018seip.pdf). Record: [research.google](https://research.google/pubs/modern-code-review-a-case-study-at-google/).
- **Why primary:** Peer-reviewed study of the practice behind source 1. Twelve interviews, 44 survey responses, logs of about 9 million reviewed changes. This is what conventional review was for, measured, before agents.

**When.** Review has been required since early in the company’s history. Median time to first feedback is under an hour for small changes and about five hours for very large ones. Median time for the whole review is under four hours. Median change is 24 lines; about 90% of changes touch fewer than 10 files. More than 80% of changes need at most one round of comment resolution.

**By whom.** A directory owner must approve, and owners are expected to have their own changes reviewed. Every change is authored or reviewed by someone with language readability. Usually one reviewer satisfies both. Fewer than 25% of changes have more than one reviewer; median reviewer count is 1. The tool suggests the smallest set of reviewers who cover the files, biased to people who recently edited or reviewed them. The author previews the diff and the analyzer output, then sends it. Analyzers and pre-commit tests are visible in the review; they do not replace the owner’s approval.

**What is read.** Reviewers read the diff in the tool, with analysis results beside it, and can diff any pair of snapshots. Expectations are education, norms, gatekeeping, and accident prevention. Finding 1: expectations “do not center around problem solving.” Review was introduced so later engineers could read the code. Defect finding is welcomed and is not the purpose. Understanding the change is described as a major hurdle. At this volume, one careful reader of a small diff is the norm, not a panel and not a skipped diff.

### 3. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI. Ryan Lopopolo, Member of the Technical Staff.
- **Date:** 11 Feb 2026.
- **URL:** [openai.com/index/harness-engineering](https://openai.com/index/harness-engineering/).
- **Why primary:** OpenAI’s own account of an internal product built with no hand-written code. This is the page the dry run cites for agent-to-agent review and for humans not sitting on every pull request. It is one repository’s outcome, and the page says so.

**When.** Review is inside the loop that drives a pull request to completion, not a single gate at the end. The authoring agent reviews its own changes locally, requests further agent reviews locally and in the cloud, answers human or agent comments, and repeats until the agent reviewers are satisfied. Humans are optional on a given pull request. “Escalate to a human only when judgment is required.” Short-lived pull requests, minimal blocking merge gates. Cleanup pull requests are reviewed in under a minute and often automerged. The page’s condition for that looseness: corrections are cheap and waiting is expensive, which they treat as a property of this repository after months of tooling, “and should not be assumed to generalize without similar investment — at least, not yet.”

**By whom.** The authoring agent is a reviewer of its own diff and is not the only reviewer. Other agents review. Humans “may review pull requests, but aren’t required to.” Over time “almost all review effort” moved to agent-to-agent. Agents often squash and merge their own pull requests. Humans still prioritize work, turn user feedback into acceptance criteria, and validate outcomes. Early on, human review was the bottleneck (three to five pull requests per engineer per day on a team of three). Recurring human comments were encoded into docs, tests, and reviewer prompts. Friday human cleanup did not scale; a background pass did.

**What is read.** The post does not give a hunk checklist. Humans steer by prompts and by outcomes: acceptance criteria, and whether the running system matches them. Agents read the repository, review comments, and the app’s UI, logs, and metrics, which were made legible so human QA would not have to be the bottleneck. Human taste still enters, as comments that become docs or checks. “The resulting code does not always match human stylistic preferences,” and that is accepted when the output is correct, maintainable, and legible to the next agent. What they report not knowing: where human judgment adds the most leverage, and how architectural coherence holds over years.

### 4. How Anthropic secures its AI-native software development lifecycle

- **Org:** Anthropic. Jason Clinton, Deputy CISO. Michael Segner credited.
- **Date:** The article page showed no date. Contemporary indexes date it 21 Jul 2026. Fetched 27 Oct 2026.
- **URL:** [claude.com/blog/how-anthropic-secures-its-ai-native-software-development-lifecycle](https://claude.com/blog/how-anthropic-secures-its-ai-native-software-development-lifecycle).
- **Why primary:** First-person account from the security organization of how review works when Claude authors about 80% of merged code. It is the security constraint on “humans not on every diff,” not a product brochure.

**When.** Review is spread across plan, generation, CI, deploy, and monitor. At plan time, an automated project-security review can let a team approve its own launch once Claude has been trusted to call the risk low. During generation, security review moved from “before the pull request” into the session that writes the code. The hard gate they chose is CI, not the editor. After production, an incident agent may draft a fix and may not deploy it.

**By whom.** “Human accountability is still central.” Human review is reserved for regulated or truly critical code. The codebase is tiered; entire codebases keep strict human approval. More than half of code is merged by an internal “Claude Tag,” while engineers direct, set intent, and own final approval. On a pull request, several agents review, each with a narrow focus and its own context, plus retrieval over past incidents. Reasons given: they do not share one bias, one compromised reviewer can be caught by another, and effort is not spread across every concern in one prompt. A new reviewer starts in shadow mode until humans have approved its comments. A risk-weighted sample of automated approvals is reviewed by people. Cross-actor invariants (“user A can never read user B’s data”) trigger additional manual review. Agents do not merge to production unchecked.

**What is read.** Security review looks for attacker-controllable input and suspicious links, then verifies its own findings. Agents need context outside the diff: policies, past decisions, related systems. Humans, in the steady state he describes, read the loops, the dashboards, and the sampled approvals more than every bug report. Every automated approval is logged with the signals behind it. He cites an internal before/after: the share of pull requests with substantive review comments went from 16% to 54% once agents had to write a proof that a finding was valid, and about a third of the bugs behind past claude.ai incidents would have been caught by the automated process they now run. Those are internal figures, not a public benchmark.

### 5. How the Claude Code team does code review

- **Org:** Anthropic, Claude Code team. Addy Osmani, Morgan Westlee Lunt, and Qing Wang. claude.dev describes itself as writing from Anthropic’s developers.
- **Date:** 15 Oct 2026.
- **URL:** [claude.dev/blog/solving-code-review-at-anthropic](https://claude.dev/blog/solving-code-review-at-anthropic/).
- **Why primary:** The engineering team’s own review policy for the Claude Code repositories, three months after source 4, with an explicit risk table. It is the clearest primary on conventional versus risk-based review for agent-written changes. It is a blog post about one set of repositories, and it distinguishes itself from the product (source 6).

**When.** Review runs continuously, before a pull request exists and again on the pull request. Most changes have been reviewed and fixed by Claude at least once before a person is asked to look. A fresh-context subagent reviews the diff after the authoring session has produced runnable evidence (tests, a build, or a screenshot). Bug-hunt, simplify, and end-to-end checks often run before open. On the pull request: a bug review, a security review that blocks while a finding is open, CI, then a human approval whose count depends on risk, then a merge queue and a staged release. Re-review is capped (they name three rounds, then a step back). After the first passes, re-review is diff-only. They measured reviewer loops that re-flagged old code (about 59% of repeated flags were already present on the first pass) and stopped treating “another full pass” as the default.

**By whom.** Low: no human approval once automated review and security checks are green. Examples they give: docs, copy, internal-only features, additive changes behind a flag. Medium: one person. Examples: ordinary product changes, and sensitive areas such as caching, the auto-updater, or system prompts. High: two people, for the highest-risk code and for a small set of paths that always need two. A classifier sets the tier from the change. A second classifier can demand another person even when the tier does not. Paths owners marked sensitive, and any CI workflow file, always need a person. Only authors on a trusted list get the lighter paths. Everyone else needs a second person. “An agent’s approval never counts as a person’s.” On an agent-written change, the person who prompted it counts as a required reviewer wherever a person is required, “the same as if they’d typed the code themselves.” An approval covers one commit; an agent push invalidates it. The stated goal is at most one human on most pull requests, who may be the author, with revert and incident rates as the guardrail. They do not yet automatically tighten approval when those rates move, and they say engineers want that before more code moves to zero-human merges.

**What is read.** The automated reviewer reads the diff against the repository and the pull request’s stated intent: does it solve the problem, fit the system, or break something else. On a bugfix it looks for the root cause. Several agents look for bugs; a separate group votes before a finding is posted. Style, diagnostics, and cleanup are dropped. `REVIEW.md` (from code owners, not from the pull request) says what failure looks like here, caps nits, lists paths to skip, and asks for a file and line. `CLAUDE.md` is loaded and the reviewer flags both violations and lines the change has made stale. Humans stay on: whether to build it; risky code (they name billing, the auto-updater, CI); architecture that other changes will copy; and the evidence. The evidence sentence is the one the dry run is missing: “Reading the evidence. Test output, the command that ran and what it returned, a screenshot, the reviewer’s findings. If you can’t say what the checks would miss, ask for a real review rather than a quick approval.”

### 6. Code Review, and Review locally with /code-review

- **Org:** Anthropic, Claude Code product documentation.
- **Date:** Living docs, fetched 27 Oct 2026. Local `/review` became an alias of `/code-review` in v2.1.223; before that, `/review` was a single-pass read-only GitHub review.
- **URL:** [Code Review](https://code.claude.com/docs/en/code-review), [Review locally](https://code.claude.com/docs/en/code-review-local).
- **Why primary:** The product contract for the feature source 5 says is the public, weaker version of their internal reviewer. It states what the managed reviewer reads and what it is forbidden to decide.

**When.** Per repository: when a pull request opens, after every push, or only on request (`@claude review` from someone with write access). Drafts and forks are skipped. Locally, `/code-review` runs on the current branch, a range, a pull request, or a path, in a background subagent with its own context. The authoring session can invoke it. Effort is chosen (`low` through `max`, plus `ultra` in a cloud sandbox).

**By whom.** Managed review: several agents read in parallel, then a verification step checks candidate findings against the code and drops false positives, then rank and post. “A review never approves or blocks the pull request, so the decision to merge stays with your team.” The check run does not fail the pull request. The docs say a review with no findings does not mean the code is error-free or secure. Locally, the reviewer is a subagent; `--fix` lets it edit the working tree, which is the opposite of a read-only verifier. Managed instructions come from `REVIEW.md` on the base branch, never from the pull request’s own edits. The local command follows `CLAUDE.md` and does not read `REVIEW.md`.

**What is read.** “A review works from reading the diff and the surrounding code, without building or running your code.” Default focus is correctness: bugs that would break production, not formatting or missing test coverage. Repository guidance can add other passes. Review level (Quick, Standard, Deep) changes thoroughness, and an owner can set Deep on one repo and Quick on another. Access during the review is read-only on the repository.

### 7. Review GitHub pull requests with Codex; Codex use case; code-review cookbook prompt

- **Org:** OpenAI, Codex product documentation.
- **Date:** Living docs, fetched 27 Oct 2026. No publication date on the pages.
- **URL:** [Review GitHub pull requests with Codex](https://learn.chatgpt.com/docs/third-party/github), [Use case](https://developers.openai.com/codex/use-cases/github-code-reviews/), [Cookbook prompt](https://developers.openai.com/cookbook/examples/codex/build_code_review_with_codex_sdk).
- **Why primary:** The shipping reviewer, as distinct from the internal harness in source 3. The use-case page is explicit that this pass sits in front of human merge approval. A direct fetch of `codex-rs/core/review_prompt.md` returned 404 the same day; the rubric below is the one the cookbook publishes, not an unreproducible repo file.

**When.** Automatic, on the repository’s review trigger, or on `@codex review`. `@codex security review` is a separate, deeper pass. The use-case page: “Catch regressions and potential issues before human review,” for “teams that want another review signal before human merge approval.”

**By whom.** Codex posts as a reviewer. It is not the approver. “Code review rules guide Codex; they don’t replace tests, branch protections, or required approvals.” A later `@codex fix` can push a change when it has permission. Security Review is an additional pass over the same pull request, using threat-model context, and can overlap the general review.

**What is read.** The pull request diff, repository guidance, and `AGENTS.md` rules nearest the changed files (`## Code Review Rules`), root plus the nested file that covers each path. Rules are supposed to be consequential behavior: a compatibility constraint, a data boundary, an unsafe side effect, and the safe path. Formatting and lint stay in CI. On GitHub, posted comments are P0 and P1 only. A one-off comment can narrow the read (`@codex review for issues in the database migration`). The cookbook prompt tells the model it is reviewing another engineer’s change; to flag correctness, performance, security, maintainability, and developer experience; to flag only actionable issues the pull request introduced; to cite file and line; to skip nits unless they block understanding; and to end with a correctness verdict and a confidence score.

### 8. Agentic AI and Code Reviews: Toward a Pattern Language for Code Reviews in the Age of Agentic AI

- **Org:** Enterprise Technology Leadership Journal (IT Revolution), Vol. 3 No. 2, Fall 2026. Zach Davis, Michelle Gill, Elisabeth Hendrickson, Angie Jones, Sha Ma, Randy Shoup, James Wickett. Affiliations named on the reader page include LaunchDarkly, GitLab (Gill), CircleCI (Shoup), DryRun Security, Agentic AI Foundation.
- **Date:** 28 Sep 2026. 31 pages. CC BY-NC-SA.
- **URL:** [Publisher page](https://itrevolution.com/product/agentic-ai-and-code-reviews/). Public talk with the definition read aloud: [Patterns and Case Studies](https://videos.itrevolution.com/watch/1233844564/).
- **Why primary:** This is the paper GitLab’s checkpoint page cites for the claims the dry run treats as GitLab’s. It is a practitioner journal, not a CS lab study. **Limit:** the full PDF was not read in this pass. Claims below are from the publisher’s abstract and from the authors’ public talk. Do not treat unaudited pattern names from the talk listing as checked against the PDF.

**When.** Throughout the delivery cycle, not only at merge. The publisher’s three conditions before a human leaves the critical path: deterministic checks first, risk-tiered review, and an inspectable record of every decision. “Trust earned, not assumed.” Patterns are “a toolbox, not a maturity ladder,” adopted as confidence in automated verification grows. Shoup’s point in the talk: human-authored and human-reviewed code could get away with less systemic rigor; machine authorship plus machine review cannot.

**By whom.** Gill reads the paper’s definition: “Examination of source code by an entity that is not the author, with the intention of making a judgment that leads to an action.” The action is reject, merge, or request a specific change. The non-author may be a person or another system. The author is excluded because they are heads-down in the change. The goal they state is to remove humans from the critical path to a degree that depends on maturity, domain, and constraints. Two reasons to examine the source: readability and maintainability, and whether the behavior is the intended one, which they call hard for the model.

**What is read.** Source, by a non-author, aimed at a decision. The record of the decision has to be inspectable afterward. The publisher summary says the value that outlasts bug-finding is risk triage. That is the conventional-to-risk-based move in one sentence, and it still defines the activity as examining source.

### 9. Build human checkpoints into the workflow; Verify what agents produce

- **Org:** GitLab, AI-native engineering playbook.
- **Date:** No date on the pages. Fetched 27 Oct 2026. Both pages’ substantive pattern is footnoted to source 8.
- **URL:** [Checkpoints](https://about.gitlab.com/ai-native-playbook/topics/working-with-agents/build-human-checkpoints-into-the-workflow/), [Verify](https://about.gitlab.com/ai-native-playbook/topics/governing-and-scaling/verify-what-agents-produce/). Companion, same playbook: [Earn autonomy with evidence](https://about.gitlab.com/ai-native-playbook/topics/working-with-agents/earn-autonomy-with-evidence/).
- **Why primary:** This is the page the dry run cites. It is a vendor playbook, and its load-bearing sentences are citations of source 8, not a separate GitLab measurement. Ranked under that paper for that reason. Still the right page to correct the citation.

**When.** The highest-leverage checkpoint is before the agent builds, on the plan. A later review confirms faithful execution of a plan a person already trusted. Weight matches stakes. A small reversible change does not get the same intent review as a consequential one, because uniform oversight becomes friction people route around. Where agents run inside guardrails, the person does not watch each iteration. They review once the agents have converged.

**By whom.** A person sets boundaries: access, scope, and what an acceptable result is. The verify page adds an independent reviewer: a separate model, or at least a separate session with no access to the author’s reasoning. A reviewer who shares the author’s assumptions “agrees not because the output is correct but because its inputs matched the author’s.” The earn-autonomy page: widen autonomy on measured acceptance, not on hope, and a checkpoint that is never exercised is worse than none, because it manufactures confidence. A named person stays accountable.

**What is read.** Before code: approach, acceptance criteria, edge cases, failure modes. After convergence: residual findings the agents flagged and could not resolve with confidence. The question they want is “does it satisfy the behavior we defined?” rather than “does this code look reasonable?” Depth follows risk: a documentation change and an authorization change do not get the same pass. Objective checks (format, types, dependency policy, and the security and correctness issues a fixed rule can catch) move next to authoring. Changes stay small, and mechanical edits stay split from behavioral ones. The “in practice” multi-agent CI story on the verify page is also footnoted to source 8: parallel specialist reviewers, a coordinator, severity ranking, noisy files filtered out, and an explicit leftover for humans — architectural judgment, cross-system impact, and subtle concurrency.

### 10. SWR-Bench: Assessing LLM Performance in Real-World Code Review Comment Generation

- **Org:** Peking University and Northwestern Polytechnical University. Zhengran Zeng, Ruikai Shi, Keke Han, Yixin Li, Kaicheng Sun, Yidong Wang, Zhuohao Yu, Rui Xie, Wei Ye, Shikun Zhang.
- **Date:** arXiv 1 Sep 2025; proceedings version dated July 2026. Proc. ACM Softw. Eng. 3, FSE, Article FSE137. DOI [10.1145/3808144](https://doi.org/10.1145/3808144).
- **URL:** [ACM PDF](https://dl.acm.org/doi/pdf/10.1145/3808144), [arXiv 2509.01494](https://arxiv.org/abs/2509.01494).
- **Why primary:** Peer-reviewed evaluation of LLM code-review tools on 1,000 manually verified GitHub pull requests (500 that human review actually changed, 500 held out as clean), with full project context. This is the 2024–2026 lab result that bears on “a clean agent review is evidence enough.” It is not a study of production incidents, and the scorer that decides whether a generated comment “hit” a ground-truth change is itself an LLM, which they validated at about 90% agreement with human judges.

**When.** The tools run once, on a finished pull request. The paper’s own remedy, Multi-Review, runs the same reviewer several times on that same change and aggregates. That is a repeated sample of one reviewer, not the specialist lenses in the dry run.

**By whom.** Automated reviewers (LLM-Review, PR-Review, and other automated-code-review tools, including a two-agent tool they call CR-Agent). No human approver is in the loop of the experiment. The ground truth is the change-actions human reviewers historically caused.

**What is read.** The pull request with project context. Tools that only see a fine-grained hunk are what this benchmark was built to replace. PR-Review, their strongest baseline, merges the diffs, drops files when the context window forces a choice, and asks for structured comments covering tests, security, and other improvements. Even that setup does not recover developer intent, code context, and domain knowledge (their Conclusion 6).

**Result that matters for the skip rule.** Best single pass they report: PR-Review with Gemini-2.5-Pro, F1 19.38%. Four of the five tool families sit under 10% precision. They conclude current tools are not ready for real-world review deployment, because a developer would spend the time checking false positives. Functional mistakes are detected better than non-functional ones such as stale docs. Repeating Gemini-2.5-Flash ten times and aggregating raised its F1 by 43.67% relative, to 21.91%, with a larger gain in recall than in precision. The highest F1 they report after aggregation is 23.84% (Gemini-2.5-Pro, five runs). Aggregation helps. It does not make a silent review mean “no defect.”

### 11. The AI-native SDLC playbook

- **Org:** Anthropic, customer-facing playbook.
- **Date:** No date on the page. Fetched 27 Oct 2026.
- **URL:** [The AI-native SDLC playbook](https://claude.com/resources/articles/the-ai-native-sdlc-playbook).
- **Why primary:** Official prescription for customers, distinct from sources 4 and 5, which describe Anthropic’s own repositories. Included because it is the page a team would find under “Claude Code review,” and it does not say what those internal pages say about skipping human approval.

**When.** On the pull request, after identical automated passes on every change. The agent may babysit a pull request it opened until the only remaining wait is code-owner approval. The agent stops at the production gate.

**By whom.** “Findings do not approve or block a PR on their own, and branch protection still requires approval from a code owner.” “The agent that wrote the code has no way to approve it.” Approval is a human, informed by the findings. The code owner “can concentrate on intent and risk because the mechanical evidence is already attached.”

**What is read.** Three passes, written in `REVIEW.md`: bugs (logic, edge cases, regressions), security (injection, authentication gaps, PII in logs), compliance (the change matches `spec.md`, `plan.md`, and design principles). Important means it would break behavior, leak data, or breach a policy. Style and naming are nits, capped (their example caps at five). Generated files and anything CI already enforces are out of scope. Human attention “moves up a level, to whether the change does what the plan intended and whether the risk is acceptable.” The playbook still has that person approving.

## Conflicts

**Must a person read the change?**
Google’s guide and the 2018 study: yes. Someone other than the author examines the code, usually one owner-qualified reviewer, on every change, including a small one. The point is code health and readability for the next human reader, not only defects.
OpenAI’s harness post: no, not on each pull request, once this repository’s loop exists. Humans validate outcomes and step in when judgment is required. Agents merge.
Anthropic’s own Claude Code repos (source 5): not always. Low-tier changes from trusted authors can merge with zero human approvals after automated review and security checks are green. Medium and high still need one or two people. An agent approval never fills a human slot.
Anthropic’s customer playbook (source 11) and the Claude Code product (source 6): the reviewer does not approve. A code owner does. Codex’s product docs say the same: a signal before human merge approval, and rules do not replace required approvals.
Clinton (source 4) is between them: humans reserved for regulated or critical code, plus a sample of the approvals that were automated, plus extra manual review when a cross-actor invariant is in play. “More than half of code is merged by Claude Tag” and “engineers own final approval” are both in that article.

**What “risk-based” means.**
The dry run uses a static list of paths. Source 5 uses a classifier on the change, a second classifier that can override the tier upward, a path list that always needs a person, and a trusted-author list. Source 4 tiers whole codebases and also samples inside the automated tier. Source 8’s stated precondition for leaving the critical path is three things at once: deterministic checks, a risk tier, and an inspectable record, adopted as confidence grows. A path list is one piece of the second condition.

**Does the authoring agent review?**
Google: the author is not the reviewer. OpenAI’s harness: the authoring agent reviews its own changes and must also get other agents to review. Dry run: the implementer self-checks and must not approve. The self-check matches OpenAI. Treating self-review as forbidden would not.

**What is the object of the human read?**
Google: description, design of the main part, then the rest of the assigned lines, and the tests, because tests do not test themselves. UI wants a run; concurrency wants a read.
GitLab / source 8: the plan and the acceptance criteria first; after the loop, the residual findings the agents could not close. Examination of source is still the definition of review.
Claude Code team: the evidence, and a real read of the change whenever the reader cannot say what that evidence would miss. Also anything that sets a pattern others will copy.
OpenAI harness: outcomes and acceptance criteria. Style is explicitly demoted if the next agent can still read the code. That conflicts with Google’s reason for introducing review.
Product reviewers (sources 6 and 7): the diff and surrounding code or `AGENTS.md`, high-severity issues only. Claude’s managed review does not build or run the code. The dry run’s verifier does run the journey. Those are different activities. A green journey does not answer Google’s concurrency question or the “would this test fail?” question. A diff-only model review does not answer the journey question.

**Are multiple reviewers worth it?**
Sadowski 2018: at Google one reviewer was enough, and more reviewers did produce more comments, unlike earlier multi-company studies that flattened after two. That is human reviewers on 24-line changes.
SWR-Bench 2026: repeating one LLM reviewer and aggregating raises recall and still leaves F1 in the low twenties. Specialist two-agent tools were in the set and did not escape the false-positive result.
Anthropic (sources 4 and 5): several narrow agents, then a separate vote or verification step, on the way to a human or a risk decision. They also found unbounded agent-versus-agent rounds re-flag old code, and capped them.
The dry run’s “lenses only after the verifier is clean” is a cost rule. Sources 4 and 5 run the narrow readers on the pull request rather than waiting for one general reader to declare it clean. Skipping lenses when the verifier has already failed is consistent with not spending a wide read on a known-red diff.

**Code health versus defect triage.**
Sadowski: review exists so the next engineer can read the code. Source 8: bug-finding value fades before risk-triage value, and readability is still one of the two reasons to look. OpenAI: legibility for the next agent can replace legibility for a human style preference. If non-risk diffs are never read by a person or by a reviewer aimed at code health, the Google purpose of review is simply unassigned. The dry run’s gardener is the only role left holding it, on a cadence, after merge.

## Corrections to “read evidence unless risk class”

Section 12 says a human reads the spec delta, the oracle diff, paths outside the allowed list, the risk-class hunks, and the verifier’s residual limitations, and otherwise approves on a complete evidence record. G1, G2, the fresh verifier, the auditor, and G3 are never skipped. Lenses are skipped only when verification already failed.

That rule takes the destination of sources 3, 4, 5, and 8 and drops the conditions those sources attach.

1. **The skip is an earned tier, not the default.** OpenAI states the non-generalization caveat in the same post as “humans aren’t required.” Source 8 says the three conditions (deterministic checks, risk tier, inspectable record) have to hold before a human leaves the critical path, and that this is earned as confidence grows. Source 5’s zero-human tier is low-risk changes from trusted authors, after automated review and security checks are green, with revert and incident rates watched, and they say they are not ready to widen it further until those rates can tighten approval automatically. Section 1 of the dry run already says not to start from agents merging themselves. Section 12’s skip sentence does not repeat the condition. A filled evidence file on slice one is the record. It is not yet evidence that the tier has been earned.

2. **A path list is a smaller trigger than the primaries use.** Keep the list. Add the cases they treat as a human read even when the path is “ordinary”:
   - The reader cannot say what the evidence would miss (source 5). A complete record that the steward cannot interpret is a quick approval, which that page rejects.
   - A sample of changes that the tier auto-cleared (source 4). Otherwise the non-risk path is never observed, and source 9 says an unexercised checkpoint manufactures confidence.
   - Cross-actor invariants, as a behavior to review, not only as a filename (source 4). The dry run already puts authorization on the list; the correction is that a passing cross-actor journey still triggers a manual look when it is the invariant, which Clinton describes as an extra review, not as a waived one.
   - A change that sets a pattern later slices will copy (source 5), which is Google’s code-health purpose in agent clothing.
   - The classifier-style override: something about this change demands a person even though its paths are on the light list (source 5’s second classifier). The dry run has no such override.

3. **“Risk-class hunks only” drops the rest of a review Google still defines as the job.** When a person has been asked to approve, the order in source 1 is description, main design, then the rest of the assigned lines, with tests read by a human because the tests will not catch their own weakness. The dry run’s instruction not to start at the top narrating style matches “main part first.” Stopping at the auth hunk does not match “then the rest,” and it assigns the test-validity read entirely to the verifier. Source 10 is why that assignment is weak: the best measured LLM reviewer in that study, aimed at a real pull request, has F1 19.38% and a precision problem. Giving the verifier the spec (source 10’s missing “developer intent”) is the right input. It does not turn a silent verifier into a human read of the tests.

4. **Agent approval is not human approval.** Sources 5, 6, 7, and 11 agree, including the pages that allow some zero-human merges. The dry run already says the implementer does not approve. Keep that. Do not let a clean lens report fill G4. Source 3’s “agents merge their own pull requests” is the same team’s end state, under the caveat in correction 1, and their humans still validate outcomes.

5. **What the human reads, when the tier says read, is the plan and the blind spot, then the diff.** Sources 8 and 9 put the expensive read before code exists: acceptance criteria, edge cases, failure modes. That supports G2 being the costly gate. The after-the-fact read they describe is residual unresolved findings plus a check that the plan was followed, not a tour of style. Source 5 adds the evidence bundle and the question “what would this miss?” Source 1, for any change a person is actually reviewing, still includes the lines and the tests. The dry run’s five-item list is close. The missing item is the blind spot of the evidence, and the missing scope is the rest of a change that a person has been asked to approve.

6. **The authoring agent does review its own diff.** Source 3 requires that local self-review, then other agents. Source 6 will let the authoring session start `/code-review`. The dry run’s self-check is the right slot. The fresh verifier, with the spec and the frozen diff and without the build transcript, matches source 9’s independent session. Keep the fresh session. Do not treat the author’s green self-check as GV.

7. **Product review and the dry run’s verifier read different things.** Claude’s managed reviewer does not run the code (source 6). The dry run’s verifier re-runs the journey. Google wants both, for different bug classes. A rule that skips the human diff because a journey passed will miss races, test tautologies, and design that will not show up as a red step. A rule that skips the journey because a model read the diff will miss the user-visible behavior Google says is hard to see from the lines.

## Rejected pages

- **RayLabs, “Multi-Agent Review Pipeline for AI Coding Agents,” 29 Aug 2026.** Vendor article. The dry run uses it for a read-only fresh pass, an auditor who does not edit, and a measured cost of each reviewer re-reading the diff. The role split is better sourced from Google’s “someone other than the author,” GitLab’s independent session (source 9, citing source 8), and Anthropic’s separate find-and-verify agents (sources 4 and 6). The cost measurement was not in any primary read here. SWR-Bench’s multi-review is repeated samples of one reviewer, aggregated, with F1 still about 24% at best. It is not that RayLabs pipeline.
- **developertoolkit.ai, “Human in the loop: judgment at the gates,” updated 16 Oct 2026.** Secondary commentary. The same claim, with a citation, is source 8 via source 9.
- **Mirrors of the OpenAI post** (advertum.com, and the prepublish.ai transcript of a talk). Use source 3.
- **Anthropic marketing restatements that only repeat source 11.** The playbook itself is kept as source 11 because its code-owner rule conflicts with source 5.
- **“Secure coding with AI — from detection to repair,” Empirical Software Engineering, 13 Mar 2026** (Belozerov, Barclay, Sami). Peer-reviewed, and out of scope: detection and repair of vulnerabilities in DevGPT snippets when a model is asked, not a review of a change. The abstract’s 2025 detection rates (on the order of 75–80% of their confirmed snippet vulnerabilities, with confident wrong answers) are a different experiment from SWR-Bench.
- **“Don’t Judge Code by Its Cover,” arXiv 2505.16222 (May 2025).** LLM-as-judge bias on surface form of code. Not a study of who reviews a change.
- **“Do LLM Evaluators Prefer Themselves for a Reason?,” arXiv 2504.03846.** Self-preference of judges, including on code generation. Not a code-review process study. Noted only because it is the nearest lab result on an author judging its own output; it is not ranked.
- **Llm4sa, ACM TKDD, June 2024.** LLMs triaging static-analysis warnings. Different task.
- **ACL 2025, “Revisit Self-Debugging with Self-Generated Tests.”** Relevant to oracle rot, not to who reads a diff.

## Bottom of the page

Eleven sources. The one to change the dry run against is source 5’s sentence, backed by source 10’s ceiling and source 1’s test rule: a complete evidence record is not a reason to skip the diff unless the reader can say what that record would miss, the miss is acceptable for this tier, and a sample of the skipped diffs is still read.
