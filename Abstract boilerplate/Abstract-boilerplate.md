# AI agent harness — Abstract boilerplate : a digest of the current state of the art

What published practice still says about an agent harness when no language, framework, or product domain is assumed. A project adapts it by filling a constitution. The prompts, artifacts, and gates below are that digest.

This file is outer policy for a coding agent that already has a loop. Each prompt in [§17](#17-prompt-contracts) is one job. A later prompt may read an earlier artifact. It must not redo that job.

Detailed notes for the claims in this file live in `harness-sources/` (01 through 16).

---

## 1. What this is, and what it is not

A harness is the runtime around the model, and this digest is the outer policy that runtime still has to execute. It keeps a human on intent, spec, and evidence. The constitution chooses the complexity score and whether a person reads the diff. The builder does not merge its own work.

A **harness** is the runtime around the model: the loop, the tools, the rules for what enters each context window, the safety controls, and the stop rules. LangChain’s anatomy (Vivek Trivedy, 10 March 2026) draws that boundary: if it is not the model, it is the harness. Prompt writing and context curation sit inside that runtime. Anthropic’s context-engineering post (29 September 2025) treats context engineering as the craft that runs at every model call, covering system instructions, tools, and history. Böckeler (2 April 2026, on martinfowler.com) calls the outer harness a user builds a form of context engineering: guides before the agent acts, sensors after. The coding product already supplies the inner loop.

This file is that outer policy: roles, artifacts, gates, and stop rules. A runtime has to execute it before it is a harness.

The three-way split of prompt, context, and harness into one turn, one session, and the whole build is Augment’s framing (16 April 2026, updated 29 June 2026). OpenAI’s harness post (Lopopolo, 11 February 2026) does not draw it. That post uses “harness engineering” for the human practice of environments, intent, and feedback loops.

**The sources do not share one end state.** OpenAI reports an internal product with no hand-written code, on the order of a million lines and about 1,500 merged pull requests, with agent-to-agent review and few blocking merge gates. Those counts are one team’s anecdote: three engineers, later seven, over about five months, stated average 3.5 pull requests per engineer per day. They also say that loop depends on repository structure and tooling that took months to build, and should not be assumed to generalize yet. Anthropic’s long-running-agent post (26 November 2025) leaves open whether one general agent or several specialists is better. This digest separates roles and fresh contexts, and keeps a human on intent, spec, and evidence. Whether that person also reads the diff, and which complexity score blocks, are constitution choices in [§5](#5-roles). It does not require many models, and it does not treat an agent’s approval as a person’s.

---

## 2. Claims: sourced vs inferred

Published claims stay in one list, with the page named on each item. Where those pages leave the rule unsettled, this digest records the choice and does not close it.

Detailed notes, conflicts, and pages that were checked and not used are in `harness-sources/`.

### Taken from published practice

Each item is a claim a named page makes, kept close to what that page says.

- Humans steer; agents execute. When the agent fails, the question is which capability is missing and how to make it legible and enforceable. “Try harder” was almost never the fix. (OpenAI, 11 February 2026)
- Repository knowledge is the system of record. A map of about 100 lines points at a versioned `docs/` tree. A single giant instruction file crowds the task, rots, and cannot be checked. Linters and CI check freshness and cross-links. (OpenAI)
- Boundaries and a small set of taste rules are mechanical checks. The error text tells the agent how to comply. Local implementation inside those boundaries stays free. (OpenAI)
- The agent can boot an isolated copy and query that copy’s logs, metrics, and traces. “Startup under 800 ms” and “no span over two seconds” are example prompts from that one product, to show that a number becomes checkable once the trace is legible. The post does not report that either number was met. They are not percentiles and not service-level objectives. (OpenAI)
- Agents copy existing patterns, including bad ones. OpenAI encoded golden principles and ran a recurring cleanup. Most of those small pull requests were reviewed in under a minute and automerged. (OpenAI)
- One feature per fresh session. A JSON feature list starts failing, because the model rewrote Markdown more readily. The coding agent is told to change only the pass field, and only after it has driven the user interface. That same agent sets the flag. Unit tests and `curl` left features broken until that drive. Each later session reads the progress log and git history, boots, and re-checks a core journey before new work, then commits. (Anthropic, 26 November 2025)
- The heavy human checkpoint GitLab publishes is the plan, before code.
- OpenAI does not require a human pull-request review, and agents often squash-merge. The Claude Code team (15 October 2026) lets low-risk changes merge with no extra human approval, counts the person who prompted the agent as the reviewer, and says an agent’s approval never counts. OWASP AISVS 1.0 Appendix C (June 2026) requires a different human to review every AI-generated change, and forbids the agent approving or merging its own work.
- A walking skeleton is a tiny end-to-end function that links the main parts and stays (Cockburn). An architectural fitness function is an objective check that a chosen characteristic still holds (Neal Ford, Rebecca Parsons, and Patrick Kua).
- GitHub Spec Kit’s task template blocks every user story on a horizontal foundation, then orders each story models, then services, then endpoints.
- Someone other than the author reads the change, including the tests, because tests do not test themselves. (Google’s review standard)
- On SWR-Bench, the best single LLM review pass reported is F1 19.38% (PR-Review with Gemini-2.5-Pro). The authors conclude that a developer would spend the time checking false positives.
- Tests written after faulty code, by a model that can see that code, detected the fault about 14% of the time. Tests from the task text alone, without the code, detected it 25% of the time. (Konstantinou, Tambon, and Papadakis, arXiv:2607.05139, July 2026)
- Prompts do not stop test edits. With a strict prompt, GPT-5 still passed 54% of ImpossibleBench’s conflicting SWE-bench tasks by cheating. Read-only tests stop edits and do not stop special-casing. (arXiv:2510.20270)
- Given fixed code, generated tests produced at least one correct strong test for the bug scenario 63% of the time. Given the buggy code, that fell to 30%, and including the JavaDoc did not change the drop. (ASE 2026, DOI 10.1145/3832783.3844564)
- “Corrections are cheap, and waiting is expensive” is OpenAI’s policy for human waiting, and for indefinite flake stalls, in a repository that already has mechanical taste checks and high agent throughput. The next sentence says that policy would be irresponsible at low throughput. The post does not say a red check may land.
- DORA, survey published 22 October 2024: an estimated 7.2% reduction in delivery stability and 1.5% reduction in throughput per 25% higher AI adoption, nearly 3,000 respondents. Report of 23 September 2025: the throughput association turns positive; instability stays unfavorable; no new 7.2% figure. Faster repair after production does not cancel that harm.
- SWE-bench “resolved” means the patch passes `FAIL_TO_PASS` and `PASS_TO_PASS`. Maintainer acceptance is not part of the score. METR, 10 March 2026: automated SWE-bench scores sit 24.2 percentage points above maintainer merge, on the repositories in that note.
- GitClear’s two-week line churn, the share of changed lines reverted or substantially revised within two weeks: 3.05% in 2020, 5.67% in 2024. The lines are not labeled by who wrote them. DORA’s deployment rework rate is a different metric: the share of deployments that were unplanned production fixes.
- A repeated mistake becomes a line in the instruction file or a tool the agent can run. (Hashimoto, 5 February 2026)
- The spec names each test seam before publication: the public boundary, what it catches, and what it misses. The steward accepts the list. (Matt Pocock, 27 April 2026, <https://www.aihero.dev/skills-to-spec>)
- One failing test at an agreed seam uses the frozen example as the expected value. An assertion that recomputes the expected value the way the code does does not count. Refactoring sits outside that cycle. (Matt Pocock, `tdd` skill, read 27 October 2026, <https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md>)
- A feature slice names its blockers and its cut through every layer the change touches. A one-layer feature slice fails the plan gate. A wide mechanical change is expand, then migrate, then contract. (Matt Pocock, 27 April 2026, <https://github.com/mattpocock/skills/blob/main/skills/engineering/to-tickets/SKILL.md>)
- After the spec is accepted, the implementer does not propose a different approach. Parallel slices whose paths do not overlap use separate working trees. (Matt Pocock, 7 July 2026, <https://www.aihero.dev/skills-implement>)
- A review keeps a standards list and a spec list. Each finding cites a line. The lists are not ranked into one winner. (Matt Pocock, 7 July 2026, <https://www.aihero.dev/skills-code-review>)
- A repeated mechanical miss becomes a check. A judgement stays in the file the reviewer reads. The always-loaded map gains a pointer. (Matt Pocock, 5 October 2026, <https://github.com/mattpocock/skills/blob/main/skills/engineering/retro/SKILL.md>)
- He measures cyclomatic complexity mixed with coverage as CRAP, a mutation check on the changed code, a dependency allow-list with no cycles, and an answer to a near-duplicate. He leaves the agent diff unread and manages from those scores. The ceilings he spoke moved. (Robert C. Martin, 20 April 2026, <https://x.com/unclebobmartin/status/2046206145597972849>; 25 May 2026, <https://x.com/unclebobmartin/status/2058869482815197320>)
- Each new model is a reason to delete instructions that corrected the previous one. Claude Code removed about half the system prompt for the 4.0 models (Cherny, interviewed 23 September 2025) and 80% for Opus 5. An ablation that strips the system prompts, including tool prompts, left the model a little more intelligent. The product still keeps the prompts that make it behave the way a person wants. The harness code that stayed is mostly safety, permissions, and static analysis. A prescribed step order is the wrong shape: state the task, the guardrails, and how to know it is done. Add a line back only when the same stumble repeats. (Boris Cherny, 27 July 2026)
- Overly specific skills, and an `AGENTS.md` that forces a full read before every edit, can hinder a stronger model. An older line that says to run the tests makes that model test more than the task needs. A hard stop written for a weaker model makes a more aligned model stop too early. Start the new model from the outcome. Carrying the old prompt stack over-specifies the path. (OpenAI, GPT-6 Astra guide, and the GPT-5.5 prompting guide, both read 27 October 2026)
- A skill states only what the model does not already know, and it is tested on every model that will load it. A fragile command stays exact. An open task stays a direction. What is right for the strongest model can be too thin for a smaller one. (Anthropic, skill authoring guide, read 27 October 2026)
- On issue-shaped tasks, a small tool loop matches a large product harness on pass rate and often costs less. Databricks, 8 July 2026, on a multi-million-line repo: same model, same effort, quality unchanged in the paired comparison; the vendor harness cost more than 2× the small one in some pairs, and the small one sent about 3× less context per turn. That post’s summary line also says quality changed. HarnessTax, 16 September 2026, on 30 tasks from each of two benchmarks: success within about ±2% and ±5%, and Claude Code often about 1.5× to 2× the cost of Pi. The authors say this may not hold when the work crosses sessions. (Pan et al.)
- Logan Kilpatrick, who leads Google AI Studio and the Gemini API, and who was at OpenAI before Google: the generic agent loop is scaffolding the model absorbs. His estimate in June 2026 was about 12 months. Search and code execution stay outside. A model that only works in its own harness is not general. Google’s products sit on a harness, Antigravity. He is not a former Googler, and he does not say to run with no outer policy. (Training Data, 11 June 2026)

### Choices the sources leave open

Where the pages disagree, or name no number, a project still has to pick. The lines below are those picks. They are not a consensus.

- A human decides intent, spec, evidence, and the routing posture. The agent does not approve or merge its own change in either posture. An agent’s approval never counts as a person’s. That start is closer to AISVS on the self-merge ban than to OpenAI or the Claude Code team’s low tier. Those teams publish the looser end. It is looser than AISVS AC.4.1: that control requires a different human to review the code of every AI-generated change. An evidence approval by the person who asked for the slice does not satisfy it. A metrics posture that leaves the diff unread does not satisfy it either.
- The four gates and the short risk list come from developertoolkit.ai, “judgment at the gates” (updated 16 October 2026). GitLab does not publish that table.
- Only the verifier sets `passes`. Anthropic’s November 2025 coding agent sets it.
- Verify or audit may send a slice back twice. The third rejection goes to the steward. No primary uses that count. The Claude 3.7 system card is why a cap exists: test edits and special-casing show up after repeated failures, and the card names no count. The closer published stops are three attempts, then raise (Anthropic, 23 January 2026, a sample default), and three review rounds, then reconsider the approach (Claude Code team).
- The build unit is a vertical slice. Spec Kit is the main published counter-pattern.
- Module order M0–M7, and the A–D scale that unlocks a later module, are local. OpenAI grades domains and layers and publishes neither the letters nor an unlock rule.
- Security uses the three triggers in [§10](#10-quality-attributes).
- Expected values are frozen before implementation. A check that goes red when the feature is deleted does not prove the oracle.
- A red invariant check stays blocking under both routing postures. G3, the evidence gate, stays in both. Dropping the diff read is the Metrics posture. It is not a license for a red check to land. Lopopolo conditions low-friction human waits on high throughput plus the invariant harness, and he publishes no escape rate.
- When the constitution names Read, a sample of evidence-only merges is still read. When it names Metrics, the person reads the evidence and does not read the agent diff. Until it names one, G4 does not treat either posture as the rule.
- The metric gates in [§11](#metric-gates) run in the implementer’s self-check. Until the constitution names a complexity score, the check reports cyclomatic complexity, CRAP, and cognitive complexity, and neither score blocks. Green metrics do not by themselves prove the spec. The gates do not replace G3.
- The glimpse-to-slice sequence in [§7](#7-outer-workflow-from-a-glimpse-to-the-pipeline) is a synthesis. No primary publishes it as one method.
- The spec names test seams, including a journey seam, and G2 freezes the spec on a small change. A feature slice that is only one layer fails the plan gate. A wide mechanical change is expand, then migrate, then contract. Standards findings and spec findings stay in separate lists. The verifier stays a fresh session. `AGENTS.md` stays a short map into `docs/`.
- A compensatory instruction is dated with the model it was written for. A model change removes it. A miss that repeats is written back. An invariant, a frozen oracle, a boundary, a fragile command, a judgement in the reviewer file, and the rule about who may merge stay, and they are not part of that deletion. Cherny’s ablation is the method. Applying it to this document’s outer policy, and keeping that policy through the ablation, is this document’s choice. [§19](#19-evolvability)

---

## 3. Failure modes the harness exists to stop

Each row is a way an agent build fails, the symptom a reader can recognize, and the response that stops the next session from inheriting that failure.

| Failure | What it looks like | Harness response |
| --- | --- | --- |
| One-shot | A session tries to build the whole app and dies mid-feature | One module, one slice, one session (Anthropic, November 2025) |
| Premature done | The agent sees some code and declares the product finished | Feature list starts failing. In Anthropic’s harness the coding agent sets the pass flag after driving the UI. In this document only the verifier sets it |
| Dirty handoff | Next session inherits a half-broken tree and spends itself repairing it | Boot, regression of the core journey, then new work; commit and progress log at the end (Anthropic) |
| Self-approval | The author writes the tests that bless the author | This document uses a fresh verifier and frozen examples. Anthropic’s November agent and OpenAI’s review loop still let the producing side take part in the pass |
| Spec drift | Code satisfies tests that no longer match the decision | Spec is an artifact. A plan-alignment check looks for a second mechanism. That check is OpenAI’s architectural lint. It does not detect an underspecified issue |
| Boundary erosion | Each slice invents its own auth, logging, or error shape | Constitution and mechanical fitness checks |
| Pattern infection | A bad local pattern gets copied into later slices | Golden rules plus a recurring cleanup pass (OpenAI). This document’s gardener is stricter than OpenAI’s automerge |
| Green but unsafe | Tests pass; the user-visible path, authorization, or a bound does not | Anthropic: unit tests and `curl` stayed green while the UI path was broken. This document adds the risk class and the security triggers in [§10](#10-quality-attributes) |
| Oracle rot | The agent edits a failing test, or special-cases the code to the tests | Accepted examples are frozen. Read-only tests stop edits and do not stop special-casing (ImpossibleBench). The freeze-before-code rule is this document’s |
| Silent gap | A stage emits prose with a missing decision, and the next stage invents it | Every artifact in this file has required fields; a missing field stops the chain. That field rule is this document’s |
| Unbounded retry | The same fix is attempted until the context is noise | Stop at this document’s revision cap and escalate. The system card motivates a cap and names no count |
| Stale guide | A line written for an older model stays in force. The new model follows it and stops early, rereads the tree, or takes the prescribed path | Remove the line, run the gate it was meant to protect, and write it back only if the same miss repeats (Cherny, 27 July 2026). An invariant stays. That split is this document’s |

---

## 4. Design principles

These principles decide what a prompt, an artifact, and a gate may do. A session stays small, the record lives in files, and human time goes to decisions a check cannot make.

1. **One prompt, one purpose.** Research, decide, specify, plan, implement, verify, audit, release. If a prompt does two, split it.
2. **Durable artifacts, not chat memory.** The next session reads files, including the progress log and git history. It does not read the previous conversation.
3. **Progressive disclosure.** A short map is always in context. Deep documents are read when the slice needs them.
4. **Invariants over style.** The harness is strict about boundaries, data validation, authorization, logging, and budgets. It is loose about local expression inside a slice.
5. **Deterministic outer loop.** Anything a program can observe, and that must always be true, is a check the runtime can fail, with an error message that says how to comply. An intent nobody stated has no sensor (Böckeler).
6. **Readers fan out; writers do not.** Many review lenses may read the same diff. One implementer writes. The lens report keeps a standards list and a spec list, and it does not blend them into one winner. [§12](#12-review-how-when-by-whom) has the rule. The fresh verifier stays.
7. **Evidence before opinion.** A gate records the command, the version, and the result. A summary is not evidence.
8. **Human time is the scarce resource.** Spend it on intent, unresolved assumptions, evidence, and the routing posture the constitution named. Under Read, that includes risk-class diffs, a change whose checks have a blind spot nobody can name, and a sample of evidence-only merges. Under Metrics, that includes the evidence and a play-test of the running system. Do not spend it re-checking what a deterministic gate already failed.
9. **Small, mergeable, recoverable.** Each slice ends in a state another session can boot. Bad work is reverted to the last green commit.
10. **Feedback becomes a rule.** A repeated miss is written down so the next session cannot repeat it (Hashimoto’s practice, and OpenAI’s “write the rule into the repo”). A mechanical miss becomes a check the implementer can run. A judgement stays in a file the reviewer reads. The always-loaded map gains a pointer. [§12](#12-review-how-when-by-whom) is where that lands.
11. **A compensation expires.** An instruction that tells the model which steps to take, or that corrects a miss the current model no longer makes, carries the model it was written for. On a model change it is removed, the gate it names is run, and only a miss that repeats is written back (Cherny, 27 July 2026). An invariant carries no such date. [§19](#19-evolvability)

---

## 5. Roles

Roles are prompts plus permissions, and the author and the judge run in different sessions even on the same model. A human blocks the pipeline at intent, spec, and evidence. The constitution chooses whether a person also reads the diff.

Anthropic’s November 2025 harness does not require that split: one coding agent implements, drives the UI, and sets `passes`.

The four gates and the risk list below are the recipe on developertoolkit.ai, “Human in the loop: judgment at the gates” (updated 16 October 2026). GitLab’s playbook does not use those four rows. GitLab’s point is that the heavy checkpoint is the plan, before code.

Published merge rules disagree.

- OpenAI’s harness post: humans may review and are not required to. Agents often squash-merge their own pull requests. The loop runs until agent reviewers are satisfied.
- Claude Code team, 15 October 2026: low-risk changes can merge with no extra human approval once automated review and security checks are green. Their examples are docs, copy, internal-only features, and additive changes behind a flag. Where a person is required, the person who prompted the agent counts as that reviewer. An agent’s approval never counts. After three review rounds the reviewer asks the author to reconsider the approach.
- OWASP AISVS 1.0 Appendix C, June 2026, Level 1: a qualified human who is not the person who asked for the generation reviews every AI-generated change. The agent does not count as that human (AC.4.1). An autonomous agent cannot approve, merge, sign, or deploy an artifact it generated. The source-control system, the CI system, and the artifact registry enforce that (AC.8.1). Policy text alone does not satisfy AC.8.1.

The constitution names the routing posture.

- **Read.** A person other than the author reads the change, including the tests, for the risk classes below, when nobody can name what the checks would miss, and on a sample of merges that were cleared on evidence alone.
- **Metrics.** The person does not read the agent diff. They decide intent and spec, read the evidence, and play-test the running system. A metric gauntlet once passed and a later play-test still found ten bugs, half of them specification misses (Robert C. Martin, 6 June 2026, <https://x.com/unclebobmartin/status/2063344635150553131>).

Until the constitution names one, neither posture is the rule. In both, the agent does not approve or merge its own change, and an agent’s approval never counts as a person’s. That ban is closer to AISVS AC.8.1 than to OpenAI’s self-merge. It is looser than AC.4.1, which requires a different human to review the code of every AI-generated change. Read does not satisfy AC.4.1 when the person who asked for the slice is the only reader. Metrics does not satisfy it at all.

| Role | May write product code | May write tests / oracles | May approve | Fresh context |
| --- | --- | --- | --- | --- |
| Steward (human) | No | Accepts the spec | Yes, at the four human gates | — |
| Researcher | No | No | No | Yes |
| Spec author | Spec and acceptance examples only | Draft examples; steward accepts them | No | Yes |
| Planner | Plan only | Names which checks must exist | No | Yes |
| Implementer | Yes, inside the allowed paths | May add cases; may not weaken or delete an accepted oracle | No | Yes, one slice |
| Verifier | No | May add failing cases whose expected value comes from the spec; may not “fix” product code | Recommends only | Yes; no author’s reasoning |
| Auditor | No | No | Pass / reject on the checklist | Yes |
| Lens reviewers | No | No | Findings only | Yes, parallel, same frozen diff |
| Release runner | Pipeline config if that is the slice | No | No | Yes |
| Gardener | Refactors that do not change behavior, in their own slice | Existing suite must stay green | No | Scheduled |

**Steward decisions, and only these, block the pipeline by default.** That sentence is this document’s choice, not AISVS and not OpenAI.

| Gate | Decision | Steward looks at | If skipped |
| --- | --- | --- | --- |
| G1 Intent | Is this worth building, and what is out of scope? | One paragraph: outcome, actor, non-goals | The wrong product, built well |
| G2 Spec | What is done, which assumptions are confirmed, which are guesses? | Acceptance criteria, test seams, assumptions, scope of files | Invented business rules |
| G3 Evidence | Do the frozen examples still hold, and does a small rule change fail them? | Spec delta, results, oracle diff, runtime proof | A deletion-red test, or a test that restates the code |
| G4 Routing posture | Apply the diff-posture the constitution named | Changed paths, the blind spot, the evidence, and, under Read, the sample | Posture unset, Read leaves a risk class or the sample unread, or Metrics merges with no evidence |

G2 is the expensive human moment on this recipe, and it matches GitLab’s point about the plan. A wrong assumption fixed there is a sentence. Fixed after implementation it is a rerun. Making non-goals a hard stop at G1 is this document’s rule. Böckeler’s August 2025 generations added behavior nobody requested, and the fillings changed between runs.

**Risk classes.** This list is the method page’s escalation list, plus production promotion. Keep it short. Under Read, these diffs are read, including the tests. Under Metrics, they still get the allow and deny cases, the evidence packet, and a play-test. They do not force a diff read.

- authentication and authorization
- anything that moves value, quotas, or irreversible user data
- stored data shape and migrations
- changes to the oracles, the linters, or the pipeline
- a slice no automated check covers
- production promotion of a release candidate

AISVS Level 2 elevates authentication, authorization, cryptography, IAM, CI/CD workflow, deployment manifests, and sandbox or network policy (AC.4.4). The Claude Code team’s named set includes billing, the auto-updater, CI, auth, release workflows, and installers, plus a classifier that can still demand a person. Schema, migrations, oracles, and an uncovered slice are the method page’s additions. This document keeps them.

Under Read, outside that list, an evidence approval names what the checks would miss, and a sample of those merges is still read ([§12](#12-review-how-when-by-whom)). Under Metrics, the person reads the evidence on every slice, including a risk class, and does not read the agent diff. A play-test of the running system still happens. Low-risk slices can ship behind a progressive rollout with automatic rollback once that mechanism exists. Until it exists, a human still presses promote. The rollout step is this document’s later easing, taken from the same method page.

---

## 6. Persistent artifacts

Lay these out in the repository, each with a single writer and a schema, under conventional names. The tree below is this document’s layout.

Anthropic’s handoff files sit at the repo root under the names in the November 2025 post. OpenAI’s knowledge base is the `docs/` tree the map points at.

```text
AGENTS.md                      # see the three uses below
docs/
  constitution.md              # invariants, quality bars, risk classes, complexity-score, diff-posture, instruction-model
  architecture.md              # domains, allowed dependency edges
  security.md                  # trust boundaries, data classes, threat notes
  reliability.md               # error budget, timeouts, retries, idempotency
  quality-score.md             # this document’s grade per domain
  product-specs/
    index.md
    <slice>.md                 # accepted spec
  exec-plans/
    active/<slice>.md
    completed/
    decisions.md               # why, and the alternative rejected
  features.json                # this document’s feature list
  progress.md                  # this document’s session log
  evidence/<slice>/            # commands, results, limitations
init.sh                        # boot the app
```

`init.sh`, in Anthropic’s post, starts the dev server so the next session does not rediscover the boot. Per-worktree logs, metrics, and traces are OpenAI’s legibility work. They are not a field of that script.

### Complexity score and diff posture

The constitution names which score blocks, and which posture G4 applies, before any agent writes product code. Until a field is filled, the check that depends on it reports, and neither option is the rule.

`complexity-score` is `crap` or `cognitive`. `diff-posture` is `read` or `metrics`.

`crap` keeps functions easy for an agent to follow: cyclomatic complexity, optionally mixed with coverage as CRAP. The formula in [§11](#metric-gates) is CC² × (1 − coverage)³ + CC. `cognitive` keeps control flow easy for a person to follow (G. Ann Campbell, Sonar, 7 December 2016). Until `complexity-score` is named, the check runs and reports both, and neither blocks. This file installs no ceiling. Spoken CRAP ceilings stay history in §11. A ceiling of 10 stays unused. If the constitution chose CRAP, §11 blocks on that score at the ceiling the constitution named. If it chose cognitive complexity, §11 blocks on that score at the ceiling the constitution named. Any tool that emits the chosen score is allowed.

`read` means a person other than the author reads the change, including the tests, at least for the risk classes and whenever nobody can name what the checks would miss. Evidence-only merges are sampled. `metrics` means the person does not read the agent diff. They set intent and spec, read the evidence (commands, frozen examples, metric results), and manage from those scores. A play-test of the running system still happens. Both stay valid. The agent does not approve or merge its own change in either posture. An agent’s approval never counts as a person’s. Green metrics do not by themselves prove the spec. G4 is where the named posture is applied. [§5](#5-roles) has the risk classes. [§15](#15-worked-example--desk) may name one posture for that pass. The other posture stays available.

`instruction-model` is the model the compensatory lines were written for. [§19](#19-evolvability) is the deletion rule. Until it is named, those lines have no baseline, and the deletion pass has nothing to compare.

### Three files that share the name `AGENTS.md`

The same filename is an always-loaded instruction spec, a short map into `docs/`, and a set of Copilot persona files. This repository keeps the short map, so the constitution stays reachable from the file that is always in context.

The August 2025 spec (agents.md; donated to the Linux Foundation’s Agentic AI Foundation on 9 December 2025) is an always-loaded instruction file. It has no required fields and no required length. Suggested contents are build and test commands, style, and similar notes a human README leaves out.

OpenAI’s February 2026 practice is a later, separate use: a map of about 100 lines into `docs/`. A giant file of the same name had already failed in that repo.

Copilot persona files live at `.github/agents/`. They are not the August 2025 spec. Matt Nigh’s GitHub blog (19 November 2025) is a write-up of those persona files, and it is the page behind a “2,500 repositories” figure. There is no primary census of 2,500 repositories using the AGENTS.md spec in these notes.

Gloaguen et al. (arXiv:2602.11988, 13 February 2026) measured always-loaded context files on issue-solving tasks. LLM-generated files lowered average resolution by about 0.5 percentage points on SWE-bench Lite and by about 2 points on their 138-instance set, and raised cost by about 20% and about 23%. Their conclusion is that extra requirements make the task harder, and that a human-written file should state only requirements the agent cannot infer. Developer-written files beat generated files for every agent they tried, and beat no file for every agent except Claude Code.

Chatlatanagulchai et al. (DOI 10.1145/3840295) counted 2,303 root context files in 1,925 repositories. Security and performance appear in about one file in seven. The sample is root files only. Absence from the file does not mean the project has no security tooling. It means the agent is not told.

The always-loaded file stays a short map into `docs/`, because a file with no pointers would drop the constitution. A three-line file, and a generated encyclopedia, were not taken (Matt Pocock, 18 January 2026, <https://www.aihero.dev/a-complete-guide-to-agents-md>).

### `features.json`

Every feature starts failing, and only the verifier may set `passes`. This document adds `id` and `module`, keeps the list under `docs/`, and the implementer does not edit descriptions or steps.

Anthropic’s published file is `feature_list.json` at the repo root, with `category`, `description`, `steps`, and `passes`. The coding agent in that harness changes only `passes`, and only after it has driven the feature.

```json
{
  "id": "F12",
  "module": "M3",
  "category": "functional",
  "description": "A member can claim an open item that nobody else holds.",
  "steps": [
    "Sign in as member A",
    "Open an unclaimed item",
    "Claim it",
    "See it as claimed by A",
    "Sign in as member B and see that claim is refused"
  ],
  "passes": false
}
```

### Slice spec (required fields)

If any field is empty, G2 fails and implementation does not start. The field list is this document’s schema.

- outcome and actor
- done-means (observable results, not tasks)
- non-goals
- assumptions, each marked `confirmed` or `guess`
- invariants this slice must not break
- data written and data read, with sensitivity class
- security-critical paths touched, if any
- allowed paths and forbidden paths
- new dependencies, or “none”
- acceptance examples: given / when / then, including one rejection case, with expected values fixed here
- test seams: the public boundary each test uses, what it catches, and what it misses. Prefer an existing high seam. The steward accepts this list at G2. The list includes a journey seam. One refusal example stays in the spec, and it may be a case at that seam.
- quality budgets that apply, each one either a named latency bar or an API4-style bound, or “unchanged”
- risk class
- how a failure is rolled back

### Plan (required fields)

OpenAI’s ExecPlan is a living document: the implementer resolves ambiguity and revises the plan during the run. G2, which stops until a guess is confirmed or accepted, is this document’s gate, and the fields below are this document’s thin plan, including the blockers and the cut, not that ExecPlan’s required sections.

- ordered steps inside the slice
- files expected to change
- which existing mechanism will be reused (auth, logging, errors)
- checks to add, and the command that runs them
- what will be left untouched
- rollback of this slice (revert to commit)
- blocked by: other slice ids, or “none”
- cut: the user-visible path through the layers this slice touches, or a named exception (constitution, quality evidence, release, or expand-contract)

`blocked by` and `cut` are required. A feature slice whose cut is a single layer fails GP. A wide mechanical change, one edit whose blast radius is the whole tree, is expand, then migrate in batches that stay green, then contract by deleting the old form. M0, M6, and M7 use the named exception. They are not tracer bullets.

### Progress log

The next session must read this log, and must read git history. This document’s entry is: slice id, commit, what passed, what failed, dead ends and why, next slice.

Anthropic’s November file (`claude-progress.txt`) has no published field list. The dead-end requirement is Anthropic’s scientific-computing note (23 March 2026): failed approaches have to be written down or the next session repeats them. That note’s filename is `CHANGELOG.md`.

### Evidence record

The record binds the commit to the commands that ran, the example frozen before implementation, and a small rule change that must fail that example.

```text
slice: M3
commit: <id>
spec: docs/product-specs/M3.md
commands:
  - <boot>
  - <unit/contract/journey commands>
results: pass | fail
frozen-example: <accepted given/when/then, fixed before implementation>
rule-change: <the small change to the rule that must fail that example>
limitations: <what was not exercised>
risk-class: authorization
complexity-score: crap | cognitive | unset
diff-posture: read | metrics | unset
human-read: yes | sample | metrics
blind-spot: <what the checks would miss, or "none named">
```

A line that only says the check would go red if the feature were removed does not fill `rule-change`. A test that asserts the code’s current return value fails that deletion too.

---

## 7. Outer workflow, from a glimpse to the pipeline

The build moves from a glimpse to a release candidate in a fixed stage order, and a stage starts when its input artifact validates. After the feature list exists, the work proceeds one vertical slice at a time.

```text
glimpse
  → G1 intent
  → research the constraints (no code)
  → constitution + architecture map
  → feature list (all red)
  → walking skeleton          ┐
  → each later module         ├─ inner loop, one slice at a time
  → quality-attribute slices  ┘
  → release candidate
  → G4 promotion
  → observe, then gardener
```

The sequence above is this document’s synthesis. No primary publishes it as one method.

GitHub Spec Kit’s command reference does not start with vertical slices. The core order is a constitution of principles, then, per feature, specify, plan, tasks, and implement. The task template’s foundational phase blocks every user story: schema, authentication framework, routing, base models, error handling, and logging. Inside a story the order is models, then services, then endpoints. That foundation, then that per-story order, is the main published counter-pattern to the vertical slices this document chooses. Böckeler’s comparison on martinfowler.com (15 October 2025) is the independent write-up of Spec Kit beside Kiro and Tessl. OpenAI’s post is the source for a `docs/` tree of specs and plans, not for this stage order. Anthropic’s November initializer writes one failing feature list from the prompt, in the same session as `init.sh`, with no constitution stage.

Vertical slices, one user-visible capability through the layers, stay this document’s build unit. QualityLayer’s spec-driven guide uses similar outer language and is first-party marketing for that product. It is not a source for this sequence.

| Stage | Prompt purpose | Human | Artifact out | Hard stop if |
| --- | --- | --- | --- | --- |
| 0 Glimpse | Research | G1 | `intent.md` | Outcome or non-goals missing |
| 1 Frame | Decide | G2 on constitution | `constitution.md`, `architecture.md`, `security.md`, `reliability.md` | No invariants, no allowed edges |
| 2 Enumerate | Specify | Steward scans for invented scope | `features.json` | Features are tasks, not observable behaviors |
| 3 Slice | Specify → plan → do → verify → audit | G2 then G3/G4 | spec, plan, code, evidence, pass flag | See inner loop |
| 4 Harden | Do | G3/G4 | security, capacity, efficiency evidence | The release-level set is missing or red |
| 5 Release | Do | G4 promote | pipeline run, rollback proof | Core journey red, or G4 has not applied the constitution’s diff-posture |
| 6 Garden | Refine | Evidence only, unless behavior changes | cleanup slices, quality grades | A “cleanup” changes behavior |

Stages 0–2 happen once per product, then again only when the intent changes. Stage 3 repeats. Stage 6 runs on a cadence, not inside a feature session. The steward scan at stage 2 is this document’s gate. Böckeler’s August 2025 runs are the reason it stays. Anthropic’s November initializer does not have that scan. The all-red list itself is Anthropic’s.

**Dependency rule.** A stage may start when its input artifact validates. Independent research questions may run in parallel. Implementation of two slices may run in parallel only when their allowed paths do not overlap; otherwise one writer wins and the other waits. Those parallel slices use separate working trees. One report of three sessions in a single checkout is that they amended each other’s commits (Matt Pocock, 7 July 2026, <https://www.aihero.dev/skills-implement>). Review lenses on one frozen diff always run in parallel.

---

## 8. Inner loop (every module, every slice)

Every slice follows one order, from orient through a fresh verify, lenses, and audit, then the human evidence and risk gates. Verify or audit may return the slice twice, and the third rejection goes to the steward with the evidence.

```text
orient → specify → G2 → plan → implement → self-check
      → verify (fresh) → lenses if verify is clean
      → audit → G3 → G4 route → merge or reject
```

The name Plan-Execute-Verify is Augment’s, and that guide cites no paper for it. This document still separates specify, plan, implement, self-check, and verify. Anthropic’s November 2025 coding agent implements and self-verifies in one session.

Verify or audit may send the slice back at most two times. The third rejection escalates to the steward with the evidence. That count is this document’s rule. The Claude 3.7 system card is why a cap exists: after repeated failures the model returns expected test values or edits the tests, and the card names no count. The closer published stops are three attempts, then raise, in Anthropic’s 23 January 2026 black-box verifier sample, and three review rounds, then reconsider the approach (Claude Code team, 15 October 2026). The Claude Code Ralph plugin’s `--max-iterations` defaults to unlimited. The README’s safety example is 20. Completion can be a string the same agent prints, and the README does not check that the string is true. Stripe’s minions return the branch to a human after a second CI run. That is their CI stop, not a cap on slice revisions.

### Orient (every session, before new work)

New work starts only after the history, the boot, and a green core journey are confirmed. The session then takes the single highest-priority red feature whose module is unlocked.

Anthropic’s November post numbers three startup steps: `pwd`, the git log and the progress file, and the feature list. The prose and the prompting guide add the boot and a core-journey check. Step 3 below adds “whose module is unlocked,” which is this document’s grade rule.

The session that continues the work reads the progress log and git history. Withholding the build transcript does not apply here. It applies to the verifier in the next subsection.

1. Confirm the workspace and that writes stay inside it.
2. Read the map, the progress log, and the latest commits.
3. Read `features.json` and select the single highest-priority red feature whose module is unlocked.
4. Boot via `init.sh`.
5. Run the core journey. If it is red, repair that before any new feature. Anthropic’s prompt also says to mark that feature failing again.
6. Only then implement.

### Specify

The spec author asks questions before writing, guesses are labeled, and the steward answers or accepts them. Implementation is forbidden in this session, and the accepted examples, including expected values, freeze at G2.

Each seam names the public boundary, what the test catches, and what it misses. The steward accepts that list at G2 with the examples. A sentence the steward never said is a defect (Matt Pocock, 27 April 2026, <https://www.aihero.dev/skills-to-spec>). G2 still freezes the spec on a small change, and the spec remains the contract after the slice ships. A page that would skip the spec for a small change, and discard it after shipping, was not taken (Matt Pocock, 27 April 2026, <https://www.aihero.dev/skills-to-spec>).

### Plan

The planner names the existing mechanism to reuse, what blocks this slice, and the cut. A plan that introduces a second way to authenticate, log, persist, or return errors fails plan-alignment before any code, and a feature slice whose cut is a single layer fails GP.

That reuse check is this document’s gate. OpenAI enforces shared helpers and boundary validation with lints; the lint text says how to comply. A wide mechanical change, one edit whose blast radius is the whole tree, is expand, then migrate in batches that stay green, then contract by deleting the old form. M0, M6, and M7 record a named exception. They are constitution, evidence, and release (Matt Pocock, 27 April 2026, <https://github.com/mattpocock/skills/blob/main/skills/engineering/to-tickets/SKILL.md>).

### Implement

One feature stays inside the allowed paths, cases take their expected values from the accepted spec, and accepted oracle steps stay unedited. The session ends with a commit and a progress note even if verification has not flipped the flag yet, the tree must boot, and a send-back includes that failure so the repair starts from it.

At each seam the spec accepted, write one failing test whose expected value is the frozen example, then the code that passes it. Do not write the rest of the suite first. Do not recompute the expected value the way the code does. Refactoring is not this cycle. After G2, this session does not propose a different approach (Matt Pocock, 7 July 2026, <https://www.aihero.dev/skills-implement>). If the accepted spec cannot be built as written, stop. Run the fast local checks during the slice, including the metric gates in [§11](#metric-gates), and the full suite once at the end. The implementer commits a bootable tree. The review that can fail the slice is the fresh verifier, then the lenses.

### Self-check

The implementer runs the deterministic commands and the new journey, may repair, and may not declare the feature passed. Anthropic’s November agent may declare it passed after its own UI drive, and this document does not copy that.

### Verify

The verifier is a separate role, stays a fresh session, and is the only role that sets `passes`. Anthropic’s November 2025 harness does not require that split, and a review inside the producing session is not the pass (Matt Pocock, 7 July 2026, <https://www.aihero.dev/skills-implement>).

The verifier receives the spec, the frozen diff, the commands, and any failure already recorded. It does not receive the author’s reasoning from the build. That withholding is the narrower rule for a black-box test verifier, or for a reviewer who must not inherit the author’s reasoning. Claude Code’s `/code-review` subagent is described that way. Anthropic’s January 2026 note says a verifier that only runs tests does not need to know why the artifact was built as it was. The repair turn is the opposite case: the implementer is given the failure.

For each done-means line the verifier either shows evidence or marks the line unchecked. It sets `passes` only when the journey matches the frozen steps. A check that goes red when the feature is deleted does not prove the oracle. The expected values were fixed before implementation, and a small change to the rule must fail those frozen examples.

### Lenses

Lenses run only after verify is clean, so a cheap failure does not pay for a wide review, and every lens reads the same frozen diff. Findings are evidence, not edits, and a standards finding and a spec finding stay in separate lists.

Lenses: correctness, boundaries, security, tests, simplicity, operability. Six lenses on every clean slice are this document’s set. Anthropic’s June 2026 workflows note says a panel of reviewers has to earn its cost, and that ordinary coding stays in one context. [§12](#12-review-how-when-by-whom) has that rule.

### Audit

The auditor re-runs the checklist against the latest tree and, on failure, returns the failing rows to the implementer without patching. A separate auditor who does not inherit the verifier’s word is this document’s role.

The closest published pieces are a second QA pass and a vote before a review comment is posted. Neither is this checklist.

### Merge

Merge the slice when audit is pass, G3 is accepted, and G4 has applied the diff-posture the constitution named. Under Read, a person other than the author has read the change, including the tests, for a risk class, an unnamed blind spot, or the sample. Under Metrics, the evidence record is accepted and the person has not been asked to read the agent diff. Until the posture is named, G4 does not merge. The commit message names the feature id, the implementer does not merge, and AC.8.1 is satisfied only when the forge enforces that, not when the prompt says it.

---

## 9. How to build each module

A module is a set of slices that share one reason to exist. Order them so each module is exercisable through the public behavior, and so later modules sit on invariants rather than on hope.

The illustration below uses **Desk**: a person submits an item, a member claims it, a member closes it, and a status can be read. Substitute any other product; the module types stay.

The order below is this document’s choice. OpenAI does not publish it.

M1 is a walking skeleton in Cockburn’s sense: a tiny end-to-end function that links the main architectural parts and stays as real code. Freeman and Pryce’s bar for that first slice is that it can be built, deployed, and tested end to end. The checks against the rest of the code are fitness functions in the sense of Ford, Parsons, and Kua: an objective assessment of a characteristic you meant to protect. OpenAI’s reason to put dependency edges in early is agent throughput: the constraints are what allow speed without drift, and the lint text carries the remedy. OpenAI’s own layer graph is a forward-only stack. The Desk edges are the hexagonal ones: domain rules do not depend on delivery or storage (Cockburn, ports and adapters). “One storage path” in the Desk constitution is this document’s rule, along the lines of OpenAI’s preference for one shared helper. Hexagonal architecture expects the real store and a test double to be interchangeable adapters. The citation for “make the edges mechanical” is OpenAI and Ford.

### Module types and the order to build them

Each module becomes exercisable through public behavior before a later module depends on it. Dependency direction, validation at the trusted service layer, and secret checks are on from the skeleton.

| Order | Module | Why this order | Unlocks |
| --- | --- | --- | --- |
| M0 | Constitution | Agents need edges and bars before speed | Every later plan-alignment check |
| M1 | Walking skeleton | One item can be submitted and read back, deployed in an isolated environment | Boot, journey, pipeline stub that can deploy and test |
| M2 | Identity and access | Later writes must have an actor | Sessions, roles, “who is calling” |
| M3 | Core state changes | Claim and close, with authorization | The business lifecycle |
| M4 | Query | List and filter without breaking write invariants | Read models, pagination |
| M5 | Operability | Errors, limits, audit, correlation | Debuggability and abuse bounds |
| M6 | Quality attributes | Release-level security, plus efficiency and traffic evidence | A candidate that is more than “it works once” |
| M7 | Release | Repeatable build, promote, rollback | CI/CD end |

Do not build M4 before M3 if the query would freeze the wrong lifecycle. That reason is this document’s, for this product. Do not build the load and fan-out work in M6 before M1, because there is nothing to measure. Dependency direction, validation at the trusted service layer, and secret checks are on from the skeleton. They are not waiting for M6. Do not leave M2 for last: every slice after identity should call the one access mechanism.

### Inside a module

A session re-checks earlier journeys, adds the smallest end-to-end behavior together with one refusal, then the boundary cases, and re-checks those journeys before it stops.

1. At session start, the core journeys of earlier modules. If one is red, repair it before new work. That order is Anthropic’s session start.
2. Smallest new end-to-end behavior (happy path plus one refusal). Pairing the refusal into the same first slice is this document’s rule. Cockburn’s skeleton stories are one small function. The Elephant Carpaccio sample puts a refusal in a later slice.
3. Boundary cases (empty, duplicate, conflict, timeout).
4. The same core journeys again at the end of the session.

### What “built” means for a module

A module counts as built when every feature has passed with evidence or been deferred by the steward, earlier journeys and dependency edges still hold, and the grade names the gaps that remain.

- Every feature in that module is either `passes: true` with evidence, or explicitly deferred in the spec with a steward decision.
- The core journeys of earlier modules still pass.
- Dependency edges still match `architecture.md`.
- No new secret, no new storage mechanism, no new error shape, unless the constitution was changed in its own slice and reviewed.
- `quality-score.md` has a grade for the module, on the scale below, including the gaps.

### Quality against the rest of the code

Local correctness is “this slice does what its spec says.” Whole-product quality treats dependency edges, secret checks, and a numeric bar as fitness functions when two readers would get the same pass or fail, and treats plan alignment and the letter grade as judgments that tell the gardener where to look and do not unlock the next module in any rule OpenAI published.

| Check | Question | When |
| --- | --- | --- |
| Journey regression | Do the already-green features still pass? | Start and end of every session |
| Dependency fitness | Does the diff respect allowed edges? | Every slice, CI |
| Plan alignment | Did it reuse auth, logging, errors, persistence? | Plan gate and verifier |
| Contract at the boundary | Do neighbors still see the same shapes? | Slice that touches a boundary |
| Data fitness | Is new stored data classified, and validated at the trusted service layer? | Any write |
| Budget fitness | Does the named latency bar hold, and are page size, payload, and fan-out bounded? | Hot path, and M6 |
| Oracle fitness | Were accepted steps or assertions weakened? | Every slice |
| Grade | What is still known-bad in this domain? | Auditor, then gardener |

A slice can be locally green and still fail the module gate if any row is red.

### Grading

This document uses a coarse scale so the gardener knows where to work. The letters, and the three unlock sentences under the table, are this document’s.

OpenAI’s quality document grades each product domain and architectural layer and tracks gaps. Background tasks update those grades. The post does not publish letter meanings, and a letter does not unlock the next module.

| Grade | Meaning |
| --- | --- |
| A | Invariants hold, journeys pass, budgets measured, no known high gaps |
| B | Journeys pass, one known gap with a tracked slice |
| C | Works for the demo path, budgets or auth not evidenced |
| D | Do not build the next module on top of this |

On this scale, M1 may ship at C inside the repository. M7 may not promote anything below B. M2 and M3 must be A or B before M4, because queries on top of a weak access model copy the weakness.

---

## 10. Quality attributes

Each attribute has a bar, a check, and a trigger. A bar is a binary rule, or a number that names the request, the proportion, the threshold, the measurement point, and the window (Google SRE, service level objectives); this file leaves the milliseconds unset, and OpenAI’s “startup under 800 ms” and “no span over two seconds” stay example prompts, not bars for Desk.

### Traffic (capacity)

There is no latency gate until the constitution names the five fields of a bar. The write path rejects excess work, and a read grows with the page.

Bar: until the constitution names the request, the proportion, the threshold, the measurement point, and the window, there is no latency gate. The write path rejects excess work instead of queueing without bound. Timeouts, maximum payload, page size, operations per request, and rate limits follow OWASP API Security Top 10, 2023 edition, unrestricted resource consumption: the API is vulnerable when those bounds are missing or left for the client to set. This document does not invent the milliseconds, the page count, or the rate.

Check: at M6 and before promotion, a repeatable load scenario against the isolated environment. On the slice that adds a list, a static check that the page size cannot be “all,” that an oversized payload is rejected, and that storage round-trips do not grow with the page length. Record latency of successes and of failures separately, plus traffic, errors, and saturation.

Trigger: the load scenario at M6 and before promotion. The static bounds on the slice that adds the list or the entry point. Not on a copy change.

Fail: the named proportion misses the named threshold in the named window, excess work is accepted without a bound, or a read grows with total data instead of with the page.

### Efficiency

The core write does a bounded number of storage round-trips, and a list read stays within the page.

Bar: the core write does a bounded number of storage round-trips; no per-item query inside a loop over a list; payloads over the stated size are rejected; work that can be deferred is not on the request path. The round-trip bound is this document’s. It is not one of OpenAI’s example prompts.

Check: query and round-trip count in the journey trace; a size limit at the boundary. OpenAI’s requirement that does transfer is legibility: if the agent cannot query the trace, a numeric budget is not a gate.

Trigger: M3, M4, M6, and any slice whose plan mentions a loop over records.

Fail: round-trips scale with list length, or a single request can force an unbounded read.

### Security

Three triggers, and “Every slice that crosses a trust boundary” is the wrong trigger. A logging change can print a token, a UI slice can add a package, and the dangerous diff is often an edit to the one allow function, a migration, or a workflow permission.

**Every change.** Known-pattern secret blocking on the push. A pipeline secret scan as well. GitLab’s secret push protection skips a file or diff patch larger than 1 MiB, skips a push that changes more than 3,150 paths or 350,000 lines, and a scan timeout accepts the push. Agent-sized diffs are the pushes that fall through a forge check. A new dependency is named in the spec, or the slice stops. New input is validated at the trusted service layer against an allow-list, a shape, or a logical limit (OWASP ASVS 5.0.0, asvs.dev).

**The diff touches a security-critical path.** One allow case and one deny case for the access this diff changes: other actor, missing actor, and a field that must not come back. Under Read, a person other than the author reads that path, including the tests. Under Metrics, the allow case and the deny case are in the evidence, and the person does not read the agent diff. The paths are authentication, authorization, cryptography, data shape and migrations, and pipeline or deployment policy.

**Release, once.** The full requirement set at the ASVS level the constitution chose, with pass or fail per requirement. M6 is that verification. It does not replace the two rows above, and it is not waived inside the session.

AISVS Appendix C is stricter than these three rows at Level 1: AC.4.1 requires a different human on every AI-generated change. Read reviews the risk classes, an unnamed blind spot, and a sample. Metrics does not review the agent diff. Both depart from that control. AC.8.1 matches this document only when the forge refuses the implementer’s self-merge. AC.4.2, Level 2, asks for SAST, IAST, DAST, secret scanning, IaC scanning, and SCA on every AI pull request. This document does not claim AC.4.2 at M1. Secret scan, the named dependency, and the allow and validation cases are the checks that can fail closed on a slice now.

Fail: a cross-actor case succeeds on a path the diff touched, a secret scan hits, a new dependency is unnamed, or the release-level set is red.

### Reliability

A timeout becomes a known error, a retried claim keeps a single holder, and the process refuses to start without its required configuration.

Bar: a timeout becomes a known error, not a hung request; a retried claim does not create two holders; a partial failure is visible; the process refuses to start without its required configuration. The last of those is this document’s harness check. The ASVS sentences that are in the standard are: an unexpected failure does not fail open, a dead dependency degrades or opens the circuit instead of hanging, and a limited resource cannot be double-booked by a replay. Retries are bounded. A permanent error is not retried. This document does not copy worked examples of queue depth or retry rate out of the SRE book.

Check: fault cases in the journey (dependency slow, dependency down, duplicate request); an idempotency case for each state change.

Trigger: M3 and M5, and any slice that adds a retry or a downstream call.

### Operability

Every request carries a correlation id, logs stay structured, and the core journey emits a trace the agent can query. Credentials stay out of the logs.

Bar: every request carries a correlation id; logs are structured; the core journey emits a trace the agent can query; a failure names the feature id. Credentials are not logged.

Check: the journey’s trace contains the correlation id and the outcome; logs contain no unstructured dump of the whole request. Until the agent can query that trace, a numeric budget is not a gate (OpenAI’s legibility point).

Trigger: M5, then regression.

### Privacy and data

Each stored field has a protection level covering return, logging, retention, and access. A status read returns state and holder, and private notes stay out of that response.

Bar: each stored field has a protection level covering return, logging, retention, and access. The names `public`, `account`, and `secret` are this document’s vocabulary for Desk. A status read returns state and holder, not private notes. ASVS Level 1 does not require the classification document. Promoting at Level 2 without it does not match that level.

Check: a read-as-other-actor case, and a field review on any schema change. Schema changes are a risk class. G4 applies the constitution’s diff-posture.

### Change safety

A slice can be reverted by commit, migrations are forward-only and reviewed, and once promotion exists the pipeline can return traffic to the previous candidate.

Bar: the slice can be reverted by commit; migrations are forward-only and reviewed; the pipeline can return traffic to the previous candidate once promotion exists.

Check: rollback is named in the spec and, at M7, rehearsed on a non-production environment. The first production exposure is the canary in [§14](#14-cicd-shape).

---

## 11. Quality gates

A gate is a named check with a verdict, an owner, and a consequence. Soft gates may be waived by the steward with a reason and a follow-up slice, and the implementer cannot waive a hard gate.

Guides steer before the agent acts. Sensors observe after, and the useful ones say how to comply (Böckeler, 2 April 2026). A repeated mistake becomes a line in the map or a tool the agent can run (Hashimoto, 5 February 2026). Boundary and taste checks are on from the start. Human merge friction is what a high-throughput repository may remove later (OpenAI). Augment’s order, “constraints, then feedback, then gates,” is that vendor’s arrangement. This document does not use it as the adoption sequence. The linter is both the constraint and the feedback, and it is present from the first commit.

Over-constraining is a real failure. Google’s continuous-integration chapter (*Software Engineering at Google*, chapter 23) puts a check on the blocking path when it is fast and reliable. A flaky or slow check stays off that path. Start with the rows marked **start**, watch false positives, then add the rest.

“Corrections are cheap, and waiting is expensive” is OpenAI’s policy for waiting on a human, or on an indefinite flake stall, after mechanical taste checks already exist and agent throughput exceeds human attention. The same post says that policy would be irresponsible at low throughput. It does not call the taste checks warnings. Augment’s phrase “hard CI failures, not warnings” is not in the OpenAI post. Those checks block inside the agent’s loop because the error text says how to comply.

G3, the evidence gate, stays under both routing postures. Whether a person also reads the diff is the constitution’s choice in [§5](#5-roles). A red invariant check stays blocking either way. See [§14](#14-cicd-shape). Lopopolo’s “corrections are cheap” sentence is about waiting on a person, or on an indefinite flake stall, after mechanical checks already exist and throughput is high. It does not say a red check may land, and it publishes no escape rate that would tell a team when to switch postures.

### Constraint layer (feedforward)

These guides are always available before an agent writes, and they do not block. The block is a check the runtime can fail.

- map file tells the agent where the constitution and the current spec are
- allowed paths for this slice
- “ask first” list: new dependency, new stored field, new entry point, retry policy
- “never” list: weaken an oracle, invent a second auth path, commit a secret, touch unrelated modules

### Feedback layer

The implementer can run these checks and read a remedy in the failure text. That remedy has to say what to do instead.

- format and lint, with inline suppression off
- structural fitness, as in [the metric gates](#metric-gates): dependency direction, the complexity score the constitution named, mutation on the slice decision, and an answer to a reported duplicate. A file-size cap is present only if the constitution names one. The failure text says how to comply.
- unit and contract checks
- the slice journey
- secret scan

Inline suppression is off in CI (`linterOptions.noInlineConfig`, or ESLint’s `--no-inline-config`). ESLint applies a disable comment before a rule that tries to ban disable comments, so that second rule does not close the hole. These notes contain no counted study of agents inserting disable comments. The switch is this document’s rule because the escape hatch is documented. A change to the linter config or the workflow is a risk class in [§5](#5-roles). G4 applies the constitution’s diff-posture.

Error text must say what to do instead. “Violation detected” is not a feedback loop.

The implementer’s green self-check is this feedback loop. It is not yet the merge gate. The merge gate is a required check on the combined result, reported by a pinned app, with the implementer absent from the bypass list (GitHub rulesets).

### Metric gates

The constitution names which complexity score blocks. Until it names one, the check reports cyclomatic complexity, CRAP, and cognitive complexity, and neither score blocks.

Cyclomatic complexity is the path count: one plus the decision points in a function. It is the score that keeps a function easy for an agent to follow. CRAP mixes that count with coverage. The formula is CC² × (1 − coverage)³ + CC (Alberto Savoia, October 2007, <https://www.artima.com/weblogs/viewpost.jsp?thread=215899>). At full coverage the score equals the path count. Below full coverage the score rises, so an untested branch fails even when the path count is small. Cognitive complexity is a different score, for how hard the control flow is for a person to follow (G. Ann Campbell, Sonar, 7 December 2016, <https://www.sonarsource.com/blog/cognitive-complexity-because-testability-understandability/>). Both are valid. The constitution picks one, and names the ceiling, the way it names a latency bar. Spoken CRAP ceilings moved, so this digest installs none: about six paths for an agent-facing function, and CRAP ceilings that include below 6, maybe 8, and later 12 (Robert C. Martin, 19 June 2026, <https://x.com/unclebobmartin/status/2067935532580024741>; 22 July 2026, <https://www.youtube.com/watch?v=RxxxGkFIUJ0>; 19 August 2026, <https://www.youtube.com/watch?v=zcLPGC-tvgk>; 24 September 2026, <https://www.youtube.com/watch?v=5kCISBJwoZo>). A ceiling of 10 stays unused. The 2007 page’s line of 30 is not the ceiling. The check on those numbers is `harness-sources/15-uncle-bob.md`.

These checks run in the implementer’s self-check. Green scores do not prove the spec, and they do not decide the routing posture. Under Read, a person still reads as [§12](#12-review-how-when-by-whom) says. Under Metrics, the person reads the evidence and play-tests the system. The pinned re-run is the merge gate. The implementer is not on the bypass list. The verifier does not treat a green score as the oracle. The steward still does G3, and G4 applies the posture the constitution named.

- **CRAP, when the constitution names that score and a ceiling.** Measured: cyclomatic complexity, coverage, and the score, on each changed function. Blocks at or above that ceiling. The failure names the function, the path count, the coverage, and the score, and tells the agent to add tests for the untested decisions or to split the function. Deleting tests, or suppressing the check, is not a repair.
- **Cognitive complexity, when the constitution names that score and a ceiling.** Measured: the cognitive score on each changed function. Blocks at or above that ceiling. The failure names the function and the score, and tells the agent to flatten the control flow a person has to hold in mind. Deleting tests, or suppressing the check, is not a repair. This digest sets no number for it.
- **Mutation, on the slice’s decision.** Measured: a small semantic edit, such as a flipped sign, comparison, or constant, that the suite still passes. A survivor in the code this slice changed blocks. There is no mutation-score percentage. The failure names the edit and tells the agent to add an assertion whose expected value comes from the accepted spec. Asserting the code’s current return value does not clear it. An equivalent mutant needs a reason, and that reason still does not pass the frozen-example check at G3. A check that only goes red when the feature is deleted still does not prove the oracle. This row is the metric under the oracle rule in [§13](#13-test-how-when-with-what). It is not a new theory of oracles.
- **Dependencies.** Measured: edges against the allow-list, and cycles. A violation or a cycle blocks. The failure names the edge and tells the agent to invert the dependency, insert an interface, or split the module. Adding the edge to the allow-list is a constitution change and its own slice. G4 applies the constitution’s diff-posture. The same run reports instability (fan-out divided by fan-in plus fan-out), abstractness (the share of public vars that are real indirection), and one distance from the main sequence, the absolute value of abstractness plus instability minus one. Instability, abstractness, and distance are reported, not a block. The tool’s default band of 0.3 around that line is not a failure line here. The distance is reported in the evidence. A picture of the layers is not the block.
- **Near-duplicates.** Measured: structurally similar forms. Near-duplicate forms are one of these checks (Robert C. Martin, 25 May 2026, <https://x.com/unclebobmartin/status/2058869482815197320>). Extract the shared behavior, or say why the two forms are not the same rule. The failure names the two sites. A reported pair with no answer fails the self-check. The 0.82 figure is a tool default, not an adopted cutoff.
- **Zero-assertion examples.** An example that asserts nothing fails. Splitting a spec file, beyond an empty example this slice introduced, is gardener work. An illegally nested example fails before the examples run.

### Checks the agent can run

These are implementations of the metric gates, the seam list, the slice cut, and the two review lists. Any tool that emits the score the constitution named can stand in for the checkers below.

- A change-risk score from the path count and coverage: <https://github.com/unclebob/crap4clj>
- A small semantic edit that the suite should kill: <https://github.com/unclebob/clj-mutate>
- Allowed dependency edges, and cycles: <https://github.com/unclebob/dependency-checker>
- Near-duplicate forms: <https://github.com/unclebob/dry4clj>
- An example that asserts nothing: <https://github.com/unclebob/scrap>
- Illegal nesting in the examples, before they run: <https://github.com/unclebob/speclj-structure-check>
- Instructions for the seam list, the slice cut, and the two review lists: <https://github.com/mattpocock/skills>

### Enforcement layer

Each gate names an owner and a consequence, and a hard gate stops the next step until it passes. The implementer cannot waive a hard gate or sit on the bypass list.

| Id | Gate | Start | Hard? | Owner | Consequence |
| --- | --- | --- | --- | --- | --- |
| G1 | Intent complete | yes | hard | Steward | No research beyond questions |
| G2 | Spec schema valid, guesses resolved or accepted, examples and seams frozen | yes | hard | Steward | No plan |
| GP | Plan reuses mechanisms, paths named, feature slice is a tracer bullet or a named exception | yes | hard | Planner output, steward if a new mechanism or the cut is unclear | No implementation |
| GO | Orient: core journey green | yes | hard while the command stays fast and deterministic | Implementer, tool | Repair before new work |
| GI | Implementer self-check commands green | yes | hard as feedback; the merge gate is the pinned re-run | Tool | No verify |
| GM | Metric gates: dependency violations and cycles, mutation survivors on the slice decision, the complexity score the constitution named once it names a ceiling, and an answer to a reported duplicate | yes | hard for the first three once they apply; a duplicate needs an answer, not a similarity cutoff | Tool; implementer repairs | No verify |
| GV | Verifier, each done-means line evidenced against frozen examples | yes | hard | Verifier | Back to implementer, two send-backs |
| GL | Lens findings triaged | after GV is clean | soft for low, hard for high | Auditor | High findings block |
| GA | Auditor checklist re-run | yes | hard | Auditor | Reject with evidence |
| G3 | Frozen example fails a small rule change | yes | hard | Steward | No merge |
| G4 | Routing posture | yes | hard | Steward | Apply diff-posture. Read: a person other than the author reads the change, including the tests. Metrics: the person reads the evidence and does not read the agent diff. Unset does not merge |
| GS | Secret, dependency, auth cases on the triggers in §10 | yes, when triggered | hard | Tool + verifier | No merge |
| GC | Capacity and efficiency budgets | M6 and hot paths | hard at M7, soft before | Tool | No promote |
| GR | Release rehearsal and rollback | M7 | hard | Release runner + steward | No promote |
| GG | Doc and feature-list schema | yes | hard | Tool | No session start |

A journey that has started to flake comes off the hard path that day. Google’s rule is that a flaky check teaches people to ignore red. Quarantine it and fix it (Fowler).

**High finding:** cross-actor access, data loss, oracle weakened, unbounded read, secret, pipeline permission widened. **Low finding:** naming, local structure, missing commentary. Pre-existing nits become gardener work. New complexity this slice added blocks unless the steward defers it with a tracked slice. That split follows Google’s review standard: a nit does not block; complexity the author just introduced does not wait for a later change.

The implementer cannot waive a hard gate, forge a required status, or sit on the bypass list. A downed check pauses the gate. A named steward may bypass a stuck combined-result queue on a recorded pull request, and may not use that bypass to skip a secret, an auth case, an oracle failure, a dependency violation, a cycle, or a mutation survivor on the slice decision. The hosts document an audited bypass for a privileged human. The limit to a stuck queue, and the ban on using it for those failures, are this document’s rule.

### Audit checklist (fresh run, latest tree)

The auditor fills pass or fail for every row on the latest tree. `AUDIT_PASS` is a status, not a claim that no defect exists.

```text
Requirements        pass/fail
Scope paths         pass/fail
Architecture edges  pass/fail
Metric gates        pass/fail
Error behavior      pass/fail
Security cases      pass/fail
Edge cases          pass/fail
Oracles intact      pass/fail
Tests               pass/fail
Boot                pass/fail
Prior journeys      pass/fail
Budgets (if due)    pass/fail
Docs touched        pass/fail
Rollback named      pass/fail
```

---

## 12. Review: how, when, by whom

Review is several different activities. Collapsing them into “someone reads the diff at the end” is how intent errors survive.

| When | Question | Who | Reads the diff? | Output |
| --- | --- | --- | --- | --- |
| After the glimpse | Worth doing? | Steward | No | G1 |
| After the spec | Is done-means observable, are seams named, and are guesses labeled? | Steward | No | G2 |
| After the plan | Any second mechanism, one-layer feature cut, or scope creep? | Planner, then steward only if the plan asks for a new mechanism | No | GP |
| During implementation | Did the commands go green? | Implementer + tools | No | GI |
| After the slice | Does behavior match the frozen spec? | Verifier, fresh | The frozen diff, and re-runs the journey | GV |
| After GV is clean | What did specialized lenses see? | Lens reviewers, parallel | Same frozen diff | Findings |
| After lenses | Did the checklist pass on the latest tree? | Auditor | No, unless investigating a fail | GA |
| Before merge | Is the evidence real, and what would it miss? | Steward | Under Read: risk class, unnamed blind spot, or the sample. Under Metrics: no. The person reads the evidence | G3, G4 |
| On a cadence | Is the codebase drifting? | Gardener | Its own small diffs | Cleanup slices |
| On a model change, and every six months if the model name has not changed | Which lines were compensating for an older model? | Steward | No. The gate each line names, with that line removed | A decision-log row. A repeated miss is written back |
| Before promote | Is this candidate fit to expose? | Steward | Under Read: risk classes and the rehearsal record. Under Metrics: the evidence, the rehearsal, and a play-test | GR |

**Who does not review.** The implementer does not approve. An agent’s approval never counts as a person’s (Claude Code team; AISVS AC.4.1 and AC.8.1). The verifier does not edit product code. The auditor does not fix and then pass. Lenses read one frozen diff. Capture it once. These notes do not include a measured cost for letting each lens re-read the diff. The capture is this document’s rule.

The verifier re-runs the journey. Claude’s managed code review reads the diff and does not build or run the code. Those are different jobs. A green journey misses races and tautological tests. A diff-only model review misses the user-visible path.

The lens report keeps a standards list and a spec list on one pinned diff. It does not rank them into one winner. Each finding cites the constitution line or the spec line. With no spec, the spec list says so. A finding is a hypothesis until the citation is checked. The judgement list is not re-run until it is empty. High findings still block. A silent judgement axis is not required (Matt Pocock, 7 July 2026, <https://www.aihero.dev/skills-code-review>). The lenses still wait until GV is clean.

A repeated mechanical miss becomes a check the implementer can run. A judgement stays in the file the reviewer reads. The always-loaded map gains a pointer to that file (Matt Pocock, 5 October 2026, <https://github.com/mattpocock/skills/blob/main/skills/engineering/retro/SKILL.md>).

**Under Read, what a person reads.**

Google’s bar, for any change a person is reviewing: someone other than the author reads the change, including the tests, because tests do not test themselves. Start with the spec delta and with what the evidence would miss. Then read the change, including the tests. Style narration is not the job. Stopping at the risky hunk leaves the rest of that review undone.

The best single LLM review pass on SWR-Bench is F1 19.38%. A clean lens report does not prove the spec. Giving the verifier the spec supplies an intent the benchmark’s reviewers often lacked. It does not turn a silent verifier into a human reading of the tests.

**When G4 applies the posture.**

Never skip G1, G2, or G3. GV and GA are this document’s verifier and auditor. Anthropic’s November harness has neither.

Under Read, a complete evidence record on a path that misses the risk list still leaves the diff to be read, including the tests, when nobody can name what the checks would miss. That sentence is the Claude Code team’s (15 October 2026). Evidence-only merges are sampled. Anthropic’s security account of its own development lifecycle already reviews a risk-weighted sample of automated approvals. GitLab’s playbook says a checkpoint nobody exercises manufactures confidence.

Under Metrics, the person does not read the agent diff. They set intent and spec, read the evidence (commands, frozen examples, metric results), and manage from those scores. A play-test of the running system still happens. A metric gauntlet once passed and a later play-test still found ten bugs, half of them specification misses (Robert C. Martin, 6 June 2026, <https://x.com/unclebobmartin/status/2063344635150553131>).

Green metrics do not by themselves prove the spec. The agent does not approve or merge its own change in either posture. An agent’s approval never counts as a person’s. Until the constitution names a posture, G4 does not merge.

Skip lenses only when GV already failed, so a known-red diff does not pay for a wide read.

OpenAI’s end state, humans not required on a given pull request, stays the outcome they say not to copy until the repository has had the same investment. This file does not install that end state. The constitution names Read or Metrics, and in both the agent does not approve or merge its own change.

---

## 13. Test: how, when, with what

Tests are evidence producers. A test the implementer can quietly weaken is not a gate.

### What exists before code

Accepted given/when/then examples in the spec, with expected values, are the oracles, and they are frozen at G2, before implementation. The spec author writes them.

Acceptance text that the agent wrote and then checked drifted (Robert C. Martin, 24 September 2026, <https://www.youtube.com/watch?v=5kCISBJwoZo>). Later tests may be added. A new expected value has to come from the accepted spec. It must not replace an accepted expected value. Accepted examples may not be deleted or loosened except by a steward-approved spec change, which is its own slice.

On runs where the generated code was already faulty, tests written afterward detected the fault about 14% of the time. Tests from the task text alone, without the code, detected it 25% of the time (arXiv:2607.05139). Both rates are low. The result is a reason to freeze the example before the implementation exists. It is not a claim that test-first generation is sufficient.

### Ladder

The ladder runs from an example a person can reject, through the journey on the booted app, to a rehearsed rollback. Each level names what it proves, who writes it, and when it runs.

| Level | Proves | Written by | When | Run |
| --- | --- | --- | --- | --- |
| Example | The decision, in words a person can reject | Spec author | Before code | Human at G2 |
| Unit | One rule, including a refusal | Implementer, expected value from the spec | During the slice | Every self-check and CI |
| Service / contract | A boundary shape still holds for neighbors | Implementer, unless a neighbor owns the examples | Slice touches a boundary | CI |
| Journey | The feature steps, through the real boot path | Implementer drives; verifier re-runs | End of slice | Orient of the next session, and CI |
| Independent cases | Cases from the spec the author did not encode: other actor, replay, malformed, timeout | Verifier, expected value from the spec | After the slice | Gate GV |
| Regression | Previously green features | Tool | Every session start and CI | GO |
| Fault | Slow or dead dependency, duplicate submit | Implementer | M3, M5 | CI for those modules |
| Security | Cross-actor on a critical path, secret scan, named dependency | Tool + verifier | The triggers in §10 | GS |
| Capacity | The named bar under the stated load | Release/quality slice | M6, hot paths | GC |
| Rollback rehearsal | Promote and return | Release runner | M7 | GR |

A boundary check the implementer writes against the response it just coded is a service test. It is a contract test when the other side owns the examples and this slice cannot regenerate them (Robinson’s consumer-driven contracts; Pact’s consumer-test rule).

### Rules that keep the ladder honest

A passing test counts when the expected value was frozen before the code, the assertion does not restate the code’s own calculation, and the journey drives the booted app. The implementer has no write access to the oracle.

- Prefer behavior over internal call order. A test that only mocks the function under test does not count as a journey.
- No bare sleep as a wait for an asynchronous result, no random thresholds, no time-dependent assertions. A test that passes and fails with no meaningful change comes out of the blocking pipeline and is fixed (Fowler, 14 April 2011).
- Failure output names the feature id and the expected observable result.
- Coverage is a map of unexecuted lines in the slice, not a goal. A global percentage is not the gate (Fowler). A low figure is still evidence of code the suite never runs (Google’s 2020 testing post). This document treats a risk-class branch with no spec-derived case as a hole. CRAP, in [§11](#metric-gates), is one of the two complexity scores. It mixes that map with the path count. It is not a second percentage, and it blocks only when the constitution names it and a ceiling.
- Do not give the implementer write access to the oracle. Prompts do not stop test edits. Read-only tests stop edits and do not stop special-casing (ImpossibleBench, arXiv:2510.20270).
- The expected values are frozen before implementation. A small change to the rule, with those examples still in force, must fail. Scope that check to the slice’s decision. Read what survived. Do not install a repository-wide mutation percentage. A survivor in the code this slice changed blocks. An equivalent mutant needs a reason, and that reason still does not pass this check. Asserting the code’s current return value does not clear the survivor (Robert C. Martin, “Mutation Testing,” 10 June 2016, <https://blog.cleancoder.com/uncle-bob/2016/06/10/MutationTesting.html>; 24 September 2026, <https://www.youtube.com/watch?v=5kCISBJwoZo>). With buggy code in front of the generator, at least one correct strong test for the bug scenario fell from 63% to 30%, and including the JavaDoc did not change the drop (DOI 10.1145/3832783.3844564).
- A check that goes red when the feature is deleted does not prove the oracle. A test that asserts the code’s current behavior fails that deletion too. If the verifier cannot show the frozen example failing under a small rule change, G3 fails.
- Tests for this slice are written at seams the spec named. Each seam records the public boundary, what it catches, and what it misses. The steward accepts that list at G2. At an agreed seam, write one failing test whose expected value is the frozen example, then the code that passes it. Do not write the rest of the suite first. An assertion that recomputes the expected value the way the code does is tautological and does not count. Refactoring is not this cycle (Matt Pocock, `tdd` skill, read 27 October 2026, <https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md>).
- A single seam is not the list. A unit-only seam can stay green on a broken journey, so the list keeps a journey seam and one refusal example, which may be a case at that seam (Matt Pocock, 27 April 2026, <https://www.aihero.dev/skills-to-spec>; Anthropic, November 2025).
- An example that asserts nothing fails. An illegally nested example fails before the examples run.
- End-to-end means the user-visible path in the booted app, not a request fired at an internal function. Anthropic’s result was that unit tests and `curl` still left features broken until the agent drove the product as a user. In that harness the same coding agent still sets the pass flag. This document’s verifier is a separate role. Anthropic does not require that split. Browser or UI automation has blind spots; record them as limitations rather than pretending the journey saw everything.

### When not to add a test

Do not add a test for a non-goal or a framework tour. Do not duplicate the journey at unit level for every branch the journey already covers, unless the branch is a risk class that must fail in isolation.

---

## 14. CI/CD shape

The pipeline is a product slice (M7), built with the same loop, after the walking skeleton can boot, deploy, and run its end-to-end check. OpenAI’s first commit already included CI configuration; holding a promotion rehearsal until M7 is this document’s choice, and the boot-and-test path is part of the skeleton.

```text
change
  → secret push protection at git push
  → schema of docs and features.json
  → format, lint, structural fitness (the metric gates in §11)
  → unit + service/contract
  → boot isolated instance
  → core journeys + slice journey
  → secret scan (second look) and dependency review
  → [if triggered] fault, security cases, capacity
  → evidence bundle stored with the commit
  → audit status
  → G3/G4
  → merge to the integration line
  → release candidate
  → rehearsal: promote, run core journey, roll back, run core journey
  → steward approves the production environment
  → canary, then widen
```

Secret push protection runs at `git push`. The later scan stays because a very large push can skip the forge check, as GitLab documents. Dependency review belongs on the pull request. A report filed under `docs/evidence/` is not the merge block, because the authoring agent can write that file.

Isolated, for the inner-loop boot, means the run cannot see another slice’s data or another run’s metrics. OpenAI’s worktree-local boot and ephemeral observability is that pattern. The bytes you promote are a separate question: provenance from the builder, not the worktree.

**Promote** is a human action in this digest. The agent prepares the candidate and the rehearsal record. It does not flip production traffic. The rehearsal runs in a non-production environment and proves the path exists. The first production exposure is a partial canary against a control, one at a time. A bad signal rolls back before diagnosis (Google SRE workbook, canarying releases). Watching budgets after a full promote is a later stage. The rollback path is the one that was rehearsed.

**After a bad canary.** Roll back by the rehearsed path and open a repair slice. It does not open a drive-by rewrite of unrelated modules.

**What blocks.** A red invariant check stays blocking even after human review is dropped. OpenAI’s low-friction merge removes required human review and indefinite flake stalls. The post does not say a red check may land. Flakes in that post get a follow-up run. A consistently red check is not described as mergeable.

GitHub rulesets are the merge block this document uses in substance. A required status check must pass. The rule names the app allowed to report the check. The coding agent is not on the bypass list. An agent identity that can set a commit status can paint a check green if the rule accepts any source.

SLSA v1.2, source track: on the protected branch, history stays append-only, and Source Level 4 requires two trusted persons on the final revision. A coding agent is not a trusted person. The spec’s path for an unreviewed bot is an explicit trusted-robot exception, illustrated with import bots and Dependabot. This document does not grant that exception at the start. Two agent approvals are not Source Level 4.

DORA’s report of 23 September 2025 associates higher AI adoption with higher software delivery throughput and with higher delivery instability. Fixing faster after production still leaves the extra instability harmful. The report does not find that extra throughput cancels the harm to product performance and burnout. The 22 October 2024 survey estimated about a 7.2% reduction in delivery stability and about a 1.5% reduction in throughput per 25% higher AI adoption. The 2025 report does not publish a new 7.2% figure, and the throughput association turns positive. The metrics page is still titled around the four keys. The current set is five: change lead time, deployment frequency, failed deployment recovery time, change fail rate, and deployment rework rate. A red check that stops a change before production is the pipeline working, not a change failure.

Keeping G3 under both postures is this digest’s reading of the evidence. It is stronger than dropping every human wait. What the OpenAI post ties to throughput is dropping the human wait, not landing a red invariant.

---

## 15. Worked example — Desk

Desk is one illustration of the digest, not the digest itself. The constitution in this pass names Read, and it reports CRAP without a ceiling, so a reader can see one path. Metrics, and cognitive complexity, stay available. Later modules inherit the intent below, including the non-goals.

Intent (G1), written small on purpose:

> A signed-in member can submit an item, claim an open item, and close an item they hold. Another member cannot claim or close it. A status read returns state and holder, not private notes. Out of scope: payments, notifications, file attachments, multi-organization hierarchies.

Non-goals are part of the intent so later agents do not “helpfully” add them. That hard stop is this document’s rule.

### M0 — Constitution (no product behavior)

The steward locks the single paths, the dependency edges, the data classes, and the still-empty budgets. Storage and delivery are chosen once, and a feature session does not reopen that choice.

Steward accepts, among other bars:

- one storage path, one authorization path, one error shape, one log shape
- dependency edges: domain rules do not depend on the delivery mechanism; delivery depends on an application entry; storage is behind an interface
- data classes, in this document’s Desk vocabulary: item body is account-class; status and holder id are readable by any member; secrets never stored in the item
- budgets, empty until filled with a real bar: for the status read, the request, the proportion, the threshold, the measurement point, and the window; submit performs a bounded number of storage round-trips; list page size, payload, timeout, and rate limit are bounded, with no millisecond set in this file
- risk classes as in [§5](#5-roles)
- complexity score, for this illustration only: CRAP, with no ceiling, so the check reports and does not block. Cognitive complexity is the other valid score
- routing posture, for this illustration only: Read. The diff reads below are what Read does on this pass. Metrics remains available

Researcher may list options for storage and delivery. Steward picks. The choice is an assumption marked `confirmed`. Agents do not re-litigate it inside a feature.

### M1 — Walking skeleton

Submit, read-back, and the empty-title refusal run on a deployed boot. This sample’s constitution chose Read, so a person other than the author reads the diff, including the tests. Metrics stays available. Grade C is allowed at this module.

Feature F1: a member submits an item and can read its status back as open, with no holder. Feature F2: an empty title is refused.

Spec freezes those two examples, including the expected refusal. Plan says the slice may create the boot script, the first entry points, and the first persistence, and may not add accounts beyond a single hard-coded member if identity is M2 — **or** the plan flags that a fake actor is a temporary seam and lists the deletion of that seam as the first task of M2. The seam is written into `decisions.md` so a later agent does not treat the fake actor as architecture. The slice is deployed and the journey runs. A stub that cannot deploy is not yet the skeleton on Freeman and Pryce’s definition.

Verifier drives submit and read in the booted app. Auditor runs the checklist. G4 applies Read, the posture this sample’s constitution chose, so a person other than the author reads the diff, including the tests, because the pipeline stub and the first data shape are risk classes. Metrics stays available. Grade target: C on this document’s scale. That is allowed. M7 is not.

### M2 — Identity and access

Sign-in makes two members distinct, refuses a missing actor, and deletes the temporary actor seam. This sample’s constitution chose Read, so a person other than the author reads every diff in this module, including the tests. Metrics stays available. The grade is B or better before claim and close.

Features: sign-in, refuse a missing actor, two members are distinct. The temporary actor seam is removed in this module, not left behind.

G4 applies Read, the posture this sample’s constitution chose, so a person other than the author reads every slice here, including the tests. Metrics stays available. Independent cases: member B cannot present A’s session; a request with no actor is refused on submit. Verifier checks the seam is gone by searching for the temporary path and by running F1 under a real session. Grade target: B or better on this document’s scale before M3.

### M3 — Claim and close

Claim and close create a single holder, refuse everyone else, and keep a replay from adding a second holder. The trace has to show a bounded number of writes, and a storage timeout returns the known error.

Features: claim an open item; second member is refused; holder closes; non-holder cannot close; closing an already closed item is a no-op or a named error (the spec picks one); replaying claim does not create two holders.

This is the module where efficiency and reliability start. The trace must show a bounded number of writes. Fault case: storage timeout returns the known error and does not report success.

Lenses run after verify is clean. The security lens and the test lens are the ones most likely to find a real gap (authorization checked only in the client-side flow; a test that asserts the mock was called, or that restates the current return value).

### M4 — List and filter

List and filter honor page size on the same authorization path and the same store as the writes. Private notes stay out of the list, the page cannot be set to “all”, and the load scenario waits for M6.

Features: a member lists open items, page size honored, filter by state. Plan-alignment: list uses the same authorization path; it does not open a second store.

Fitness against the whole: F1–F3 journeys still pass; a list cannot return private notes; page size cannot be set to “all.” Capacity here is the static bound (one page, many items). The load scenario is M6.

### M5 — Operability

Correlation, a structured refusal, an audit record, and a submit limit are specified as results a person can observe. The window and the limit are filled in the spec before any code, and this file does not pick the integers.

Features are mostly invisible: correlation id on submit, structured refusal on bad input, audit record of claim and close, limit on submit rate. Spec done-means are still observable (“the refusal body contains the feature’s error code and not the stack,” “the N+1th submit inside the window is refused”). The window and the N are filled in the spec before implementation. This file does not pick the integers.

### M6 — Quality attributes

No new user feature. The slice’s product is evidence.

- The chosen ASVS level, once, with pass or fail per requirement. Every-change secret, dependency, and validation checks have already been running. Cross-actor claim, cross-actor close, and cross-actor read of notes are part of this release set.
- Efficiency: round-trip budget on submit and claim, from the trace.
- Traffic: the constitution’s latency bar, if it now names the five fields, against status read and against claim (two members, one item). If the five fields are still empty, the latency gate stays off. Page size, payload, timeout, and rate limit still fail closed.
- Results land in `docs/evidence/M6/`. A miss opens a repair slice. It does not get waived inside the same session.

Grade moves to A or B on this document’s scale, or promotion is forbidden.

### M7 — Release candidate

The pipeline is the one in [§14](#14-cicd-shape), rehearsed by promoting a candidate in a non-production environment, running F1 and the claim journey, rolling back, and running them again on the previous candidate. The steward approves the production environment, the first exposure is a partial canary, and the agent does not approve that environment or merge its own release change.

### What a single M3 session looks like

One claim session runs from a green orient to a merge by a forge identity other than the implementer. The implementer leaves `passes` unset. This sample’s constitution chose Read, so the steward reads the diff, including the tests, because authorization is a risk class. A constitution that chose Metrics would read the evidence and play-test the running system, and would leave the agent diff unread. The third rejection escalates.

1. Orient: read progress and git history, boot, F1 and F2 green, pick “claim an open item.”
2. Spec already accepted at G2, examples and seams frozen. If the conflict rule is still a `guess`, stop and ask.
3. Plan: which files, what blocks this slice, reuse of the M2 allow decision, tests at the seams the spec named, nothing else.
4. Implement the claim path and the two-member refusal. One failing test at the agreed seam, then the code. Expected values come from the spec. Do not propose a different approach.
5. Self-check: commands, journey, prior journeys.
6. Commit, progress note. Do not set `passes`. Anthropic’s November coding agent would set it after its own UI check. This document’s implementer does not.
7. Fresh verifier re-runs the steps, adds the replay case when the spec’s expected value requires it, and sets `passes` only when the frozen examples hold. A deletion-red check is not that evidence.
8. Lenses on that commit. Auditor re-runs the checklist.
9. Steward reads the evidence and names what the checks would miss. This sample’s constitution chose Read, so the steward also reads the change, including the tests, because authorization is a risk class (G4). Metrics stays available. Merge is a forge identity that is not the implementer.

If the verifier finds that member B can claim, the implementer gets that failure and repairs. Verify or audit may send the slice back twice. The third rejection escalates to the steward with the spec, the diff, and the failures. Two send-backs is this document’s cap.

### Cadence after the first promotion

Gardener sessions, separate from features: scan for a second logging shape, a module graded below B on this document’s scale, docs that disagree with the code, and patterns already banned, and each cleanup is a slice with the same gates, and behavior must stay green. A cleanup that changes behavior is a feature and goes through G2 again; OpenAI automerges most small cleanup pull requests, and this gardener does not, which is this document’s stricter rule.

---

## 16. Stop, retry, escalate

Every row binds the session to a single next action, and the third rejection leaves the loop for the steward. The next role does not invent a missing field.

| Condition | Action |
| --- | --- |
| Required artifact field missing | Stop. Do not let the next role improvise. The field rule is this document’s. |
| Core journey red at orient | Repair only. No new feature in that session. Mark the feature failing if it had been passing (Anthropic’s coding prompt). |
| Self-check red | Implementer may retry inside the slice. |
| Verifier or auditor reject | Return the failure to the implementer. At most two send-backs. That cap is this document’s. |
| Third reject | Escalate to the steward with the spec, the diff, and the failures. The closer published stops are three attempts, then raise, or three review rounds, then reconsider the approach. |
| Agent wants a new dependency, field, or mechanism | Stop at “ask first.” |
| Agent edits an oracle or a feature’s steps | Invalid on this document’s rule. Revert. Anthropic’s control is the prompt, plus JSON because Markdown was rewritten. Read-only tests stop the edit and do not stop special-casing. |
| Tooling for the gate is down | Pause the gate. The implementer does not skip it and does not weaken it. |
| Scope grows mid-slice | Cut back to the feature id, or stop for a new spec. |

The question at escalation is “what capability or rule is missing?”, which is Lopopolo’s question when the agent cannot proceed. Asking it because a slice hit this document’s cap is this document’s trigger. OpenAI keeps the review loop going until agent reviewers are satisfied, and does not use a third-reject handoff. The answer is written into the constitution or a check, by an agent, in a follow-up slice the steward accepts.

---

## 17. Prompt contracts

Use these as the session prompts, and fill the slots. Do not merge two contracts into one session, except where a contract says the same role continues.

Shared preamble, in every agent prompt:

```text
You are the <ROLE> for one slice of this repository.
Read: AGENTS.md, docs/constitution.md, docs/progress.md,
the slice spec if it exists, docs/features.json, and git history.
Workspace writes stay inside this repository.
You may not delete or rewrite accepted acceptance examples.
You may not set passes to true unless you are the verifier under P4.
If a required input is missing, stop and name the field.
When you finish, append docs/progress.md and list artifacts written.
```

### P0 — Researcher (glimpse)

The researcher lists decisions and questions for the steward, and stops before choosing a technology or inventing a requirement.

```text
Purpose: surface decisions, not solutions.
Input: the glimpse paragraph.
Do: list actors, outcomes, non-goals, dangerous ambiguities,
    and questions the steward must answer.
Do not: choose a technology, write code, or invent requirements
    to fill gaps. Mark unknowns as questions.
Output: docs/intent-questions.md with a one-line headline.
Gate: G1 will be answered by the steward, not by you.
```

### P1 — Spec author (constitution or slice)

The spec author writes observable done-means, names every seam, and fixes expected values for G2. File plans and code belong to later sessions.

```text
Purpose: write a spec whose every done-means line is observable.
Input: accepted intent, questions already answered, constitution.
Do: fill every required spec field. Mark each assumption
    confirmed or guess. Include one refusal example.
    Name each test seam: the public boundary, what it catches,
    and what it misses. The journey is one of them.
    Fix expected values in the examples. They freeze at G2
    with the seam list.
    When the artifact is the constitution, name complexity-score
    (crap or cognitive) and diff-posture (read or metrics), or leave
    a field unset. An unset score reports both and blocks on neither.
    An unset posture means G4 does not merge.
Do not: plan files, name an implementation trick, or start coding.
    Do not write a decision the steward did not make.
    Do not skip the spec because the change is small.
Output: the spec path. Headline states the outcome in one line.
Gate: stop if any guess would change authorization, data class,
    or a budget. Those go back to the steward before G2.
```

### P2 — Planner

The planner names steps, the reused mechanism, the blockers, and the cut, so another session can execute the plan. A second mechanism, or a feature slice in a single layer, stops the plan.

```text
Purpose: a plan another session can execute without you.
Input: accepted spec.
Do: name steps, allowed paths, the existing mechanism reused
    for auth, storage, errors, and logs, the checks to add,
    the rollback commit strategy, what blocks this slice,
    and the cut. A feature slice names the user-visible path
    through the layers it touches. A constitution, evidence,
    or release slice names that exception. A wide mechanical
    change names expand, then migrate, then contract.
Do not: write product code. Do not introduce a second mechanism.
    If reuse is impossible, stop and say so.
    A feature slice that is only one layer fails GP.
Output: docs/exec-plans/active/<slice>.md
Gate: GP. A new mechanism is an escalation, not a step.
```

### P3 — Implementer

The implementer makes one red feature work and leaves a bootable tree, with `passes` still false. A red core journey at the start is repaired, and the new feature waits.

```text
Purpose: make exactly one red feature work, and leave the tree bootable.
Input: accepted spec, accepted plan, features.json, progress log,
    and git history. If verify or audit rejected this feature,
    the input includes that failure.
Do: orient first (read progress and git history, boot, core journey).
    If the core journey fails, fix that and stop. Otherwise implement
    only this feature id, inside allowed paths. At each seam the spec
    accepted, write one failing test whose expected value is the
    frozen example, then the code that passes it. Run the fast local
    checks during the slice, including the metric gates, and the full
    suite once at the end. If complexity-score is unset, report both
    scores and do not treat either as a block. If you were sent back, repair from the
    failure you were given. Commit.
Do not: set passes to true. Do not edit accepted examples.
    Do not start a second feature. Do not add a dependency
    the plan did not name. Do not write the rest of the suite first.
    Do not recompute an expected value the way the code does.
    Do not propose a different approach. If the accepted spec
    cannot be built as written, stop. Do not treat a withheld
    build transcript as your input. That withholding is the
    verifier’s view of your reasoning, not your repair input.
    A review in this session is not the pass.
Output: commit, progress note, commands and results.
Gate: GI. Red self-check means you repair, you do not hand off.
    Anthropic’s November 2025 coding agent sets passes after its own
    UI check. This document leaves that flag to the verifier.
```

### P4 — Verifier

The verifier tries to show the slice misses the spec, without the author’s reasoning and without editing product code. `passes` becomes true only when every frozen example holds.

```text
Purpose: try to show the slice does not meet the spec.
Input: spec, features.json, frozen diff, commit, commands, and any
    failure already recorded. You do not receive the author’s
    reasoning from the build. You do receive the spec, the diff,
    and the failure if there is one.
Do: for each done-means line, run the journey or mark it unchecked.
    Add a case only when its expected value comes from the accepted
    spec (other actor, replay, malformed input), not from the code’s
    current output.
    The accepted examples were frozen before implementation.
    A small change to the rule, with those examples still in force,
    must fail. A check that only goes red when the feature is deleted
    does not prove the oracle. An assertion that recomputes the
    expected value the way the code does does not prove it either.
    Set passes true only if every step holds against the frozen examples.
Do not: edit product code. Do not loosen an example to make it pass.
    Do not treat the author’s reasoning as the evidence.
Output: docs/evidence/<slice>/verify.md and the pass flag.
Gate: GV. Any unchecked done-means line is a reject.
    Anthropic’s November 2025 coding agent verifies and sets passes
    itself. This document keeps you as a separate role.
```

### P5 — Lens (one lens per session, same diff)

One lens reads the same frozen diff and reports findings that cite the constitution or the spec, in separate lists. It does not edit code, and a finding has to point at a hunk.

```text
Purpose: <correctness | boundaries | security | tests | simplicity | operability>
Input: frozen diff and spec. You are read-only.
Do: report findings with file, why it fails the constitution
    or the spec, and how you would confirm it. Severity high or low.
    Keep a standards finding and a spec finding in separate lists.
    Cite the constitution line or the spec line. Do not rank
    the lists into one winner. A finding is a hypothesis until
    the citation is checked.
Do not: fix code. Do not restyle for preference.
    No finding if you cannot point at a hunk.
    Do not invent a requirement the spec does not state.
Output: a finding list. Empty is allowed.
Gate: high findings block. Low findings go to the gardener queue
    unless this slice introduced the complexity, in which case they block
    until the steward defers them with a tracked slice.
```

### P6 — Auditor

The auditor re-runs the checklist on the latest tree and records pass or fail for each row it can run itself. A reject goes back to the implementer with the failing rows, and nothing in the tree is edited.

```text
Purpose: re-run the checklist on the latest tree.
Input: spec, checklist, commands. Read-only.
Do: run boot, prior journeys, this journey, fitness checks,
    and the triggered attribute checks. Fill pass/fail per row.
Do not: edit anything. Do not inherit the verifier’s word for a row
    you can run yourself.
Output: AUDIT_PASS or AUDIT_REJECT plus the row evidence.
Gate: GA. A reject returns to the implementer with the failing rows
    and the failure output.
```

### P7 — Gardener

The gardener removes one drifted pattern in a slice whose done-means is that behavior stayed the same. A behavior change aborts the cleanup.

```text
Purpose: remove one drifted pattern without changing behavior.
Input: quality-score.md and one assigned deviation.
Do: open a slice whose done-means is “behavior unchanged,
    pattern gone.” Run the full journey set.
Do not: bundle a feature. Do not change a budget or an API.
Output: a normal slice evidence record.
Gate: same as any slice. A behavior change aborts the cleanup.
    OpenAI automerges most small cleanup pull requests.
    This document does not.
```

### P8 — Release runner

The release runner builds a candidate and rehearses promote and rollback off production. The steward approves the production environment, and this session does not move production traffic.

```text
Purpose: produce a candidate and a rehearsal record.
Input: integration line, constitution budgets, core feature ids.
Do: run the pipeline, store the evidence bundle, promote in the
    non-production environment, run the core journeys, roll back,
    run them again. Prepare the canary comparison. Do not move
    production traffic.
Do not: approve the production environment. Do not skip a red hard gate.
    Do not merge the integration line if you are the implementer identity.
Output: docs/evidence/release/<id>.md
Gate: GR. The steward approves the production environment.
```

---

## 18. How to know the harness is working

Measure the harness, not the volume of code. The rows below are this document’s scoreboard.

They are not DORA’s five metrics. The guide still titled around the four keys defines five: change lead time, deployment frequency, failed deployment recovery time, change fail rate, and deployment rework rate. Deployment rework rate is the share of deployments that were unplanned production fixes. The rework row here is two-week line churn.

| Signal | Healthy direction | What the number is |
| --- | --- | --- |
| Slice resolution | Share of slices that reach AUDIT_PASS without a steward rewrite | This document’s signal. SWE-bench “resolved” means `FAIL_TO_PASS` and `PASS_TO_PASS`, not that a maintainer would merge. METR, 10 March 2026: automated scores sit 24.2 percentage points above maintainer merge |
| Rework | Share of a slice’s lines replaced within two weeks | GitClear churn: 3.05% of changed lines in 2020, 5.67% in 2024, not labeled by author. Not DORA’s deployment rework rate |
| Escape | Defects found after production exposure, tagged by module | A catch before production is the pipeline working (DORA 2024). The 7.2% figure is not a per-module escape count |
| Verification tax | Steward time per slice, mostly at G2 rather than in diff reading | This document’s signal. These notes do not include a primary that times a steward at a spec gate |
| Oracle integrity | Count of attempts to weaken an accepted example (target: caught, not merged) | This document’s signal |
| False-green | Verifier rejects where the implementer’s tests were already green | This document’s signal |

Do not use lines of code, number of prompts, or “AI suggestions accepted” as success. OpenAI’s million lines, about 1,500 merged pull requests, and 3.5 pull requests per engineer per day are one team’s anecdote. The post’s scarce resource is human time and attention. The cheap-corrections sentence is limited to that high-throughput setting.

Delivery instability, from the 22 October 2024 survey: an estimated 7.2% reduction in delivery stability and 1.5% reduction in throughput per 25% higher AI adoption, nearly 3,000 respondents. Those are associations, not an experiment. The 23 September 2025 report turns the throughput association positive and leaves the instability association unfavorable. It publishes no new 7.2% figure. In that report’s checks, instability still harms product performance and burnout, and extra throughput does not cancel the harm. The gates exist so a throughput gain does not silently buy that instability.

Review the signals on a cadence. A repeated escape becomes a constraint or a check. A repeated mistake becomes enforceable. Hashimoto’s practice (5 February 2026) is to write it into the repo. A mechanical miss becomes a check, a judgement stays in the file the reviewer reads, and the map gains a pointer. [§12](#12-review-how-when-by-whom) is that rule. A model change is the other review: compensatory lines come out, and the same signals are run again. [§19](#19-evolvability)

---

## 19. Evolvability

The harness stays easy to change when the model changes. A line in it is one of three kinds.

| Kind | What it is | On a model change |
| --- | --- | --- |
| Compensation | A step order, a command to read a stack of docs before every edit, a scolding that fixed an older model, an order to run tests because the previous model would not | Remove it. Run the gate it names. Write it back only if the same miss repeats |
| Invariant | Who may merge, a frozen oracle, an allowed dependency edge, an allow case and a deny case, a budget | Stays. It is a decision, and the deletion pass does not include it |
| Fragile command | A migration, or any command that must run as written (Anthropic’s skill guide: one script, no extra flags) | The command stays. The explanation of why it exists is a compensation |

The constitution names `instruction-model`, the model the compensatory lines were written for. A procedural line that names no gate is a compensation. A judgement in the reviewer file stays ([§4](#4-design-principles), principle 10). A second model is a second test of the same lines. This file keeps one dated text for the named model.

### What gets deleted

Cherny, 27 July 2026: every new model, Claude Code deletes part of the system prompt, the tool set, and the tool prompts, because a line written for the previous model may not transfer three months later. For the 4.0 models that deletion was about half the system prompt (interview of 23 September 2025). For Opus 5 it was 80%. Those lines had been correcting behavior the new model already has. An ablation that strips the system prompts, including the tool prompts, left the model a little more intelligent. The product still keeps the prompts that make it behave the way a person wants. The rebuild is: delete, use it, and add a line back only when the same stumble repeats. Guessing the line first is the failure he names. The model reads every surviving line on every turn.

OpenAI’s GPT-6 Astra guide, read 27 October 2026, says the same thing in the repository’s own files. Best practices are changing. Skills, `AGENTS.md`, and task prompts accumulated for older models are worth revisiting at each release. Overly specific guidance, which used to help, can now hinder. A skill is a short router, not an itinerary. Forcing a full document stack before every edit is excessive. A pointer still helps when it says when the document applies. An old “run the tests” line makes a thorough model test more than the task needs. A hard boundary written so a weaker model would ask first makes a more aligned model stop too early. The GPT-5.5 prompting guide says to start from the smallest prompt that still states the outcome, and that carrying the old prompt stack narrows the search.

Anthropic’s skill guide, read the same day, starts from “Claude is already very smart” and adds only what Claude does not already know. Freedom matches fragility: a code review gets a direction, a migration command stays exact. A skill is tested on every model that will load it. A line that is right for the strongest model can be too thin for a smaller one, and a line that helps the smaller one can overconstrain the strongest (the Astra guide’s version of that warning).

Cat Wu, 15 May 2026, says the Claude Code team removes pieces of the system prompt and of tool descriptions as models get smarter, ships few opinionated tools, and lets a developer add one. Semantic navigation did not move their evals. She names Sutton’s Bitter Lesson as a reason not to freeze a form factor. A later collapse back to a text box is a possibility she states, and she says the tools are still needed. The essay itself was not re-read for this section.

Gloaguen et al. already measured the cost of extra always-loaded requirements on issue tasks ([§6](#6-persistent-artifacts)). That result is about the context file, not about the vendor’s system prompt. It points the same way.

### What stays

Cherny says almost all of the harness code that remains is safety, permissions, and static analysis, plus the UI. He also says verification is the part people most often leave out. “You don’t even need scaffolding” is about a long Opus 5 run: no `/goal`, and `/go` and `/loop` are optional. The examples he gives still contain the check. The two-week rewrite compares screenshots. The eleven-day language rewrite runs a test suite. A prescribed path comes out. A way to know the work is done stays.

Potencier’s public pages, fetched 27 October 2026, do not contain the sentence this section was asked to file under his name (a prompt from a few months ago is worth revisiting, because a more detailed prompt constrains a smarter model). The sentences that match are Cherny’s and OpenAI’s. What his pages do say is the other half of the split. The SymfonyLive Paris 2026 schedule abstract says the model is half of the result, and the harness — context, tools, prompts, and feedback — determines the quality. The recording was not transcribed, so that sentence is the abstract. The Upsun article quoting him calls trust a dial: a gate comes off after the step has been encoded as a script and the runs are consistently good. That is a deletion of supervision over a check that now runs itself. His release skill validates the plan, then lets the agent run, with a second model watching for anything unusual. One review question he publishes is whether he will be able to evolve the code next year.

His November 2025 talk, “LLMs as constrained generators,” was not transcribed. The title is about bounding the output. That is the fragile-command row, and it is earlier than the autonomy posts. This note does not treat the title as a retraction of either row.

### A thin loop on a large repo

The remembered strategy, “for the best models, do not harness at all, and let the model figure it out,” was not found as a sentence from a former Googler. The closest public statement is Logan Kilpatrick, 11 June 2026, who leads Google AI Studio and the Gemini API and was at OpenAI before that. He says the generic agent loop is scaffolding the model absorbs, and his estimate was about 12 months, which had not elapsed on the day of this note. Search and a choice of code execution stay outside the model. A model that cannot use someone else’s harness is not general. In the same interview, Google’s products sit on one harness, Antigravity. He is arguing about a proprietary loop as a moat. He is not describing a product with no policy.

Two measurements say a small tool loop keeps up with a large product harness on pass rate, and spends less, on issue-shaped tasks.

- Databricks, 8 July 2026, tasks taken from human pull requests on a multi-million-line repo, tests held out, git history sealed. The same model at the same effort kept the same quality in Pi as in Claude Code or Codex. The vendor harness cost more than 2× Pi in some pairs. Pi sent about 3× less context per turn. The post’s summary line also says the harness changes quality. The author’s shorter account of the same study says the success rate was the same, at about 2× less cost. This section uses the paired comparison.
- HarnessTax, Pan et al., 16 September 2026: seven models, three harnesses, 30 tasks from SWE-bench Lite and 30 from Terminal-Bench 2.0, three runs each. Success stayed within about ±2% and about ±5%. As a geometric mean, Claude Code cost about 2.0× Pi on SWE-bench Lite and about 1.5× Pi on Terminal-Bench 2.0. Pi’s tools are read, write, edit, and bash. One pair, Claude Fable 5 on SWE-bench Lite: Claude Code 97.8% at about $1.33, Pi 96.7% at about $0.67. The opening summary says up to 5×. The authors write that the benchmarks may have been seen in training, and that the result may not hold where requirements evolve and tasks cross sessions. They point that limit at the long-running-agent harness this file already uses.

The SWE-agent authors’ own later docs, read 27 October 2026, say that much of the custom interface they argued for in 2024 is no longer needed to make a useful agent. The live percentage on that page is not installed here.

Those results are about fixing a bounded change. They are not a result about this file’s gates. OpenAI’s February 2026 account, already in [§1](#1-what-this-is-and-what-it-is-not), is the large build: on the order of a million lines, and the loop depended on repository structure and tooling that took months. Young’s November 2025 harness is what makes a later session able to boot. Cherny’s remaining layer, safety and permissions and static analysis, is the same layer as the invariant row. A strategy of no outer policy is unevidenced for the build this file is for. A strategy of a short compensation layer, re-tested when the model changes, is what the pages support.

### The pass

This pass is the steward’s. It is the review row in [§12](#12-review-how-when-by-whom). The trigger is a change of `instruction-model`. Six months with the same name is the backstop, which is Cherny’s interval for a user of the coding agent. His product team ablates at each model release. Both intervals are his. Using them on this file’s instructions is this document’s choice.

The steward lists the compensatory lines, removes them, and re-runs the gate each line names. A procedural line that names no gate comes out. A gate that stays green means the line stays out. The decision log records the model, the line, and the signal. A miss that repeats is written back, dated to the new model, or it becomes a check a program can run ([§4](#4-design-principles), principle 10). Invariants and reviewer judgements stay off that list. Cherny’s warning about capability evals applies to a benchmark the model has saturated. A frozen example, and an allow case and a deny case, stay in force across that saturation.

Conflicts, the pages where the Potencier sentence was looked for, and the studies that were read and not used as a rule, are in `harness-sources/16-evolvability.md`.

---

## 20. Limits

Read these bounds before treating a rule in this file as a settled result from a lab or from AISVS. The first real product fills the constitution with real bars, then runs M0–M1 before the full lens set.

- Anthropic’s November 2025 post leaves open whether one general agent or several specialists is better. The January 2026 guidance from the same organization is to start with one agent. This document separates the verifier from the implementer as an assurance choice. The November harness does not require that split.
- OpenAI’s low-friction merge is one repository’s outcome after months of tooling. The post says it would be irresponsible at low throughput. This document does not start there. A red invariant check stays blocking even if a later team drops the human read.
- End-to-end driving misses some interface classes. Anthropic notes native dialogs the automation could not see. Record blind spots in the evidence file.
- “Startup under 800 ms” and “no span over two seconds” are example prompts, not budgets. A latency bar names the request, the proportion, the threshold, the measurement point, and the window. A budget without a legible trace is not a gate.
- No gate here proves the absence of defects. `AUDIT_PASS` means the checklist was re-run and the rows were green.
- This digest stops where a real project starts. The first product fills the constitution, including the complexity score and the routing posture, then runs M0–M1 before investing in the full lens set.
- AISVS AC.4.1 is stricter than G4. [§5](#5-roles) records that departure. This document does not claim the standard.
- A thin tool loop that matches a product harness on pass rate is a result about issue-shaped tasks. [§19](#19-evolvability) has the numbers. It is not a result about these gates.

---

## 21. Sources

Primary pages used for the rules above. The notes in `harness-sources/` (01 through 16) are the check for conflicts, second-hand pages, and claims that were not carried into this file.

Harness, the runtime, and the outer policy:

- Vivek Trivedy, LangChain, “The Anatomy of an Agent Harness,” 10 March 2026. <https://www.langchain.com/blog/the-anatomy-of-an-agent-harness>
- Ryan Lopopolo, OpenAI, “Harness engineering: leveraging Codex in an agent-first world,” 11 February 2026. <https://openai.com/index/harness-engineering/>
- Anthropic, “Effective context engineering for AI agents,” 29 September 2025. <https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents>
- Justin Young, Anthropic, “Effective harnesses for long-running agents,” 26 November 2025. <https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>
- Birgitta Böckeler, “Harness engineering for coding agent users,” martinfowler.com, 2 April 2026. <https://www.martinfowler.com/articles/harness-engineering.html>
- Mitchell Hashimoto, “My AI Adoption Journey,” 5 February 2026. <https://mitchellh.com/writing/my-ai-adoption-journey>
- Anthropic, “Building multi-agent systems: when and how to use them,” 23 January 2026. <https://claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them>

Evolvability of the instructions. Conflicts, the Potencier pages that were checked, and the studies not used as a rule are in `harness-sources/16-evolvability.md`.

- Boris Cherny, Y Combinator Startup School, 27 July 2026. Transcript: <https://www.ycrootaccess.com/p/boris-cherny-building-claude-code>
- Gergely Orosz, “How Claude Code is built,” 23 September 2025. <https://newsletter.pragmaticengineer.com/p/how-claude-code-is-built>
- OpenAI, “Rethinking skills and prompts for GPT-6 Astra,” read 27 October 2026. <https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra>
- OpenAI, GPT-5.5 prompting guide, read 27 October 2026. <https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.5>
- Anthropic, skill authoring best practices, read 27 October 2026. <https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices>
- Cat Wu, interviewed by Samuel Axon, “Claude Code’s product lead talks usage limits, transparency, and the lean harness,” Ars Technica, 15 May 2026. <https://arstechnica.com/ai/2026/05/claude-codes-product-lead-talks-usage-limits-transparency-and-the-lean-harness/>
- Vinay Gaba, Ankit Mathur, Rishabh Singh, Patrick Wendell, and Matei Zaharia, “Benchmarking Coding Agents on Databricks’ Multi-Million Line Codebase,” 8 July 2026. <https://www.databricks.com/blog/benchmarking-coding-agents-databricks-multi-million-line-codebase>
- M. Z. Pan, S. Yang, N. Arabzadeh, W.-L. Chiang, Ion Stoica, and Matei Zaharia, HarnessTax, 16 September 2026. <https://arena.ai/blog/coding-agents-harness-tax>
- Logan Kilpatrick, Training Data, 11 June 2026. <https://sequoiacap.com/podcast/google-deepminds-logan-kilpatrick-why-the-model-eats-the-harness>
- Fabien Potencier, SymfonyLive Paris 2026, schedule abstract. <https://live.symfony.com/2026-paris/schedule/building-a-coding-agent-in-php-inside-the-harness>
- Upsun, “The AI trust dial,” quoting Potencier, read 27 October 2026. <https://upsun.com/blog/trust-dial-ai-agents-autonomy/>

Stops, review, and who may merge:

- Anthropic, Claude 3.7 Sonnet system card. <https://www.anthropic.com/claude-3-7-sonnet-system-card>
- Claude Code Ralph Wiggum plugin README. <https://github.com/anthropics/claude-code/blob/main/plugins/ralph-wiggum/README.md>
- Addy Osmani, Morgan Westlee Lunt, and Qing Wang, “How the Claude Code team does code review,” 15 October 2026. <https://claude.dev/blog/solving-code-review-at-anthropic/>
- Anthropic, “How Anthropic secures its AI-native software development lifecycle.” <https://claude.com/blog/how-anthropic-secures-its-ai-native-software-development-lifecycle>
- Google eng-practices, “What to look for in a code review.” <https://google.github.io/eng-practices/review/reviewer/looking-for.html>
- OWASP AISVS 1.0, Appendix C, June 2026. <https://github.com/OWASP/AISVS/blob/v1.0/1.0/en/0x92-Appendix-C_AI_for_Code_Generation.md>
- GitLab, “Build human checkpoints into the workflow.” <https://about.gitlab.com/ai-native-playbook/topics/working-with-agents/build-human-checkpoints-into-the-workflow/>
- Zeng et al., SWR-Bench, DOI 10.1145/3808144. <https://arxiv.org/abs/2509.01494>
- developertoolkit.ai, “Human in the loop: judgment at the gates,” updated 16 October 2026. <https://developertoolkit.ai/en/method/principles/human-in-the-loop/>

Artifacts, workflow, modules:

- AGENTS.md spec. <https://agents.md/>
- Linux Foundation, Agentic AI Foundation, 9 December 2025. <https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation>
- Matt Nigh, “How to write a great agents.md: Lessons from over 2,500 repositories,” 19 November 2025. <https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/>
- Gloaguen et al., arXiv:2602.11988, 13 February 2026. <https://arxiv.org/abs/2602.11988>
- Chatlatanagulchai et al., DOI 10.1145/3840295. <https://doi.org/10.1145/3840295>
- Anthropic Science, “Long-running Claude for scientific computing,” 23 March 2026. <https://www.anthropic.com/research/long-running-claude>
- GitHub Spec Kit, agentic SDD reference. <https://github.github.io/spec-kit/reference/agentic-sdd.html>
- Spec Kit task template. <https://github.com/github/spec-kit/blob/main/templates/tasks-template.md>
- Birgitta Böckeler, “Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl,” 15 October 2025. <https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html>
- Birgitta Böckeler, “How far can we push AI autonomy in code generation?,” 5 August 2025. <https://www.martinfowler.com/articles/pushing-ai-autonomy.html>
- Alistair Cockburn, walking skeleton (essay, archived). <https://web.archive.org/web/20140329213351/http://alistair.cockburn.us/Walking+skeleton>
- Alistair Cockburn, hexagonal architecture, 4 September 2005. <https://alistair.cockburn.us/hexagonal-architecture/>
- Steve Freeman and Nat Pryce, *Growing Object-Oriented Software, Guided by Tests*, 2009. Contents: <https://growing-object-oriented-software.com/toc.html>
- Henrik Kniberg and Alistair Cockburn, Elephant Carpaccio facilitation guide, 11 July 2013. <https://docs.google.com/document/d/1TCuuu-8Mm14oxsOnlk8DqfZAA1cvtYu9WGv67Yj_sSk/pub>
- Neal Ford, definition page for *Building Evolutionary Architectures*. <https://nealford.com/books/buildingevolutionaryarchitectures.html>
- Anthropic, dynamic workflows in Claude Code, engineering post 2 June 2026. <https://claude.com/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code>
- Claude Code, code review. <https://code.claude.com/docs/en/code-review>

Attributes, gates, tests, CI, measurement:

- OWASP ASVS. <https://asvs.dev/>
- OWASP API Security Top 10, 2023, unrestricted resource consumption. <https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/>
- Google SRE book, service level objectives. <https://sre.google/sre-book/service-level-objectives/>
- Google SRE workbook, canarying releases. <https://sre.google/workbook/canarying-releases/>
- GitLab secret push protection. <https://docs.gitlab.com/user/application_security/secret_detection/secret_push_protection/>
- *Software Engineering at Google*, chapter 23. <https://abseil.io/resources/swe-book/html/ch23.html>
- ESLint, configure rules. <https://eslint.org/docs/latest/use/configure/rules>
- GitHub rulesets. <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets>
- SLSA v1.2 source requirements. <https://slsa.dev/spec/v1.2/source-requirements>
- Martin Fowler, “Eradicating Non-Determinism in Tests,” 14 April 2011. <https://martinfowler.com/articles/nonDeterminism.html>
- Martin Fowler, “Test Coverage,” 17 April 2012. <https://martinfowler.com/bliki/TestCoverage.html>
- Google Testing Blog, “Code Coverage Best Practices,” 7 August 2020. <https://testing.googleblog.com/2020/08/code-coverage-best-practices.html>
- Ian Robinson, “Consumer-Driven Contracts,” 12 June 2006. <https://martinfowler.com/articles/consumerDrivenContracts.html>
- Pact, consumer tests. <https://docs.pact.io/consumer>
- Konstantinou, Tambon, and Papadakis, arXiv:2607.05139. <https://arxiv.org/abs/2607.05139>
- Zhong, Raghunathan, and Carlini, ImpossibleBench, arXiv:2510.20270. <https://arxiv.org/abs/2510.20270>
- Dagger, ASE 2026, DOI 10.1145/3832783.3844564. <https://dl.acm.org/doi/10.1145/3832783.3844564>
- DORA, Accelerate State of DevOps 2024 (22 October 2024). <https://dora.dev/research/2024/dora-report/2024-dora-accelerate-state-of-devops-report.pdf>
- DORA, State of AI-assisted Software Development 2025 (23 September 2025). <https://dora.dev/research/2025/dora-report/>
- DORA metrics guide. <https://dora.dev/guides/dora-metrics-four-keys/>
- Jimenez et al., SWE-bench, arXiv:2310.06770. <https://arxiv.org/abs/2310.06770>
- METR, “Many SWE-bench-passing PRs would not be merged into main,” 10 March 2026. <https://metr.org/notes/2026-03-10-many-swe-bench-passing-prs-would-not-be-merged-into-main/>
- GitClear, “AI Copilot Code Quality,” February 2025. <https://www.gitclear.com/ai_assistant_code_quality_2025_research/>
- Stripe, “Minions,” parts 1 and 2. <https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents>

Change-risk score, mutation, dependencies, and near-duplicates. Ceilings that moved, and numbers that were not installed, are checked in `harness-sources/15-uncle-bob.md`.

- Robert C. Martin, post of 14 April 2026, as quoted by Jan Giacomelli, “Is Clean Code Dead?,” 12 August 2026. <https://jangiacomelli.com/blog/is-clean-code-dead/>
- Robert C. Martin, thread of 20 April 2026. <https://x.com/unclebobmartin/status/2046206145597972849>
- Robert C. Martin, dry analysis among the walls, 25 May 2026. <https://x.com/unclebobmartin/status/2058869482815197320>
- Robert C. Martin, play-test after a metric gauntlet, 6 June 2026. <https://x.com/unclebobmartin/status/2063344635150553131>
- Robert C. Martin, cyclomatic complexity for agent-facing functions, 19 June 2026. <https://x.com/unclebobmartin/status/2067935532580024741>
- Kent C. Dodds, interview with Robert C. Martin, 22 July 2026. <https://www.youtube.com/watch?v=RxxxGkFIUJ0>
- Matt Pocock, live interview with Robert C. Martin, 19 August 2026. <https://www.youtube.com/watch?v=zcLPGC-tvgk>
- Aviator, interview with Robert C. Martin, 24 September 2026. <https://www.youtube.com/watch?v=5kCISBJwoZo>
- Nariman Codes, commentary quoting the 14 April post, 7 June 2026. Not a source for the gates. <https://www.youtube.com/watch?v=wqYZSCC6Ino>
- Robert C. Martin, “Mutation Testing,” 10 June 2016. <https://blog.cleancoder.com/uncle-bob/2016/06/10/MutationTesting.html>
- Change-risk checker. <https://github.com/unclebob/crap4clj>
- Mutation checker. <https://github.com/unclebob/clj-mutate>
- Dependency checker. <https://github.com/unclebob/dependency-checker>
- Near-duplicate checker. <https://github.com/unclebob/dry4clj>
- Example-structure checker. <https://github.com/unclebob/scrap>
- Example-nesting checker. <https://github.com/unclebob/speclj-structure-check>
- Layer picture, not a pass/fail check. <https://github.com/unclebob/arch-view>
- Alberto Savoia, CRAP formula, October 2007. <https://www.artima.com/weblogs/viewpost.jsp?thread=215899>
- G. Ann Campbell, Sonar, “Cognitive Complexity,” 7 December 2016. <https://www.sonarsource.com/blog/cognitive-complexity-because-testability-understandability/>

Seams, slice cuts, and the two review lists. Conflicts, and claims that were not adopted, are checked in `harness-sources/14-matt-pocock.md`.

- Matt Pocock, skills repository, created 3 February 2026, `main` read 27 October 2026. <https://github.com/mattpocock/skills>
- Matt Pocock, skill index, read 27 October 2026. <https://aihero.dev/skills>
- Matt Pocock, “The /to-spec Skill,” 27 April 2026. <https://www.aihero.dev/skills-to-spec>
- Matt Pocock, `to-tickets` skill, 27 April 2026. <https://github.com/mattpocock/skills/blob/main/skills/engineering/to-tickets/SKILL.md>
- Matt Pocock, `tdd` skill, read 27 October 2026. <https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md>
- Matt Pocock, “The /implement Skill,” 7 July 2026. <https://www.aihero.dev/skills-implement>
- Matt Pocock, “The /code-review Skill,” 7 July 2026. <https://www.aihero.dev/skills-code-review>
- Matt Pocock, `retro` skill, 5 October 2026. <https://github.com/mattpocock/skills/blob/main/skills/engineering/retro/SKILL.md>
- Matt Pocock, `writing-for-agents`, read 27 October 2026. <https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md>
- Matt Pocock, “A Complete Guide To AGENTS.md,” 18 January 2026. <https://www.aihero.dev/a-complete-guide-to-agents-md>
- Matt Pocock, dictionary entry `AGENTS.md`, read 27 October 2026. <https://github.com/mattpocock/dictionary-of-ai-coding/blob/main/dictionary/AGENTS.md.md>
- Matt Pocock, “How To Make Codebases AI Agents Love,” 26 February 2026. <https://www.aihero.dev/how-to-make-codebases-ai-agents-love>
- Matt Pocock, “5 Agent Skills I Use Every Day,” 16 March 2026. <https://www.aihero.dev/5-agent-skills-i-use-every-day>

Augment’s harness guide (16 April 2026, updated 29 June 2026, <https://www.augmentcode.com/guides/harness-engineering-ai-coding-agents>) is the source of the one-turn / one-session / whole-build table and of the name Plan-Execute-Verify. It is not the origin of the OpenAI, Anthropic, Böckeler, or Hashimoto claims above. Its phrase “hard CI failures, not warnings” is not in the OpenAI post. developertoolkit.ai is the source of the four-gate vocabulary and the short risk list, not of the lab or OWASP rules. QualityLayer’s spec-driven guide (<https://qualitylayer.dev/blog/spec-driven-development-guide>) is vendor marketing and is not a source for the outer sequence. RayLabs is not the source for independent review. Nigh’s “2,500 repositories” figure is about Copilot persona files under `.github/agents/`, not a census of the AGENTS.md spec.
