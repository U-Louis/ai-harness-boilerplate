# Inner loop — source note

Research date: 27 Oct 2026. Scope: the inner loop of a coding agent — orient, plan, execute one slice, self-check, independent verify, audit — and the neighboring claims about Plan-Execute-Verify, generate-and-check versus phase gates, one feature per session, and a fresh-context verifier. Read against section 8 of `ai-harness-state-of-the-art.md`, and against the pass-flag and revision rules in sections 2, 15, and 16. That file was not edited.

“Primary” means the team that did the work, or the paper’s authors. A catalog, a course, or a plugin that names the same loop is not primary.

Ranked by how directly the page prescribes this loop. Dates are the dates printed on the page. Living docs are dated by the fetch.

---

## Ranked sources

### 1. Effective harnesses for long-running agents

- **Org:** Anthropic Engineering
- **Author:** Justin Young
- **Date:** 26 Nov 2025
- **URLs:**
  - https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
  - Prompt that implements the session: https://github.com/anthropics/claude-quickstarts/blob/main/autonomous-coding/prompts/coding_prompt.md (read from `main` on 27 Oct 2026; the file has no date of its own)
- **Why primary:** The lab’s own account of the harness the dry run cites for one feature per session, the failing feature list, `init.sh`, the progress file, and the startup ritual. The prompt is the operational text. A footnote says the initializer and the coding agent share the system prompt, the tools, and the harness. They differ only in the first user prompt.

**Loop**

```text
Session 1 (initializer): feature list, all failing → init.sh → git → progress file
Every later session, same coding agent, fresh context:
  pwd → read spec, feature list, progress, git log
  → boot init.sh
  → re-test 1–2 core features already marked passing
  → if any fail, mark them failing and repair before any new feature
  → pick the single highest-priority failing feature
  → implement it
  → verify it in the UI, as a user (browser automation, screenshots)
  → only then set passes: true
  → commit and append the progress file
  → leave a bootable tree
```

**Claims that bear on the dry run**

- The agent that writes the feature is the agent that flips `passes`. The prompt says the only legal edit to the feature file is that field, and only after UI verification with screenshots. Unit tests and `curl` were the failure mode this step exists to stop.
- The fresh session is required to read the progress file and `git log`. That handoff is the memory. The post calls compaction an insufficient substitute.
- “One feature at a time” is the remedy for one-shotting the app. The prompt’s session goal is “at least one feature,” and it allows the initializer to start implementation if time remains. The emphasis is scope, not a hard ban on a second feature inside a long session.
- The post leaves open whether one general coding agent or specialists (testing, QA, cleanup) would do better. It does not describe a separate verifier, an auditor, a revision cap, or a rule that the judge must not see the build.
- Browser automation misses some UI (native alert dialogs). The post records that limit.

### 2. Harness design for long-running application development

- **Org:** Anthropic Engineering, Labs
- **Author:** Prithvi Rajasekaran
- **Date:** 24 Mar 2026
- **URL:** https://www.anthropic.com/engineering/harness-design-long-running-apps
- **Why primary:** The same harness, revised by a Labs author who names the November work as the previous version. This is the page that adds a planner and an evaluator, and then strips pieces back when the model changes.

**Loop**

```text
Planner: 1–4 sentence prompt → high-level product spec (not a line-by-line design)
Each sprint, while the sprint construct existed:
  generator and evaluator agree a sprint contract (what “done” means, how it will be verified)
  → generator implements one feature and self-evaluates
  → evaluator drives the running app (Playwright), grades against hard thresholds
  → any failed threshold returns the sprint with specific findings
Later, on Opus 4.6, for one of the reported runs:
  planner → one long build → QA → build → QA → build → QA
Agents communicate through files. On Opus 4.5 the author dropped per-session context resets and ran one continuous session with compaction.
```

**Claims that bear on the dry run**

- Self-evaluation is the failure this harness adds a second agent to fix. Generators praise their own mediocre work. A separate evaluator can be tuned to be skeptical. The author says the split does not by itself remove leniency: the evaluator is still an LLM that is generous to LLM output, and early runs identified a real bug and then talked themselves into approving it.
- The generator still self-checks before QA. The evaluator is a second pass, not a replacement for the author’s own test.
- The evaluator’s findings are concrete (criterion, file, line, what happened). They are handed back. The generator is not asked to repair from a blank context.
- The November context reset was a Sonnet 4.5 remedy for “context anxiety.” Opus 4.5 made the reset unnecessary in this harness. Fresh sessions are a response to a model limit, not a standing law.
- One reported full-stack run was about 6 hours and $200 against a 20-minute, $9 solo run. A later run, after sprints were removed, still did three QA rounds (about 4 hours, $125). The author then says the evaluator pays for itself when the task is past what the current model does reliably alone, and is overhead when it is not. A new model is a reason to delete harness pieces.
- The planner is told not to specify granular implementation. A wrong detail in the spec cascades. That cuts against a plan that freezes file-level steps before anyone has run the code.

### 3. Building multi-agent systems: when and how to use them

- **Org:** Anthropic
- **Date:** 23 Jan 2026 (`datePublished` on https://claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them)
- **URL:** https://claude.com/blog/building-multi-agent-systems-when-and-how-to-use-them
- **Why primary:** Anthropic’s own decision rule for splitting a coding loop into planner, implementer, tester, and reviewer. It is the page that both warns against that split and carves out the verifier exception.

**Loop they say works**

```text
Default: one agent plans, implements, and tests the feature, because those phases share context.
Exception, black-box verification:
  main agent finishes a unit of work
  → a verification subagent receives the requirements, the list of files, and the command to run
  → it runs the suite and reports pass/fail and issues
  → on failure, the issues are appended and the main agent tries again
  → stop after max_attempts (the sample sets this to 3) and raise
```

**Claims that bear on the dry run**

- Separate agents for planning, execution, review, and iteration lost context at each handoff and spent more tokens coordinating than executing. In their testing, multi-agent runs used 3–10× the tokens of a single agent on equivalent tasks.
- Dividing by role (one agent writes features, another writes tests, a third reviews) is the decomposition they call counterproductive. The reviewer then lacks the exploration and the iteration. An experiment with planner, implementer, tester, and reviewer spent more tokens on coordination than on the work.
- “Sequential phases of the same work” is listed as a bad boundary. Planning, implementation, and testing of the same feature “share too much context.”
- The exception is black-box verification: a verifier that only runs tests and reports results does not need the implementation history. “The verifier does not need to understand why the artifact was built as it was. It only needs to determine whether the artifact meets the specified criteria.”
- The sample repair does not hide the failure. `requirements` is extended with “Previous attempt failed” plus the issues.
- `max_attempts = 3` is an example default, then the function raises. It is not described as a measured optimum, and it is three attempts, not two send-backs.
- The failure mode they name for this verifier is declaring victory after one or two tests. The mitigation is an explicit command to run the whole suite, including inputs that should fail.

### 4. Claude Code best practices, plan mode, and subagents

- **Org:** Anthropic, Claude Code docs
- **Date:** Living docs, fetched 27 Oct 2026
- **URLs:**
  - https://code.claude.com/docs/en/best-practices
  - https://code.claude.com/docs/en/permission-modes (plan mode)
  - https://code.claude.com/docs/en/sub-agents
- **Why primary:** The product manual for the session loop, the plan gate, and what a fresh subagent actually receives.

**Loop**

```text
If the spec is still fuzzy: interview until SPEC.md exists, then start a fresh session to build from that file.
Plan mode (optional): research and write a plan; edits stay blocked until the plan is approved.
Implement in the build session.
Self-check: a command that returns pass or fail; keep going until it passes.
  Optional: /goal, a separate model checks the conversation after each turn.
  Optional: a Stop hook blocks the end of the turn until a command passes, or a block limit is hit.
Optional, still in the session: /verify builds and runs the app and looks at the result.
Review: /code-review, or a second session, or a subagent, pointed at SPEC.md and the diff.
```

**Claims that bear on the dry run**

- `/code-review` “reviews the diff in a fresh subagent that never saw your conversation. It isn’t swayed by the reasoning that produced the code.” A second session can review a file “without having watched it being written.” The docs also say a fresh reviewer helps “because it hasn’t seen your reasoning,” and that handing the same task to a subagent does not make the answer smarter.
- A non-fork subagent “doesn’t see your conversation history.” Claude writes a delegation message. A fork inherits the parent on purpose. Clean context is the default for a subagent. It is not a platform deletion of the build log from every judge. The parent decides the handoff.
- `/verify` runs in the session that just did the work. It is the opposite assignment from `/code-review`.
- “If you’ve corrected the same thing twice and it’s still wrong, a third correction mostly adds to a conversation that already contains two failed attempts.” The prescribed move is `/clear` and one new prompt that includes the lesson. A correction made twice also belongs in `CLAUDE.md`.
- Plan mode is a gate on edits, in the same session, until a person approves. `showClearContextOnPlanAccept` is an option that approves the plan and clears the planning context. The default path keeps the session.
- The docs do not say one feature per session, and they do not say the reviewer sets a pass flag the author is forbidden to set.

### 5. Dynamic workflows in Claude Code

- **Org:** Anthropic
- **Date:** Docs fetched 27 Oct 2026. Engineering post by Thariq Shihipar and Sid Bidasaria, 2 Jun 2026.
- **URLs:**
  - https://code.claude.com/docs/en/workflows
  - https://claude.com/blog/a-harness-for-every-task-dynamic-workflows-in-claude-code
- **Why primary:** The product’s own account of when a script, rather than the model, should hold the loop, and of adversarial verification as a pattern inside that script.

**Loop**

```text
Ordinary coding stays on the default harness (plan and execute in one context).
When the task is large, parallel, or adversarial, Claude writes a JavaScript workflow:
  the script holds the loop and the intermediate results
  → agent() / parallel() / pipeline() spawn subagents, optionally in their own worktrees
  → a second agent adversarially checks a finding, a claim, or one rule
  → a skeptic can drop false positives
  → loop until a stop condition (no new findings, no more errors), not a fixed pass count
A stalled agent restarts from the same prompt, without that attempt’s transcript, at most five times. That is a stall retry, not a reject-and-repair cap.
```

**Claims that bear on the dry run**

- The blog names three reasons to leave the single context: stopping early on a multi-part task, preferring one’s own findings when asked to judge them, and goal drift across compaction.
- “One verifier per rule,” each in a clean context, plus a skeptic, is the pattern for rules the model keeps missing. It is not the default inner loop of a feature.
- “Most traditional coding tasks do not need a panel of 5 reviewers.” Multi-agent versus single-agent “follows similar logic — parallelism and specialization have to earn their coordination cost.”
- The user sees one report at the end, not the turn-by-turn transcript of the workers. That hides worker scratch from the parent. It does not say the verifier must be denied the diff or the spec.

### 6. Harness engineering for AI coding agents — the PEV section

- **Org:** Augment Code
- **Author:** Molisha Shah
- **Date:** 16 Apr 2026, updated 29 Jun 2026
- **URL:** https://www.augmentcode.com/guides/harness-engineering-ai-coding-agents
- **Why primary:** The page the dry run cites for “Plan, then execute inside the plan, then verify.” It is primary for what Augment means by PEV. It is not primary for an older pattern of the same name. The section cites no paper. See the provenance section below.

**Loop**

```text
Plan: decompose, with acceptance criteria
→ Execute, bounded by that plan
→ pre-execution gates on every tool call (known tool, valid arguments, approval, path inside the workspace)
→ runtime feedback whose error text says how to comply
→ post-execution check against the plan (plan alignment: did it reuse the auth middleware, the response shape)
   and against external quality checks
```

**Claims that bear on the dry run**

- Generate-and-check, in their table, has no planning, an unconstrained execution, and a binary pass/fail after the fact. PEV adds a plan, bounds the execution, and checks before, during, and after, including questions a test suite cannot see.
- The page does not say the verifier is a second model, does not say to hide the build transcript, and does not give a revision count. Pass@1 is a metric they tell the reader to track (resolved on the first attempt), not a cap of two repairs.
- Humans “approve at high-leverage decision points” and can pause a session. The Cosmos product, described later on the same page, is a multi-agent system (environments, experts, sessions) with a code-review expert. That product description is not the definition of the PEV loop.
- “Over-constraining is a real failure mode.” Constraints, then feedback, then gates, is their implementation order. The dry run already uses that sentence for the outer harness.

### 7. Cursor Plan Mode

- **Org:** Cursor
- **Date:** Docs fetched 27 Oct 2026. Announcement by Jai Smith, 7 Oct 2025.
- **URLs:**
  - https://cursor.com/docs/agent/plan-mode
  - https://cursor.com/blog/plan-mode
- **Why primary:** The vendor’s own description of the plan-then-build gate in this editor. The blog is the claim that most new features at Cursor now start as a plan, and that this improved the code. The docs are the current steps.

**Loop**

```text
Clarifying questions
→ research the codebase
→ a plan the person can edit (markdown, file paths, to-dos)
→ the person clicks Build
If the build misses: revert, make the plan more specific, run it again
Skip plan mode for a small change you have done many times
```

**Claims that bear on the dry run**

- The gate is a person accepting the plan. There is no independent verifier, no feature list, no orient ritual, and no revision counter.
- The recovery they recommend is a new execution from a tighter plan, rather than a chain of follow-up repairs in the failed session.
- The docs do not say plan mode refuses a write the way Claude Code plan mode does. They describe a mode the person enters and then leaves by building.

### 8. Reflexion

- **Org:** Authors; NeurIPS 2023
- **Authors:** Noah Shinn, Federico Cassano, Edward Berman, Ashwin Gopinath, Karthik Narasimhan, Shunyu Yao (as printed on the arXiv HTML read here)
- **Date:** NeurIPS 2023. arXiv:2303.11366
- **URLs:**
  - https://arxiv.org/abs/2303.11366
  - https://proceedings.neurips.cc/paper_files/paper/2023/hash/1b44b878bb782e6954cd888628510e90-Abstract.html
- **Why primary:** The peer-reviewed actor / evaluator / self-reflection loop. It is the paper that shows, on code, what happens when the evaluator does and does not see an execution trace.

**Loop**

```text
Actor produces a trajectory in the environment
→ Evaluator scores that trajectory (exact match, a heuristic, or another LLM call)
→ Self-Reflection writes a verbal summary into memory
→ Actor tries again with that memory
→ stop when the evaluator passes, or when the trial limit is hit
Programming variant: the evaluator is a self-generated unit-test suite (at most 6 tests), executed, not a second agent reading the source and guessing.
```

**Claims that bear on the dry run**

- The evaluator’s input is the trajectory. The next trial’s input is the reflection. Hiding the trace from the evaluator is the ablation they report as harmful: on the 50 hardest HumanEval Rust tasks, reflection without tests scored 52% against a 60% baseline, and tests without the verbal reflection stayed at the 60% baseline. Both together reached 68%.
- Memory of reflections is bounded, usually to 1–3, and to 1 in the programming setup. That bound is about context size, not about escalating to a person after two failures.
- Trial counts elsewhere in the paper are larger than two. AlfWorld runs are discussed across 12 trials. On HotpotQA they retry until three consecutive failures. The paper does not treat two as the point where more attempts stop helping.
- The programming tests are written by the same model family. A suite that passes on a wrong program ends the loop early. They treat that false pass as worse than a false fail, because a false fail can still be reflected on.

### 9. Agentless

- **Org:** University of Illinois Urbana-Champaign. Journal version in Proceedings of the ACM on Software Engineering.
- **Authors:** Chunqiu Steven Xia, Yinlin Deng, Soren Dunn, Lingming Zhang
- **Date:** arXiv:2407.01489 (July 2024). Journal version published 19 Jun 2025, PACMSE Vol. 2, Issue FSE, article FSE037. https://doi.org/10.1145/3715754
- **URL:** https://arxiv.org/abs/2407.01489
- **Why primary:** A peer-reviewed coding pipeline whose third phase is verification by execution, and whose thesis is that an autonomous agent loop is not required for that.

**Loop**

```text
Localize: files → classes and functions → edit sites
→ sample candidate patches in a simple diff (default in the paper: 4 location samples × 10 patches)
→ sample reproduction tests; keep a test only if executing it on the unpatched repo prints “Issue reproduced”
→ run regression tests; keep patches with the fewest failures
→ run the reproduction test; keep patches that print “Issue resolved”
→ pick among the survivors
The model does not choose the next tool. There is no session, no progress file, and no second LLM reading a build transcript.
```

**Claims that bear on the dry run**

- The paper’s question is whether a complex tool-using agent is necessary. On SWE-bench Lite at submission they report 32% resolved (96 of 300) at about $0.70, the highest open-source number they compare, against agent systems whose traces they describe as 30 or 40 turns and hard to audit.
- Verification is the test process, not a reviewer. The LLM writes the reproduction test. Execution on the original repo is what admits the test. That is generate-and-validate. It is the pattern Augment’s table calls weaker than PEV, and it beat the agent baselines in this paper’s comparison. The two claims are about different failures: Agentless is about bug-fixing cost and resolve rate; Augment’s plan-alignment gate is about architecture a test suite cannot see. Neither page refutes the other.
- The model is asked which existing tests are not regression tests, because a real fix may change behavior. An LLM still sits inside the “oracle” selection. The independence is the execution, not a fresh context window.

### 10. SWE-agent

- **Org:** Princeton Language and Intelligence. NeurIPS 2024.
- **Authors:** John Yang, Carlos E. Jimenez, Alexander Wettig, Kilian Lieret, Shunyu Yao, Karthik Narasimhan, Ofir Press
- **Date:** arXiv:2405.15793, 6 May 2024; v3, 11 Nov 2024. NeurIPS 2024.
- **URL:** https://arxiv.org/abs/2405.15793
- **Why primary:** The peer-reviewed coding agent whose contribution is the loop’s interface (what the model sees after each action), not a plan/execute/verify phase split. The current README says mini-swe-agent has superseded it and is simpler. The loop below is from the NeurIPS paper, not from the later 100-line agent.

**Loop**

```text
One LM, one context.
Each turn: a thought and one action
→ ACI runs it (search, a 100-line file window, an edit, python, pytest)
→ a concise observation comes back
→ observations older than the last five are collapsed to one line; the plan and the action history stay
→ the agent submits, or a $4 per-instance budget submits whatever edits exist
A linter can reject an edit that introduces a syntax error, show before and after, and ask for the edit again. The file is unchanged until the edit is valid.
```

**Claims that bear on the dry run**

- Execution feedback is inside the same agent that is editing. The paper’s measured behavior, not a required phase, is: reproduce or localize first, then from about turn 5 the frequent pair is edit, then run. Resolved GPT-4 runs had a median of 12 steps and $1.21. Unresolved runs averaged 21 steps and $2.52. The authors suspect a higher budget would not have fixed the unresolved set.
- They keep the action history on purpose and throw away stale file dumps. That is a context diet. It is not a second agent denied the transcript.
- There is no orient step, no feature list, and no human escalation at a fixed revision count. The stop is the agent’s `submit` or the dollar cap.

### 11. Cognition on multi-agent context

- **Org:** Cognition
- **Author:** Walden Yan
- **Dates:** 12 Jun 2025 and 22 Apr 2026
- **URLs:**
  - https://cognition.com/blog/dont-build-multi-agents
  - https://cognition.com/blog/multi-agents-working
- **Why primary:** The maker of Devin, first arguing for one linear agent that shares full traces, then reporting the multi-agent pattern they actually shipped: one writer, and a reviewer that starts clean. The two posts are one argument updated ten months later.

**Loop in the 2026 post**

```text
One writer.
A review agent with no shared context looks at the result.
Findings come back to the writer, which still has the user instructions and the decisions, and filters them.
The two iterate. Writes stay single-threaded.
```

**Claims that bear on the dry run**

- 12 Jun 2025: “Share context, and share full agent traces, not just individual messages.” Actions carry decisions that were never written down. A subagent that did not see those actions will contradict them. The recommended default is one linear agent. As of that date they describe Claude Code subagents as question-answerers, not parallel writers, because the subagent lacks the main trace.
- 22 Apr 2026: the parallel-writer warning still stands. The pattern they say works is extra intelligence beside one writer. “A clean-context reviewer catches bugs the coder can’t see.” They report the review loop working best when the two agents share no context beforehand. The reasons they give are attention at long context, and a reviewer that can question an instruction the writer accepted. They also say the writer must use its broader context to reject out-of-scope findings, or the loop chases itself.
- They describe multiple review cycles as normal, and sometimes too slow. They do not publish a cap of two.
- Devin Review, on PRs Devin wrote, “catches an average of 2 bugs per PR, of which roughly 58% are severe.” That is their production measurement, on their own reviewer, not a lab benchmark.

---

## Does “Plan-Execute-Verify” have a primary source besides Augment?

The name, and the table that calls generate-and-check weaker than phase gates, are Augment’s (source 6). The guide does not cite a paper for them.

Older primaries cover pieces, under other names:

| Source | Date | What it actually prescribes | Verify phase? |
| --- | --- | --- | --- |
| Wang, Xu, Lan, Hu, Lan, Lee, Lim, “Plan-and-Solve Prompting,” ACL 2023. https://aclanthology.org/2023.acl-long.147/ | July 2023 | One prompt: devise a plan, then carry the plan out. Arithmetic and reasoning, not a coding agent. | No |
| LangChain, “Plan-and-Execute Agents.” https://www.langchain.com/blog/planning-agents | 13 Feb 2024 | Planner writes the steps. Executors run them. A replanner decides whether to stop or write a follow-up plan. They cite Wang et al. and BabyAGI. | No separate verifier. Replan is the check. |
| Ridnik, Kredo, Friedman, AlphaCodium, arXiv:2401.08500. https://arxiv.org/abs/2401.08500 | 16 Jan 2024 | Natural-language pre-processing, then generate, run, and fix against public tests and model-written tests, until pass or a try-limit. They call this flow engineering against a single prompt. On CodeContests validation, GPT-4 pass@5 moved from 19% to 44%. | Yes, and it is test execution in the same flow. The try-limit is named; the HTML read here does not state the integer. |
| Anthropic, “Building effective agents.” https://www.anthropic.com/engineering/building-effective-agents | 19 Dec 2024 | Evaluator-optimizer: one call generates, another evaluates, in a loop. Autonomous agents should take ground truth from the tool result at each step, and a max iteration count is a normal stop. The count is not specified. The page now says the tooling it describes has changed. | Yes, as a second call. Not a plan-alignment gate. |

None of those pages is Augment’s PEV. The nearest coding ancestor of “do not one-shot; stage the run and the tests” is AlphaCodium. The nearest ancestor of “a second model judges and loops” is Anthropic’s evaluator-optimizer. The nearest ancestor of “write the whole plan, then act, then replan” is LangChain’s plan-and-execute, which stops one role short of a verifier. Augment’s addition is the pre-execution gate and the plan-alignment check (reuse the existing auth path). That addition is theirs.

---

## Conflicts: one agent or several

These are sequential positions, including inside Anthropic.

| When | Who | What they say about the inner loop |
| --- | --- | --- |
| Dec 2024 | Anthropic, “Building effective agents” | Start simple. Add an evaluator-optimizer or an orchestrator only when a single call is not enough. Coding agents are attractive because tests are ground truth. A person still checks system fit. |
| May–Nov 2024 | SWE-agent; Agentless | One interactive agent with a careful interface, or no agent at all: localize, patch, execute. Agentless argues the agent trace is the expensive, hard-to-audit part. |
| Jun 2025 | Cognition | One linear agent. Share full traces. Subagents answer questions. |
| Nov 2025 | Anthropic long-running harness | One coding agent per fresh session, one feature, self-verify, set `passes`. Specialists might be better. Unresolved. |
| Jan 2026 | Anthropic multi-agent article | Role splits for the same feature lost context and cost 3–10×. A black-box test verifier is the split they keep. |
| Mar 2026 | Anthropic Labs harness | Planner, generator, and evaluator, then a measured deletion of the per-sprint reset and of per-sprint QA when the model could hold the build. The evaluator stayed for work at the edge of the model. |
| Apr 2026 | Cognition | One writer. A clean-context reviewer beside it. Parallel writers still fail. |
| Apr 2026 | Augment | PEV as phases. Cosmos, separately, is specialists. |
| Jun 2026 | Claude Code workflows | A scripted multi-agent loop for adversarial and large tasks. Ordinary coding stays one context. A panel of reviewers has to earn its cost. |
| Oct 2025–2026 | Cursor | One agent, a person between plan and build. |

The dry run’s inner loop (specify, plan, implement, self-check, fresh verify, lenses, audit, as separate sessions) is the role split the January 2026 article calls the telephone game, with two exceptions that article allows: a black-box test run, and context isolation when the subtask’s context would pollute the parent. The March 2026 Labs harness is the existence proof that Anthropic has also shipped the three-role version, and then deleted part of it per model. Both pages are theirs. The dry run currently reads as if the November post had already chosen the specialist loop. The November post says the choice is open. The January post says to start with one agent. The March post says to add the evaluator when the solo model is past its reliable range, and to re-check that after each model.

---

## Corrections

### The two-revision cap

Section 8 says verify or audit may send the slice back at most twice, and the third rejection escalates to the steward. Section 16 repeats “maximum two round trips,” and also escalates when the same failure happens twice. Section 15’s Desk walkthrough is stricter: one repair, and a second miss escalates. Those three sentences are not the same rule, and none of them is a number from the sources.

What the sources do say:

- A bound exists so a loop cannot run forever. Anthropic’s December 2024 agent note says a maximum iteration count is a normal stop, and does not pick the number. SWE-agent stops at a dollar budget or at `submit`. AlphaCodium names a try-limit and, in the text read here, does not print it. The January 2026 sample uses `max_attempts = 3`, then raises. Claude Code’s stall restart gives up after five stalled attempts. That last one is a hung worker, not a failed spec.
- The Claude Code team’s own review cap, on the 15 Oct 2026 engineering post, is three rounds, then “the reviewer asks the author to step back and reconsider the approach.” After the first few passes they re-review the new diff. One pull request ran more than fifty rounds overnight before a person stopped it. After they added the cap, mean reviews to pass on their internal pull requests moved from 2.08 to 1.32. https://claude.dev/blog/solving-code-review-at-anthropic/ The action at the cap is “reconsider the approach,” which can be a new plan. It is not “stop repairing.”
- Claude Code best practices: the same correction failing twice inside one conversation is a reason to `/clear` and restate the lesson, because a third try keeps re-reading the two failures. A correction you have made twice belongs in `CLAUDE.md`. That supports “do not pile a third attempt on the same transcript.” It does not support ending the slice.
- Reflexion’s code results got worse when the loop could not see tests, and their other tasks use more than two trials. A cap of two would have cut off the procedure the paper measured.
- Cognition’s 2026 review loop is allowed to run multiple cycles. They complain that it can take a while. They do not cap it at two.
- The March 2026 Labs run that still used QA did three QA rounds on the DAW build, and the second and third rounds still found missing behavior.

A bound is supported. The published neighbors are “three attempts,” “three review rounds, then reconsider the approach,” and “two failed corrections in one transcript, then a fresh prompt that carries the lesson.” Escalating to the steward with the evidence is compatible with those pages as a human checkpoint. Choosing two, and treating the third rejection as the end of repair, is the dry run’s choice. Section 15’s “second miss” is a different choice. Sources do not decide between them.

### “No build transcript for the verifier”

Prompt P4 gives the verifier the spec, the feature list, the frozen diff, and the commit, and withholds the build transcript. The verifier re-runs the journey and is the role that sets `passes`. Read against the sources, that sentence bundles four rules that do not travel together.

1. **Who sets `passes`.** In the November 2025 prompt, the coding agent sets `passes` after it has driven the UI itself. The dry run forbids the implementer to set it and gives it to the fresh verifier. That reassignment is a later design (Claude Code’s `/code-review`, the January black-box verifier, the March evaluator). It is not the November harness.

2. **The next session must read a transcript.** The November orient steps are `pwd`, the progress file, the feature list, and `git log`, then a regression of features already marked passing. Cognition’s June 2025 rule is to share full traces with any agent whose next action depends on earlier decisions. Applying “no build transcript” to the session that orients, or to the implementer who must repair, contradicts both. The January sample does the opposite on repair: it appends the verifier’s issues to the next attempt.

3. **What a fresh reviewer is denied.** Claude Code’s `/code-review` subagent has not seen the conversation, “so it isn’t swayed by the reasoning that produced the code.” The January article’s allowed split is narrower: the verifier does not need to know why the artifact was built that way, because it only runs tests against the criteria. Cognition’s April 2026 post agrees a clean-context reviewer finds bugs the writer misses, and says the writer must still filter those findings with the context the reviewer never had. The dry run’s P4 matches this reviewer pattern when the withheld object is the author’s reasoning, and when the verifier still receives the spec, the diff, and the right to re-run. It does not match it if “build transcript” is read as “no progress notes, no git history, no failure output.”

4. **Self-check and verify are different layers, and the sources keep both.** November: the same agent tests, then flips the flag. Best practices: the author runs the command (`/verify` included); a different context reviews the diff. March 2026: the generator self-evaluates, then QA drives the app and returns findings. Reflexion: the evaluator must see the execution, and a reflection with no tests made the code worse. A fresh LLM that only re-reads the author’s story is the pattern those pages are written against. A fresh LLM that re-executes is the pattern they keep.

The audit step (a third role, re-runs a checklist, does not edit, does not inherit the verifier’s word) is not in these pages. The closest pieces are the March evaluator’s second and third QA passes, the June 2026 skeptic that drops false-positive findings, and the Claude Code team’s separate vote before a review comment is posted. None of them is the dry run’s auditor.

Lenses on every clean slice are also heavier than the June 2026 guidance. That post says a panel of five reviewers is the wrong default for ordinary coding, and best practices says a reviewer asked to find problems will always find some, so the prompt should skip style. The dry run’s “lenses only after verify is clean” keeps the cost off a slice that already failed. It does not, by itself, justify six lenses on every slice that passed.

---

## Rejected pages

| Page | Why it was not used |
| --- | --- |
| `archive228/loopkit` `docs/effective-harnesses-v03.md` | Unofficial reconstruction of the November harness. It adds failure rates and an internal debate that are not in the Anthropic post. |
| schwannden.github.io Plan-Execute-Verify tutorial; agentpatternscatalog.org “Planner-Executor-Verifier”; forwarddeployedprep.com on PEV | Secondary write-ups of the name. No lab or paper behind them. The catalog’s retry discussion is not a measurement. |
| `myksyut/pev-harness` | A third-party Claude Code plugin. Its “max 3 then a human” is that repo’s policy. |
| LangChain plan-and-execute blog, and Wang et al. ACL 2023 | Real primaries, and they stop before a verifier. Covered in the provenance section. Not an inner loop for a coding session. |
| AlphaCodium, arXiv:2401.08500 | Real primary for staged generate-run-fix. Covered in the provenance section. Competitive-programming flow, not a multi-session product harness. |
| Anthropic, “Building effective agents,” 19 Dec 2024 | Real primary for evaluator-optimizer and for “put a max iteration count on an agent.” Superseded, for this question, by the January and March 2026 pages. The page itself says its tooling description is out of date. |
| “Building agents: When to fold subagents into your main agent loop,” claude.com | Search snippets describe one loop, the plan revised in the same context, and an optional verifier after the answer. Not fetched in full, so not ranked. |
| arXiv:2609.00006, “Harness Engineering: Anatomy… of Eleven Systems” | A survey. Not used. The loops above were read from the systems’ own papers and docs. |
| OpenAI harness post, 11 Feb 2026 | Primary for the outer harness. It does not prescribe this session loop. Left to the note that covers it. |
| RayLabs, QualityLayer, Wavect, Quash, PlanVault | Cited by the dry run for review and test shape. Not re-read here. |
| MetaGPT, ChatDev | Company-scale role play. A QA role exists. They are not the inner loop of one slice, and they were not re-read. |
| Self-Debug (Chen et al., ICLR 2024) and Huang et al. on intrinsic self-correction | Reflexion cites Self-Debug and its own ablation already shows reflection without execution going backwards. Those papers were not re-read in this pass. |
| mini-swe-agent | The SWE-agent README says it has superseded SWE-agent and is much simpler, with a reported 65% on SWE-bench Verified in about 100 lines (news line dated only “July 24”). The FAQ was not read, so the NeurIPS paper remains the ranked source. |

---

## What would change the ranking

- The Claude.com “fold subagents back into the main loop” article, once read in full. If it prescribes an optional verifier after a single loop, it sits between sources 3 and 4.
- A stated try-limit integer in the AlphaCodium code or appendix, if a revision cap is going to be borrowed from that paper.
- The mini-swe-agent FAQ, if the claim is that the NeurIPS loop’s ACI no longer matters.
