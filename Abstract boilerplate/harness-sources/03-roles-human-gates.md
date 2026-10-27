# Roles and human gates — source note

Research date: 27 Oct 2026. Scope: which decisions stay with a person, risk classes, fresh-context review, read-only reviewers, and separation of duties (author, reviewer, auditor). Read against section 5 of `ai-harness-state-of-the-art.md`. That file was not edited.

“Primary” here means the organization that did the work, or the body that publishes the normative text. A recap, a course, or a method site that cites those pages is not primary.

Ranked by how directly the page answers this question. Dates are the dates printed on the page.

---

## Ranked sources

### 1. How the Claude Code team does code review

- **Org:** Anthropic, on the claude.dev engineering blog (“tips, tricks, and POVs from Anthropic’s developers”)
- **Authors:** Addy Osmani, Morgan Westlee Lunt, and Qing Wang
- **Date:** 15 Oct 2026
- **URL:** https://claude.dev/blog/solving-code-review-at-anthropic/
- **Why primary:** First-person practice of the team that builds Claude Code, with their own revert and incident numbers. This is the page developertoolkit.ai cites for “Anthropic’s five human decisions.”

**Claims**

- Human time is the scarce resource. The goal is that most pull requests in the Claude Code repos merge with at most one human, who can be the author, without a rise in reverts or incidents. From June to September, merged pull requests more than doubled and the share that had to be reverted fell by more than 40 percent. “Did a human read the code?” is called an imprecise proxy for “which check is the right one?”
- Before a pull request, Claude is given something it can run, and “a subagent reviews the diff with fresh context.” The post attributes both habits to an internal best-practices guide. This note did not open that guide.
- Every pull request gets a Claude review before a person looks. Several agents look for bugs; a separate group votes before a finding is posted. Their own repos block merge while a bug-review or security-review finding is still open. The author fixes it or resolves the thread with a reason.
- Human approval is a tier set by the change, not by who wrote it. A classifier picks the tier. A second classifier can still demand a person.

  | Tier | Examples on the page | Human approvals |
  | --- | --- | --- |
  | Low | Docs, copy, internal-only features, additive changes behind a flag | None, once automated review and security checks are green |
  | Medium | Ordinary product changes; sensitive areas such as caching, the auto-updater, or system prompts | One person |
  | High | The highest-risk code, plus a small set of paths that always need two people | Two people |

- Paths owners list as sensitive, and any CI workflow file, always need a person. The adoption checklist names auth, billing, CI, release workflows, and installers for `CODEOWNERS`.
- Only authors on a trusted list get the lighter paths. Everyone else needs a second person.
- “An agent’s approval never counts as a person’s.” On an agent-written pull request, “the person directing the agent counts as one of the required reviewers.” An approval covers one commit. A later agent push requires approval again.
- Five responsibilities stay with people: whether to build it (direction, taste, scope); risky code such as billing, the auto-updater, and CI workflows; architecture that sets a pattern others will copy; reading the evidence (test output, the command and what it returned, a screenshot, the reviewer’s findings), and if you cannot say what the checks would miss, ask for a real review; owning the outcome. “A person is accountable for every change that ships, whoever typed it.”
- “You land it, you own it” for a small or medium change that breaks. The conditions they name: small changes, reversibility, blast radius, fast regression detection, and incident review of the system rather than blame.
- They do not yet have circuit breakers that tighten approval when revert or incident rates move. “Engineers here want them before more code moves to zero-human merges.”
- Review rounds are capped. After three rounds the reviewer asks the author to reconsider the approach. One pull request went through more than fifty agent review rounds overnight.

### 2. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI
- **Author:** Ryan Lopopolo, Member of the Technical Staff
- **Date:** 11 Feb 2026
- **URL:** https://openai.com/index/harness-engineering/
- **Why primary:** The team’s own account of a five-month internal product built with no hand-written code. About a million lines, about 1,500 merged pull requests, three engineers then seven.

**Claims**

- “Humans steer. Agents execute.” Humans prioritize work, turn user feedback into acceptance criteria, and validate outcomes. When the agent fails, the question is which capability is missing, not “try harder.”
- “Humans may review pull requests, but aren’t required to. Over time, we’ve pushed almost all review effort towards being handled agent-to-agent.” Codex reviews its own changes, requests more agent reviews, and loops until the agent reviewers are satisfied. Agents “often squash and merge their own pull requests.”
- The end-to-end loop includes “Escalate to a human only when judgment is required” and then “Merge the change.” The post does not define which judgments those are.
- “Humans always remain in the loop, but work at a different layer of abstraction.” That layer is priority, acceptance criteria, and outcome checks, not a required diff read.
- Minimal blocking merge gates. Short-lived pull requests. Flakes are often fixed in a follow-up rather than by blocking. “In a system where agent throughput far exceeds human attention, corrections are cheap, and waiting is expensive. This would be irresponsible in a low-throughput environment.”
- “This behavior depends heavily on the specific structure and tooling of this repository and should not be assumed to generalize without similar investment—at least, not yet.”
- The post does not list auth, money, migrations, CI, or tests as human-read classes. It does not say a second person must approve. It does not say the agent is forbidden to merge.

### 3. OWASP AISVS 1.0, Appendix C — AI-assisted secure coding

- **Org:** OWASP Foundation
- **Date:** June 2026 (version 1.0). The project page says 1.0 was released 24 Jun 2026. Markdown on the default branch on 27 Oct 2026 still states AC.4.1 and AC.8.1 as below. The repo README also points at a 1.01 PDF; this note checked the 1.0 PDF and the 1.0 markdown, not a line-by-line diff of 1.01.
- **URLs:**
  - PDF: https://raw.githubusercontent.com/OWASP/AISVS/main/1.0/dist/AISVS-1.0.pdf
  - Markdown: https://github.com/OWASP/AISVS/blob/main/1.0/en/0x92-Appendix-C_AI_for_Code_Generation.md
  - Project: https://owasp.org/www-project-artificial-intelligence-security-verification-standard-aisvs-docs/
- **Why primary:** A published verification standard whose appendix is specifically about AI coding tools. Requirements are written as “verify that,” at levels. Level 1 is the baseline of the standard, not an optional extra. It is a compliance target, not a statute, and not a claim that any lab measured these controls.

**Claims**

- **AC.4.1 (Level 1).** AI-generated code always goes through code review by a qualified human engineer. The reviewer must not be the same identity that asked for the generation. The AI agent does not count as that human.
- **AC.4.4 (Level 2).** Security-critical files need a higher bar when AI generated or modified them: two-person review, security-team sign-off, or stricter. The list is authentication, authorization, and cryptography; IAM policy; CI/CD workflow definitions; deployment manifests; sandbox or network policy.
- **AC.7.2 (Level 2).** AI-generated infrastructure and pipeline configuration needs human review and approval before it runs outside a hermetic sandbox.
- **AC.7.4 (Level 2).** High-impact pipeline triggers need dual control and a security-team review no matter who produced the change (including `pull_request_target`, `workflow_run`, self-hosted runner labels, workflow `permissions`, OIDC trust, secret-to-environment mappings).
- **AC.8.1 (Level 1).** Autonomous agents cannot approve, merge, sign, or deploy artifacts they themselves generated. The source-control system, the CI system, and the artifact registry enforce it. Policy alone does not satisfy the control.
- **AC.8.4 (Level 3 in the markdown).** Generation, review, approval, and deployment are distinct principals, human or system.
- The appendix maps AC.4.1 to NIST SSDF PW.7, OWASP ASVS v5 V10, and ISO/IEC 27001:2022 A.5.3, and maps AC.8.1 to NIST SP 800-53r5 AC-5. Those mappings are the standard’s. PW.7 itself does not say what AC.4.1 says. See source 6.

Money, schema migrations, and ordinary test edits are not in the AC.4.4 list. Tests are not given a separate human-read class. The Level 1 rule is broader than a risk list: a different human reviews the code every time.

### 4. GitLab AI-native playbook — judgment, checkpoints, autonomy

- **Org:** GitLab
- **Date:** The three pages carry no publication date. Each one footnotes *Enterprise Technology Leadership Journal* 3, no. 2 (Fall 2026). Those journal articles were not read. They are sold as PDFs on itrevolution.com.
- **URLs:**
  - https://about.gitlab.com/ai-native-playbook/topics/working-with-agents/decide-where-human-judgment-stays/
  - https://about.gitlab.com/ai-native-playbook/topics/working-with-agents/build-human-checkpoints-into-the-workflow/
  - https://about.gitlab.com/ai-native-playbook/topics/working-with-agents/earn-autonomy-with-evidence/
- **Why primary:** GitLab’s own playbook, written as practice for engineering teams. It is primary for what GitLab tells readers to do. The sentences that carry the argument are marked as citations of the Fall 2026 journal, so it is secondary for the underlying studies.

**Claims**

- Sort steps by stakes and reversibility. A person keeps decisions that are hard to reverse, high-consequence, or dependent on context and values. An agent runs work that is bounded, checkable, and cheap to undo.
- One named person stays accountable. An agent has capability and does not have accountability. Decisions that affect people directly (the examples are hiring, performance, and access) stay with a person.
- The highest-leverage checkpoint is before the agent builds, on the plan: approach, scope, assumptions, acceptance criteria, edge cases, failure modes. Review after the build confirms that a trusted plan was carried out. It is a poor moment to reverse a bad plan.
- Match checkpoint weight to risk. Uniform oversight becomes friction people route around. On a low-risk reversible change the person may not watch the loop, and may look only at residual findings the agents could not resolve.
- Decide in advance who is notified when a boundary fails, how access pauses, and how the boundary is tightened.
- Autonomy widens in stages, each stage earned against an explicit success threshold. A checkpoint nobody exercises is worse than none, because it manufactures confidence. A rising rate of the agent stopping to ask is treated as healthy.

The playbook does not name four gates called intent, spec, evidence, and risk class. It does not list authentication, money, schema, migrations, or tests as the human-read set.

### 5. SLSA v1.2 source track — two-party review

- **Org:** SLSA community (OpenSSF-related specification)
- **Date:** v1.2 announced 24 Nov 2025. https://slsa.dev/blog/2025/11/announce-slsa-v1.2
- **URL:** https://slsa.dev/spec/v1.2/source-requirements
- **Why primary:** The normative source-integrity spec. Source Level 4 is the public “two trusted persons” rule. It is not an AI-coding standard. It becomes relevant when someone cites SLSA as the separation-of-duties requirement for agent pull requests.

**Claims**

- Source Level 4: changes to protected branches must be agreed by two or more trusted persons before submission. Acceptable pairs are a trusted uploader plus a different trusted reviewer, or two different trusted reviewers. The uploader can be one of the two. A third person (an auditor who does not edit and does not approve) is not required.
- The reviewed revision is the final revision. Further edits during review must be reviewed too. The platform must show a human-readable diff. Reviews should cover at least security-relevant properties. “Should” is not “must.”
- A trusted robot may be granted a perpetual exception, for example Dependabot or an import bot, so that it can merge a change that has not had two-party review. The robot is not defined as one of the two trusted persons. The exception is a waiver of the review, not a way to count the agent as a reviewer.
- Levels 1–3 do not require two-party review. Two-person review is the top source level, for organizations that want to resist unilateral change.

### 6. NIST SP 800-218, SSDF v1.1 — PW.7

- **Org:** NIST
- **Date:** February 2022
- **URL:** https://doi.org/10.6028/NIST.SP.800-218
- **Why primary:** The federal secure-development framework that AISVS cites for human review of code. The citation is easy to over-read. The primary sentence is a decision, not a mandate that every AI diff be read by a second person.

**Claims**

- **PW.7** is “Review and/or Analyze Human-Readable Code to Identify Vulnerabilities and Verify Compliance with Security Requirements.”
- **PW.7.1:** determine whether code review (a person looks at the code) and/or code analysis (tools, alone or with a person) should be used, as the organization defines.
- **PW.7.2:** perform that review and/or analysis, and record the issues. One example is a person reviewing what a static-analysis tool reported.
- The practice does not mention generative coding agents, does not require the reader to be someone other than the author, and does not require a human look at every change. An organization can satisfy the letter of PW.7.1 by deciding that tools analyze a class of change and a person reviews only the tool’s findings.

NIST SP 800-218A (the generative-AI community profile) is about developing foundation models, not about application code written by a coding agent. It is not a source for this question. See rejected pages.

### 7. Building multi-agent systems: when and how to use them

- **Org:** Anthropic
- **Author:** Cara Phillips, with Paul Chen, Andy Schumeister, Brad Abrams, and Theo Chu
- **Date:** 23 Jan 2026
- **URL:** https://claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them
- **Why primary:** Anthropic’s product-engineering guidance on one agent versus specialists, after the November 2025 harness post left the question open.

**Claims**

- Start with one well-tooled agent. Teams at Anthropic have spent months on multi-agent setups and then matched the result by prompting a single agent better.
- They report having built separate agents for planning, execution, review, and iteration, and finding lost context at each handoff, with more tokens spent coordinating than doing the work. In their tests, multi-agent runs used about 3 to 10 times the tokens of a single agent on equivalent tasks.
- Three cases where several agents beat one: context pollution, work that can run in parallel, and specialization that improves tool choice or focus. Outside those, coordination cost exceeds the benefit.
- A subagent’s value for review is a clean context, not a proof that a specialist is a better judge. “Decomposition follows context, not problem type.”
- The article is not a study of code-review accuracy. The 90.2 percent figure that appears in some Anthropic PDFs is a different document (research-style multi-agent search), and this blog does not use it.

### 8. Effective harnesses for long-running agents

- **Org:** Anthropic Engineering
- **Date:** 26 Nov 2025
- **URL:** https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- **Why primary:** The harness post the dry run already uses for session shape. Quoted here only for roles and the open question.

**Claims**

- Two prompts, not a review board: an initializer, then a coding agent. Each session starts with no memory of the previous conversation. State moves through files (`claude-progress.txt`, a feature list, git).
- Future work, in the post’s words: “it’s still unclear whether a single, general-purpose coding agent performs best across contexts, or if better performance can be achieved through a multi-agent architecture.” The examples they float are a testing agent, a QA agent, or a cleanup agent.
- The post does not say a human must approve each feature, and it does not evaluate author-versus-reviewer as an assurance control. The human in that write-up supplies the original prompt and can watch the run. The agent is told to leave the tree in a mergeable state.

### 9. Harness design for long-running application development

- **Org:** Anthropic Engineering
- **Date:** 24 Mar 2026
- **URL:** https://www.anthropic.com/engineering/harness-design-long-running-apps
- **Why primary:** A later Anthropic engineering post that actually built the specialist split the November post left open. It is one researcher’s prototype (“getting it to build complete applications without human intervention”), not a team merge policy.

**Claims**

- Three personas: a planner that expands a one-to-four-sentence prompt into a product spec, a generator that implements one feature at a time, and an evaluator that drives the app and grades the sprint. The planner is told to stay at product and high-level design, because a wrong low-level spec would cascade.
- Before each sprint the generator and the evaluator agree a sprint contract: what “done” means, before code. They communicate through files.
- The evaluator is a separate context that clicks through the running application. It is not a read-only diff reviewer, and it is not a human.
- This experiment removes the human spec gate the dry run calls G2. The human’s input is the short prompt. The planner writes the spec.

### 10. Claude Code product docs — plan mode, subagents, Code Review

- **Org:** Anthropic
- **Date:** Living docs, fetched 27 Oct 2026
- **URLs:**
  - Plan / permissions: https://code.claude.com/docs/en/agent-sdk/permissions
  - Subagents: https://code.claude.com/docs/en/sub-agents
  - Code Review: https://code.claude.com/docs/en/code-review
- **Why primary:** The control surface of the tool. These pages describe what the product enforces. They are not a finding about which merge decisions are safe.

**Claims**

- Plan mode: Claude explores and writes a plan without editing source. File edits are never auto-approved in plan mode, even if an allow rule matches or a `PreToolUse` hook returns allow. A subagent of a plan-mode session stays in plan mode (agent-team teammates are the documented exception).
- A non-fork subagent “starts with a fresh, isolated context window” and does not see the parent conversation, the skills already invoked, or the files already read. The parent composes the delegation message, so whatever the parent puts in that message is visible. A fork inherits the parent conversation, which is the opposite of withholding the build transcript.
- Docs include a “read-only” code-reviewer example that denies Edit and Write. That example still grants Bash. The stronger read-only pattern in the same docs is an agent whose tool list is only read-only tools, or a `PreToolUse` hook that rejects mutating commands. Denying Edit and Write does not by itself stop a shell from writing.
- Managed Code Review: several agents read the diff and surrounding code, then a verification step filters findings. Repository access during a review is read-only. The review does not push commits, and it does not build or run the code. “A review never approves or blocks the pull request.” The check run does not conclude as a failure. `REVIEW.md` is taken from the base branch, not from the pull request’s own edits.
- That non-blocking product statement and the 15 Oct team post are both Anthropic’s. The team post says their repos do not merge while a review finding is open. The product will not enforce that. The repository’s own rules have to.

### 11. Cursor docs — Plan Mode, subagents, hooks

- **Org:** Cursor
- **Date:** Living docs, fetched 27 Oct 2026
- **URLs:**
  - https://cursor.com/docs/agent/plan-mode
  - https://cursor.com/docs/subagents
  - https://cursor.com/docs/hooks
  - Run modes (action approval, not merge approval): https://cursor.com/docs/agent/security/run-modes
- **Why primary:** The control surface. Plan Mode is a workflow. `readonly: true` is the actual write boundary.

**Claims**

- Plan Mode researches, asks questions, and writes a plan the person can edit, then the person clicks to build. The page recommends it for architectural decisions and for work with several valid approaches. It does not say the mode refuses a write the way Claude plan mode does.
- “Subagents start with a clean context.” They do not see prior conversation history. The parent puts what it thinks is relevant into the prompt. Clean context is not the same as “the build transcript was withheld.”
- `readonly: true` means no file edits and no state-changing shell commands. The docs’ security-auditor example uses that flag. A reviewer that adds a failing test cannot run under it.
- `subagentStart` hooks can allow or deny spawning a subagent. They do not review the diff.
- Auto-review run mode is an action classifier for shell, MCP, and fetch. The same page says the classifier is not a security boundary. It is not a merge gate and it is not a second person.

### 12. Codex docs — sandbox, subagents, pull-request review

- **Org:** OpenAI
- **Date:** Living docs, fetched 27 Oct 2026
- **URLs:**
  - https://developers.openai.com/codex/subagents
  - https://developers.openai.com/codex/sandboxing
  - https://developers.openai.com/codex/use-cases/github-code-reviews/
- **Why primary:** The control surface of the other agent the dry run is written against. The review use-case page and the February harness post do not say the same thing about human merge.

**Claims**

- Sandbox mode is what the process can touch. Approval policy is when it must ask. Default local posture is workspace write and no network. `read-only` is a sandbox: the agent can read, and edits, commands, or network need an approval or are impossible when approval is `never`.
- Subagents inherit the parent turn’s sandbox and approval mode. A custom agent can override that and be marked read-only.
- The GitHub review use case is framed as catching regressions “before human review,” for teams that want another signal “before human merge approval.” The person is still the assumed approver on that page. The February harness post is one internal product where that assumption was dropped. Codex’s public tutorial did not drop it.

---

## Conflicts

Three positions are in print. They are not the same policy wearing different words.

| Question | Claude Code team, 15 Oct 2026 | OpenAI harness post, 11 Feb 2026 | OWASP AISVS 1.0 App. C |
| --- | --- | --- | --- |
| Must a person approve the merge? | Not on the low tier, once automated review and security checks are green. Medium: one person. High: two. | No. Humans may review and are not required to. Agents often merge their own pull requests. | Yes for the code review (AC.4.1). The agent also cannot approve, merge, sign, or deploy its own artifact (AC.8.1), enforced in the forge. |
| Can the person who asked for the generation be the reviewer? | Yes. That person counts as a required reviewer, as if they had typed the change. | The post does not require a reviewer at all. | No. The reviewer is a different identity. The agent does not count. |
| What is the risk list? | Classifier plus path overrides. Named examples: billing, auto-updater, CI workflows, auth, release workflows, installers, caching, system prompts. Low-risk can be zero humans. | Unspecified. “When judgment is required.” | Level 1: all AI-generated code, by a different human. Level 2 elevation: auth, authz, cryptography, IAM, CI/CD, deployment manifests, sandbox and network policy. Not “money” or “migrations” as those words. |
| Does agent review count as the human? | Never. | Agent-to-agent review is the review they actually rely on. | Never. |
| Where does the post say this stops generalizing? | They want circuit breakers before more zero-human merges. Revert and incident rates are the guardrail. | Explicitly: low-throughput environments, and any repo that has not had the same investment. | Not an empirical claim. It is the Level 1 bar if you assess against AISVS. |

GitLab sits nearer the Claude Code team than AISVS, and it is less specific than either. The heavy human moment is the plan. Later review is light when the work is reversible. Autonomy is widened only against a measured threshold. A named person stays accountable even when they are no longer reading every step. GitLab does not say the low-risk path may have zero human approvals, and it does not say a second human must read every diff.

SLSA Source Level 4 is a fourth rule, and only if you claim that level. Two trusted persons. The author/uploader may be one of them, which AC.4.1 does not allow for the requester. A robot exception waives the review. It does not turn the agent into a trusted person. NIST PW.7 is weaker than all of the above: the organization chooses review, analysis, or both.

Inside Anthropic, the 15 Oct post and the Code Review docs disagree on blocking. The team’s repositories do not merge with an open finding. The product review never approves or blocks, and its check does not fail. Copying the product defaults does not reproduce the team’s gate.

Inside OpenAI, the harness post and the Codex review tutorial disagree. The tutorial still assumes a human merge approval. The harness post is one internal system that stopped requiring it, and says not to copy that until the harness exists.

A later interview is not the blog. On Latent Space, Lopopolo says most human review is post-merge, and that a human still cuts the release branch and approves a smoke test before distribution, because that product is a native app rather than continuous deploy. Those sentences are not in the 11 Feb post. The episode title’s “0% human review” is the show’s wording. See rejected pages.

On specialists versus one agent, Anthropic’s own pages are sequential, not one verdict:

- 26 Nov 2025: still unclear; specialists might do better at test, QA, and cleanup.
- 23 Jan 2026: start with one agent; plan/execute/review/iterate splits often lost context and cost several times the tokens; use several agents for context isolation, parallel work, or tool focus.
- 24 Mar 2026: a research harness did use planner, generator, and evaluator, and let the planner write the spec, in a run aimed at building without a person in the loop.
- 15 Oct 2026: the production review system is several agents over one frozen change (find, then a separate vote), plus a fresh-context subagent before the pull request. That is context isolation and parallel reading, which the January post allows. It is not a claim that five role-agents beat one coder.

Fresh context is real in the 15 Oct post, in Claude Code’s non-fork subagents, and in Cursor’s subagents. It is not a platform guarantee that the build transcript was withheld. The parent writes the handoff. A Claude Code fork inherits the parent session on purpose.

Read-only is also real, and it is narrower than “a verifier.” Managed Code Review does not edit and does not run the code. Cursor `readonly: true` blocks file edits and state-changing shell. Codex read-only sandbox blocks edits unless a person approves. A role that adds a failing test, or that re-runs the journey, needs a write or an exec permission those modes exist to remove. The lens and the auditor match the documented read-only modes. A verifier that adds cases does not.

---

## Corrections to the dry run

Section 2 says this is taken from published practice, and cites GitLab and developertoolkit.ai together:

> The highest-leverage human checkpoint is before code exists: intent and the definition of done. Afterward, a person reads evidence. A person reads the diff itself for a short list of risk classes.

Split that sentence.

- The first clause matches the GitLab checkpoint page: the plan, before code, is where a person’s judgment has the most leverage. GitLab’s page footnotes that claim to Davis, Gill, Hendrickson, Jones, Ma, Shoup, and Wickett, *Agentic AI and Code Reviews*, ETLJ Fall 2026. This note did not read the paper.
- “A person reads evidence” and “a short list of risk classes,” and the four named gates in section 5 (intent, spec, evidence, risk route), match developertoolkit.ai’s page “Human in the loop: judgment at the gates,” updated 16 Oct 2026. See the demoted section below. GitLab never uses that four-row table. OpenAI never uses it. The Claude Code team’s five responsibilities are close, and they are not the same: architecture is its own human decision, the prompter may be the only human, and the low tier can be zero human approvals.
- The dry run’s risk classes (authentication and authorization; value, quotas, or irreversible user data; stored shape and migrations; oracles, linters, or the pipeline; a slice no check covers; production promotion) are the method page’s escalation list, plus promotion. Anthropic’s named set is billing, auto-updater, CI, auth, release workflows, installers, and a classifier. AISVS Level 2 elevation is auth, authz, cryptography, IAM, CI/CD, deployment manifests, and sandbox or network policy. Schema and migrations are the method site’s addition. Do not attribute that list to GitLab, OpenAI, NIST, or SLSA.

Section 5’s rule that only the steward’s four gates block the pipeline, and section 12’s “never skip G3,” are stricter than both the Claude Code team and the OpenAI harness post, and looser than AISVS AC.4.1.

- Stricter than Anthropic’s low tier and stricter than OpenAI, because every slice still needs a human evidence approval.
- Looser than AISVS, because a non-risk slice can merge on evidence with no second person reading the code, and the steward who asked for the slice can be the one who approves it. AC.4.1 requires a qualified human who is not that identity, and a code review rather than an evidence packet. AC.8.1 also forbids the generating agent from merging its own change, in the forge, not only by prompt.

Section 1 and the inferred bullets in section 2 already say to start strict and not to copy OpenAI’s self-merge until invariants exist and escapes are measured. That inference agrees with the OpenAI post’s own limit (“irresponsible in a low-throughput environment,” “should not be assumed to generalize”) and with the Claude Code team’s wish for circuit breakers before more zero-human merges. Keep it labeled as a choice. It is not what either team does on the systems they describe as already mature.

“Author ≠ reviewer ≠ auditor” is the dry run’s role split. The published rules are:

- AISVS Level 1: requester ≠ human reviewer, and agent ≠ reviewer; agent ≠ approver/merger/deployer.
- AISVS Level 3 (AC.8.4): generation, review, approval, and deployment are distinct principals.
- SLSA Source Level 4: two trusted persons; the uploader may be one; no required third auditor; a robot exception waives review rather than supplying a reviewer.
- Claude Code team: agent approval never counts; the prompter does count; low risk may have no person; high risk has two people.
- NIST PW.7: the organization decides whether a person looks.

A three-role agent split (implementer, verifier, auditor) plus one human steward is not the same control as any of those.

Section 12 calls the verifier’s pass “the frozen diff, read-only.” Section 5 and prompt P4 tell the verifier to add failing cases and to run the journey. Those cannot both be the documented read-only mode. Adding a case is a write. Running the app is not what Anthropic’s managed Code Review does; that product reads the diff and does not build or run. The 15 Oct post uses both patterns as different layers: an executable check before the pull request, and a fresh-context subagent that reviews the diff. The dry run’s verifier is the first layer. The lenses are the second.

“Same model, different session, no build transcript” is stronger than the docs. Fresh context is supported for a new session (Anthropic, Nov 2025) and for a non-fork subagent (Claude Code and Cursor). The parent chooses the handoff text. A fork is designed to inherit the transcript. Nothing in the product docs strips the build log unless the prompt withholds it.

Section 19 says Anthropic leaves open whether one general agent or several specialists is better, and that the dry run separates roles because self-review is a weak assurance boundary. The November post does leave it open. By January 2026 Anthropic’s published advice is to start with one agent, and they report that a plan/execute/review/iterate split often lost context. By March 2026 a research harness used three personas anyway, including an evaluator, and removed the human from spec writing. By October 2026 the Claude Code team uses several readers over one change for finding bugs, and still keeps a person on the five decisions above. Separating roles to avoid self-approval matches AISVS and matches “an agent’s approval never counts.” It does not follow from a finding that specialists beat one agent. The January post says the opposite for the general case.

Production promotion as a human button is the dry run’s rule. GitLab supports it only at the level of “hard to reverse, so a person decides.” The OpenAI blog does not say who flips production. The Lopopolo interview says a human cuts the release branch and approves a smoke test for that native app. The Claude Code team ships behind flags, to employees first, then a `latest` channel, then `stable`, and the person who lands the change owns the break. A universal human promote is a reasonable start-strict rule. It is not the published end state of either lab.

---

## Demoted: developertoolkit.ai

Not ranked. The dry run cites it as if it were practice alongside GitLab. It is a method site by Michał Jaskólski (AI Developer Toolkit / Wondel.ai), with a paid toolkit behind parts of the site.

- **Page:** Human in the loop: judgment at the gates, not in every diff
- **URL:** https://developertoolkit.ai/en/method/principles/human-in-the-loop/
- **Dates on the page:** structured data says published 8 Feb 2026 and modified 16 Oct 2026. The visible label is “Updated 2026-10-16.” This note did not recover the February text, so it does not claim the four gates were unchanged before the Anthropic post.

**What the page asserts, in its own voice**

- Four human decisions: intent, spec, evidence, risk class. The table, the time budgets, and the failure column are the ones section 5 of the dry run reproduces. The page says the time budgets are starting points, not research findings.
- Escalation classes, where a named person reads the code every time: authentication and authorization, money, schema, data migrations, changes to the oracle (tests, CI, lint, type config), and anything no check covers. Elsewhere, approve on evidence. Low-risk classes ship behind progressive delivery with automatic rollback.
- Spend the most time on the spec. A wrong assumption caught there is a sentence.

**Who it cites**

- The Claude Code team post of 15 Oct 2026, for the five human decisions, “you land it, you own it,” the pull-request template fields, and raising a direction before the pull request is finished. The page then maps those five onto its four gates. The mapping is the author’s. The Anthropic post does not use the four gate names, and its low tier has no required human approval.
- Claude Code, Codex, and Cursor docs for the controls that hold a gate (plan mode, hooks, `CODEOWNERS`, `codex review`, Cursor plan mode). It says Cursor features were checked on cursor.com on 28 Aug 2026, and Codex permission profiles on codex-cli 0.157.1. Those pins will have moved. The product pages above were re-read on 27 Oct 2026.
- It does not cite the OpenAI harness post, the GitLab playbook, NIST, SLSA, or AISVS.

Use the page as a concise operating recipe that was fitted to the 15 Oct Anthropic article the next day. Cite Anthropic, not this page, for what that team does. Cite this page for the four-gate vocabulary the dry run adopted.

---

## Rejected pages

| Page | Why it was not used as a source |
| --- | --- |
| Latent Space, “Extreme Harness Engineering… 0% human code, 0% human review,” transcript of Lopopolo | Secondary interview. The title overclaims the 11 Feb blog. The transcript does add a human release branch and a human-approved smoke test. Those lines were not promoted into the ranked claims. https://www.latent.space/p/harness-eng |
| BidClub write-up of the same episode | Same interview, further removed. https://bidclub.ai/e/extreme-harness-engineering-token-billionaires-1 |
| Agientry, Stackness, CodeBridge, AI Insiders, daily.dev, Thread Navigator | Summaries of the 15 Oct Claude Code post. The post itself was read. |
| `archive228/loopkit` `docs/effective-harnesses-v03.md` | Unofficial reconstruction. It invents an internal debate and names people. Not Anthropic. |
| HeyTrove “Anthropic Engineering Playbook” marketplace page | Unofficial packaging of the engineering posts. |
| NIST SP 800-218A | Community profile for developing generative AI and dual-use foundation models, including provenance of model releases. It is the wrong document for “who reviews application code an agent wrote.” |
| NIST SP 800-204D | CI/CD supply-chain practices. AISVS cites it for pipeline scanning. It does not set the human role on AI-authored changes. |
| GitLab issue 487382, Duo Workflow human-in-the-loop | Product design for pausing a workflow so a person can steer. Not a code-review or separation-of-duties policy. |
| ETLJ Fall 2026 papers GitLab footnotes (Davis et al. on code review; Eslick et al. on sociotechnical architecture; Cockcroft et al. on agents deciding) | Paywalled PDFs. Not read. GitLab’s pages are cited for GitLab’s wording only. https://itrevolution.com/product/agentic-ai-and-code-reviews/ |
| O’Reilly Radar, “Everyone’s an Engineer Now” | Second-hand account of a different review culture. Not the Claude Code team post. |
| Pragmatic Engineer, “How Codex is built” | Second-hand. A tiered human review of the Codex product (non-critical may merge after AI review; core agent and open-source components stay human) is interesting and was not verified from OpenAI. |
| Claude.com article “Running an AI-native engineering org” | Search snippets overlap the 15 Oct post (humans on legal, trust boundaries, security, product taste). Not fetched in full, so not ranked. |
| developertoolkit.ai related pages (“the human’s job,” “reading evidence instead of code,” the autonomy ladder) | Same method site. Not independently sourced. |
| RayLabs, QualityLayer, Wavect, Quash, PlanVault | Cited by the dry run for review and test shape. Out of scope for this note. Not re-verified here. |

---

## What would change the ranking

- Reading the Fall 2026 ETLJ paper GitLab actually cites, if a copy is obtained. Until then the playbook is “what GitLab says,” not “what that study found.”
- A line-by-line check of AISVS 1.01 against the 1.0 PDF for AC.4 and AC.8.
- The Claude Code best-practices guide named, and not linked, in the 15 Oct post.
