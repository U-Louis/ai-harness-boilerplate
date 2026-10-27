# Matt Pocock — source note

Research date: 27 October 2026. Scope: his own pages and repositories on coding-agent skills, instruction files, specs, tickets, review, and tests, 2024–2026. Read against `ai-harness-state-of-the-art.md`. That file was updated from this note on three practices: test seams confirmed before code, tracer-bullet tickets with blocking edges, and an unblended standards/spec review. Corrections already in the dry run were left in place.

“Primary” means he wrote it, on mattpocock.com, aihero.dev, or a repository he owns. A skill file on `main` is the enforceable text. An AI Hero article is his explanation of that skill. Where the two disagree, the skill file read on 27 October 2026 wins. Living pages are dated by the `datePublished` field on the page when it has one, and by this fetch.

He is an engineer and educator who teaches AI engineering through AI Hero. The tool that bears on this harness is the skill set in `mattpocock/skills`, not the Total TypeScript course.

## Ranked sources

Ranked by how directly the page should change a prompt, an artifact, or a gate. Dates on skill files are the fetch. The changelog dates are the ones printed on <https://aihero.dev/skills>.

### 1. mattpocock/skills

- **Title:** Skills For Real Engineers
- **Date:** Repository created 3 February 2026. `main` pushed 27 October 2026. Changelog on the skill index: 30 April 2026 (`grill-with-docs`), 11 May 2026 (`handoff`, `prototype`), 18 June 2026 (v1), 8 July 2026 (v1.1: `wayfinder`, `to-spec`, `to-tickets`), 5 August 2026 (v1.2), 5 October 2026 (v1.3: `implement-spec`, `pr`, `retro`, `CONTEXT.md` renamed `GLOSSARY.md`).
- **URL:** <https://github.com/mattpocock/skills>
- **Index:** <https://aihero.dev/skills>
- **Why primary:** The README is his statement of what the set is for, and the `SKILL.md` files are the instructions an agent is given. They are prompts. Installing one does not by itself fail a build.

**Claims**

- The skills are small, editable, and meant to work with any model. He names GSD, BMAD, and Spec Kit as approaches that own the process, take control away, and make a bug in the process hard to fix. That is his design stance. The README gives no measurement.
- Human-invoked skills orchestrate and are reachable when a person types them (`disable-model-invocation` on the ones checked below). Model-invoked skills hold a discipline the agent may apply when the task fits. A human-invoked skill may call a model-invoked one. It may not call another human-invoked skill.
- `/setup-matt-pocock-skills` runs once per repository and asks for the issue tracker, the triage labels, and where docs go. Later skills read that configuration. A local markdown tracker is allowed.
- The chain his docs describe is `grill-with-docs` → `to-spec` → `to-tickets` → `implement` → `code-review` → `retro`.
- Four failure stories in the README, each tied to a skill: the agent did not build what was wanted (`grill-me`, `grill-with-docs`); the agent is verbose (`GLOSSARY.md` via `grill-with-docs`); the code does not work (types, browser, and `tdd`); the codebase becomes a ball of mud (`improve-codebase-architecture`, which he says is a survey and will not untangle an old codebase by itself).

### 2. to-spec

- **Title:** `to-spec` skill; article “The /to-spec Skill”
- **Date:** Article `datePublished` 27 April 2026, `dateModified` 24 August 2026. Skill file read 27 October 2026. The 16 March 2026 article already uses the name and says it was `/to-prd`.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/to-spec/SKILL.md>
- **Article:** <https://www.aihero.dev/skills-to-spec>
- **Why primary:** This is the spec step. The article states the rules the skill file leaves implicit.

**Claims**

- The person invokes it. The agent does not. It does not interview. It synthesizes the thread, the codebase, `GLOSSARY.md`, and ADRs. A sentence the person never said is a defect.
- Before prose, it sketches test seams, prefers an existing seam, picks the highest it can, and checks them with the person. The fewer the better. The ideal number is one. `tdd` may test only at seams agreed in advance. `code-review` treats a seam nobody agreed as a finding.
- Reach for it when the work will span several sessions. When the work fits one context window, his article says to skip the spec and run `implement`. He also says nothing keeps the spec in sync, to treat it as disposable after the work ships, and to put lasting decisions in the glossary and ADRs.
- The skill template asks for an extremely long user-story list and forbids file paths, with a narrow exception for a decision-rich snippet that came from a prototype. The article’s own FAQ says the template fits a refactor poorly, and that the seams and the out-of-scope section are the parts worth reading. It does not link the ADRs it followed, and it does not search the tracker for a duplicate.

### 3. tdd

- **Title:** Test-Driven Development
- **Date:** Article `datePublished` 27 April 2026, `dateModified` 24 August 2026. Skill file read 27 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/tdd/SKILL.md>
- **Why primary:** This is the test loop `implement` is told to drive.

**Claims**

- A seam is the public boundary where behavior is observed. No test is written at an unconfirmed seam. Each proposed seam gets one line on what it catches and what it misses.
- Red before green. One seam, one test, one minimal implementation. Do not write the whole suite first. He calls that horizontal slicing: the tests then check imagined behavior.
- A tautological assertion recomputes the expected value the way the code does. Expected values come from an independent source: a known-good literal, a worked example, or the spec.
- Tests couple to behavior through the public interface. A test that breaks when the behavior is unchanged is implementation-coupled.
- Refactoring is not part of the red-green cycle. It belongs to review. The 16 March 2026 article still lists “look for refactoring candidates” inside the loop. The skill file on `main` is the later text.

### 4. to-tickets

- **Title:** To Tickets
- **Date:** Article `datePublished` 27 April 2026, `dateModified` 24 August 2026. Skill file read 27 October 2026. Formerly `/to-issues`.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/to-tickets/SKILL.md>
- **Why primary:** This is the slice-cutting rule, including the exception.

**Claims**

- Each ticket is a tracer bullet: a narrow complete path through every layer the change touches (he names schema, API, UI, and tests), demoable or verifiable on its own, sized to one fresh context window. A horizontal slice of one layer is the cut he rejects.
- Each ticket declares its blocking edges. The person is shown the breakdown and asked about granularity and the edges before anything is published. Work proceeds from the frontier: tickets whose blockers are done.
- A wide refactor is the exception. One mechanical change whose blast radius is the whole tree (his examples are a renamed column and a retyped shared symbol) is sequenced expand, then migrate in batches that stay green, then contract. When a batch cannot stay green alone, the batches share an integration branch that blocks a final integrate-and-verify ticket.
- Tickets avoid file paths, with the same prototype-snippet exception as `to-spec`. His `implement` article says the skill trusts the ticket it is given, so a horizontal ticket gets built as written.

### 5. implement

- **Title:** `implement` skill; article “The /implement Skill”
- **Date:** Article `datePublished` 7 July 2026, `dateModified` 24 August 2026. Skill file read 27 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/implement/SKILL.md>
- **Article:** <https://www.aihero.dev/skills-implement>
- **Docs copy:** <https://raw.githubusercontent.com/mattpocock/skills/main/docs/engineering/implement.md>
- **Why primary:** This is the build session. The article is stricter than the short skill file.

**Claims**

- One ticket per invocation, in a fresh context. It does not interview, does not clarify, and does not propose a different approach. The article calls that the difference from telling a fresh agent to “build this.”
- Order: read the ticket and the seams; red-green at the pre-agreed seams; fast checks often (he names typecheck and single test files); the full suite once; `code-review`; commit to the current branch. It does not create a branch, does not open a pull request, and does not close the ticket.
- The skill file does not itself agree the seams. The article says that if agreement never happens, the run is “just write the code,” and that naming the seams in the spec is what stops that.
- It commits before the review diff can see uncommitted work, because `code-review` diffs against a commit. His page records that mismatch as unfixed, and records readers who find the commit too eager.
- One field report on that page: three sessions in one checkout, one afternoon, amended each other’s commits, lost a stash, and committed on the wrong branch. `implement-spec` is his parallel path, and it uses a worktree per ticket.
- The article says a fresh `code-review` session is a valid alternative to reviewing inside the producing session, because an agent that reviews code it just wrote is biased toward it.

### 6. code-review

- **Title:** `code-review` skill; article “The /code-review Skill”
- **Date:** Article `datePublished` 7 July 2026, `dateModified` 24 August 2026. Skill file read 27 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/code-review/SKILL.md>
- **Article:** <https://www.aihero.dev/skills-code-review>
- **Why primary:** This is his review procedure.

**Claims**

- Two axes on one pinned diff (`git diff <fixed>...HEAD`), each in its own sub-agent. Standards: does it follow the repo’s documented standards. Spec: does it do what the originating issue or spec asked. The report does not merge or re-rank them. The summary names a worst issue per axis and no overall winner.
- Every standards finding cites a rule or a named smell plus the hunk. Every spec finding quotes a line of the spec. No spec: the spec axis says “no spec available” and does not invent requirements. A bad ref or an empty diff fails before the sub-agents start.
- The smell baseline is twelve smells from Fowler’s *Refactoring*, chapter 3. Each is a judgement, never a hard violation. A documented repo standard overrides the baseline. Anything a linter already enforces is skipped.
- The article says the skill does not hunt nulls, races, or off-by-ones. That job is Claude Code’s built-in review, which collides with this skill’s name. It also says findings are hypotheses until the citation is checked, the skill does not re-verify them, and running it until it returns empty does not converge.
- The shipped sub-agent prompts do not forbid calling the skill again. His page records a fan-out past fifty agents as an open bug.
- He tells the reader to prefer a fresh session. The same page says `implement` still runs the review inside the producing session.

### 7. retro, and writing-for-agents

- **Title:** `retro`; `writing-for-agents`
- **Date:** `retro` article `datePublished` 5 October 2026. Skill files read 27 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/retro/SKILL.md>
- **Also:** <https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md>
- **Why primary:** This is where a repeated miss is allowed to land.

**Claims**

- `retro` is human-invoked. It reads the session and proposes environment changes, most severe first. Categories include navigation, automated checks, coding standards, a large `AGENTS.md`, expensive tools, instructions that do not change behavior, and missing information.
- A mechanical miss (a fixed syntactic pattern, a banned API, an import shape, a file location) becomes a deterministic check. `CODING_STANDARDS.md` is for judgement the check cannot make. The implementer is under more context pressure than the reviewer, so the standards file is read at review.
- `AGENTS.md` and `CLAUDE.md` are always loaded and should usually be navigation pointers. `writing-for-agents` uses the same split: context load for what every turn pays, a pointer for the rest. It says to state the behavior you want, and to keep a prohibition for a hard guardrail you cannot phrase as the desired action.

### 8. AGENTS.md guide, and the dictionary entry

- **Title:** “A Complete Guide To AGENTS.md”; dictionary entry `AGENTS.md`
- **Date:** Guide `datePublished` 18 January 2026. Dictionary file has no date; read 27 October 2026.
- **URL:** <https://www.aihero.dev/a-complete-guide-to-agents-md>
- **Dictionary:** <https://github.com/mattpocock/dictionary-of-ai-coding/blob/main/dictionary/AGENTS.md.md>
- **Why primary:** His writing rules for the always-loaded file. The guide quotes someone else for the only number in it.

**Claims**

- The guide’s minimum is a one-sentence project description, the package manager when it is not the default, and non-standard build or typecheck commands. Anything else is a pointer, a nested file, or a skill. Do not generate a comprehensive file from an init script. Do not record file paths that move. Domain words are safer than paths, and even those drift.
- The guide’s “150–200 instructions” sentence is attributed to Kyle at Humanlayer. That article was not fetched. The number is not Pocock’s measurement.
- The guide says Claude Code reads `CLAUDE.md` and suggests a symlink from `AGENTS.md`. That sentence is the January page. The dictionary, read in October, calls `CLAUDE.md` Claude Code’s name for the same kind of brief. This note did not re-test the loader.
- The dictionary: the file holds what the agent cannot derive from the code. A correction given twice is a candidate line. A style guide goes behind a pointer. A long file costs tokens and dilutes every line in it.

### 9. grill-with-docs, domain-modeling, grilling

- **Date:** `grill-with-docs` article `datePublished` 7 July 2026, `dateModified` 24 August 2026. Changelog names the skill on 30 April 2026. Skill files read 27 October 2026. The 16 March 2026 article quotes an earlier three-sentence `grill-me`.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md>
- **Also:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/domain-modeling/SKILL.md>, <https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md>, <https://www.aihero.dev/5-agent-skills-i-use-every-day>
- **Why primary:** This is the decision interview that `to-spec` assumes has already happened.

**Claims**

- `grilling` walks a design tree in rounds. The frontier is every decision whose prerequisites are settled. The agent asks that whole frontier, with a recommended answer, then waits. Facts from the repo are the agent’s job. Decisions are the person’s. The session ends when the frontier is empty, and the agent does not act until the person confirms a shared understanding. He attributes the design-tree image to Frederick P. Brooks, *The Design of Design*.
- `grill-with-docs` is that interview plus `domain-modeling`. A resolved term is written to `GLOSSARY.md` immediately. The glossary has no implementation detail.
- An ADR is offered only when all three hold: hard to reverse, surprising without the context, and a real trade-off. Otherwise it is skipped.

### 10. wayfinder

- **Date:** Article `datePublished` 7 July 2026, `dateModified` 24 August 2026. Skill file read 27 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/wayfinder/SKILL.md>
- **Why primary:** His planning method when the route will not fit one session. It is a different artifact from a feature ticket.

**Claims**

- The map is decision tickets, one per session (research tickets may run together). A ticket asks a question. The answer is a decision. The map is done when nothing is left to decide before someone builds. It does not build, unless the map’s notes explicitly carry execution.
- Human-in-the-loop tickets are not answered by the agent on the person’s behalf. Out of scope is written down and does not graduate. Fog that cannot yet be phrased as a question stays off the ticket list.
- When the map clears, `to-spec` collapses it. Sending the map straight to `implement` drops that collapse. If the charting session finds no fog, the skill says to stop and not create a map.

### 11. diagnosing-bugs

- **Date:** Skill file read 27 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/diagnosing-bugs/SKILL.md>
- **Why primary:** His loop for a bug whose cause is unknown.

**Claims**

- Phase 1 is a command that goes red on the user’s exact symptom, is deterministic, is fast, and has already been run. Without that command the agent stops and does not hypothesize. He lists ways to build the command, from a failing test down to a human clicking through a script.
- Then minimise, then three to five falsifiable hypotheses shown to the person, then one variable at a time, then a regression test at a seam that reproduces the real bug pattern, then cleanup of the instrumentation. No correct seam is itself a finding.
- “The bug is 90% fixed” once the loop exists is his line. It is not a measured rate. Secrets in captured output are redacted before they are shown.

### 12. Deep modules

- **Title:** “How To Make Codebases AI Agents Love”; skill `codebase-design`; skill `improve-codebase-architecture`
- **Date:** Essay `datePublished` 26 February 2026. Skill files read 27 October 2026. The 16 March 2026 article says to run the survey once a week or after a surge. The README says every few days.
- **URL:** <https://www.aihero.dev/how-to-make-codebases-ai-agents-love>
- **Also:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/SKILL.md>, <https://github.com/mattpocock/skills/blob/main/skills/engineering/improve-codebase-architecture/SKILL.md>
- **Why primary:** His use of the word module. It is not the dry run’s M0–M7.

**Claims**

- He takes deep modules from John Ousterhout, *A Philosophy of Software Design*: a lot of behavior behind a small interface. The skill’s depth is leverage at the interface, and the skill explicitly rejects a line-count ratio. A seam is Michael Feathers’: a place where behavior can change without an edit in that place. The skill tells the agent not to substitute “component,” “service,” “API,” or “boundary.”
- The essay’s grey box: the person owns the interface, the agent owns the implementation, tests lock the behavior, and a passing suite means the person need not read the inside. They may still look, for taste or performance.
- `improve-codebase-architecture` is human-invoked. It weights recent hot spots, writes an HTML report outside the repo, and grills the candidate the person picks. It does not propose interfaces in the report. A candidate that contradicts an ADR is shown only when the friction is enough to reopen that ADR.

### 13. implement-spec, prototype, pr

- **Date:** `implement-spec` and `retro` articles `datePublished` 5 October 2026. `prototype` and `pr` skill files read 27 October 2026. Changelog: `prototype` announced 11 May 2026; `pr` in v1.3 on 5 October 2026.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/implement-spec/SKILL.md>
- **Also:** <https://github.com/mattpocock/skills/blob/main/skills/engineering/prototype/SKILL.md>, <https://github.com/mattpocock/skills/blob/main/skills/engineering/pr/SKILL.md>
- **Why primary:** Adjacent tools. Each one collides with a rule the dry run already made.

**Claims**

- `implement-spec` builds the whole ticket graph on one integration branch. Ready tickets run as sub-agents in separate worktrees. Communication is a pointer to the spec, the ticket, and commits. One `code-review` runs on the integrated branch. A merger sub-agent lands each ticket.
- `prototype` is throwaway code that answers one question, marked as a prototype, with no tests and no persistence unless persistence is the question. The decision is folded into the real work. The prototype is committed on a branch that is not merged. The main branch keeps the decision.
- `pr` is a pull-request body: a small visual, before-and-after evidence, and a merge-danger note (one-way or two-way door, plus blast radius). The skill file credits Dex Horthy’s `show-me` skill at Humanlayer. It is not Pocock’s own template.

## What bears on this harness

Three practices change how a prompt, an artifact, or a gate runs. They are his. They are not a consensus of the earlier dossiers.

1. **Seams are a spec field, and the implementer goes red then green at those seams.** `to-spec` confirms the seams before the prose is final. `tdd` refuses an unconfirmed seam, writes one failing test, and takes the expected value from an independent source. The dry run already froze examples at G2. The new field is the seam list: boundary, what it catches, what it misses. The implementer writes one failing test at an agreed seam, then the code. A tautological assertion does not count. Refactor stays out of that cycle.
2. **A feature slice names its blockers and its cut.** `to-tickets` makes a one-layer feature ticket illegal, requires the person to accept the breakdown, and sequences a wide mechanical change as expand, migrate, contract. `implement` does not reopen that plan. The plan schema gains `blocked by` and `cut`. GP fails a feature slice that is only one layer and is not a named exception. M0, M6, and M7 are the named exceptions. Parallel slices do not share one working tree. That last sentence rests on one field report on his implement page.
3. **Standards and spec are separate verdicts, and a repeated mechanical miss becomes a check.** `code-review` will not blend the two axes, cites the line, and says “no spec” when there is none. Findings stay hypotheses until the citation is checked, and the judgement axis is not looped until empty. `retro` puts a mechanical miss in a check, a judgement in the file the reviewer reads, and a pointer in the always-loaded map. The lens report uses the two lists. The fresh verifier stays. His own code-review page is why the producing session does not own that review.

## Conflicts

| Question | What he publishes | What this document kept |
| --- | --- | --- |
| Spec on a small change? | Skip `to-spec` when the work fits one window (`skills-to-spec`). | G2 still freezes a spec for every slice. That was already this document’s stricter choice. His page is another vote for skipping it. It was not taken. |
| Does the spec last? | Treat it as disposable after shipping. Glossary and ADRs last. | The spec stays the contract. Frozen examples are the oracles. The papers in `11-testing.md` are the stronger evidence: a model that can see faulty code writes weaker tests, and prompts do not stop test edits. |
| How many seams? | Ideal is one, the highest existing seam. | The list is required. A journey seam is required, and the spec still carries one refusal example, which may be a case at that seam. Anthropic, November 2025: unit tests and `curl` stayed green while the user-visible path was broken. His ideal count is a preference with no measurement. |
| Who reviews, and when? | `implement` runs `code-review` in the producing session, then commits. The code-review article says to prefer a fresh session. | The fresh verifier stays, and lenses still run only after that verifier is clean. The two-list report is his. The in-session review is not the pass. |
| One checkout, many writers? | `implement` is one ticket. A field report says three sessions in one checkout damaged each other’s commits. `implement-spec` uses worktrees and a merger sub-agent. | One implementer writes. Parallel slices whose paths do not overlap use separate working trees. `implement-spec` does not replace the inner loop, and the implementer still does not merge. |
| What goes in the always-loaded file? | January guide: three kinds of line, and no generated comprehensive file. Dictionary: what the agent cannot derive; a second correction is a candidate line. `retro`: pointers. | OpenAI’s short map into `docs/` stays. A file with no pointers would drop the constitution. A mechanical miss becomes a check. The map gains a pointer. Gloaguen et al. are the measurement on generated files. The Humanlayer instruction-budget number was not used. |
| What is a module? | Ousterhout depth. The skill bans the word “boundary.” Grey box: passing tests mean the person need not read the implementation. | M0–M7 stay delivery stages. “Boundary” stays for trust and contract edges. “Seam” is reserved for the test surface. A risk-class diff is still read, including the tests. |
| Planning when the route is fog? | `wayfinder`: decision tickets, no build, then `to-spec`. | The §7 sequence stays this document’s synthesis. Wayfinder is his tool for that fog. It is not a new stage. |
| ADR versus a decision log? | An ADR only when three tests all hold. | `decisions.md` still records the alternative rejected. His three-gate test was not made the schema. |
| Refactor inside red-green? | March 2026 article: look for refactor candidates in the loop. Skill file on `main`: refactoring belongs to review. | The skill file. The gardener remains the behavior-preserving pass, and it still does not automerge. |

## Rejected

Not used for a claim, or read and not carried in.

- <https://www.mattpocock.com/> and <https://www.mattpocock.com/faqs> — bio and a TypeScript FAQ. No agent-harness method.
- Total TypeScript, and the AI Hero course body. The public skill pages are the method. The course was not fetched.
- The guide’s 150–200 instruction figure. It is a Humanlayer citation. That article was not fetched.
- “The bug is 90% fixed,” “code quality has shot up dramatically,” a 63% token reduction on the skill text (changelog, 18 June 2026), and any install or star count. None is a measured effect on a product build. This note did not remeasure them.
- The `to-spec` template’s extremely long user-story list. His own FAQ says people struggle to read it, and that a refactor fits the template poorly. The dry run’s spec fields stay, plus seams.
- Twelve Fowler smells as hard gates. He says they are judgement calls. The simplicity lens was not rewritten into that list.
- The `pr` template. The file credits Dex Horthy / Humanlayer. One-way and two-way doors do not replace the risk list.
- `implement-spec` as the inner loop. It merges sub-agent work and reviews once at the end. One writer, a fresh verifier, and no self-merge stay.
- Grey-box “you don’t need to look inside.” Risk-class diffs are still read. The Effect aside in the essay is language-specific.
- `wayfinder` as a replacement for §7. Prototype code during the glimpse, which writes no code. A conversation `handoff` as a required artifact: the README one-liner is a compact of the chat, and the full skill file was not used. The durable handoff is the files already in §6.
- `diagnosing-bugs` as a new phase of every repair. A verifier reject already carries the failure. The “no hypothesis before a red command” rule is his, and it was not added as a gate. The 90% line is why it stayed out of the dry run’s rules.
- Question quotas from the March article (16, then 30 to 50). The current `grilling` skill stops when the frontier is empty.
- The January guide’s claim that Claude Code does not read `AGENTS.md`, treated as a current loader fact. The dictionary’s later wording is the one this note uses: `CLAUDE.md` is the Claude-specific name of the same brief.
- skills.sh leaderboard copy, and names that appear there but not in the engineering folder read here (`qa`, `git-guardrails-claude-code`, and the writing and misc skills). `skills/deprecated`, `skills/in-progress`, and `skills/misc` were not read. `ask-matt`, `triage`, `research`, and `wizard` were taken from the README one-liners only.
- The 19 August 2026 live with Robert C. Martin (<https://www.youtube.com/watch?v=zcLPGC-tvgk>) is already a source in the dry run for Martin’s words. This note did not re-watch it, and it is not a source for the skill method.
- SEO roundups and reposts that rephrase him, including AI Coding Daily’s 19 January 2026 summary of the AGENTS.md guide. The guide itself was fetched. The date used is the page’s `datePublished`.
