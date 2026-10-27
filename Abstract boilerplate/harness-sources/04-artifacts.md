# Persistent artifacts — primary sources

Read on 27 Oct 2026 for dry-run §6. The dry run was not edited. Names below are the ones the sources actually use.

This note covers files that make a repository legible to a later session: instruction files (`AGENTS.md`, `CLAUDE.md`, rules), feature lists, progress logs, execution plans, architecture and quality docs, and checks that those docs are still true. It does not cover gates, review roles, or CI shape except where a source uses them to keep an artifact honest.

## Ranked sources

### 1. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI. Ryan Lopopolo, Member of the Technical Staff. Acknowledgements name Victor Zhu and Zach Brock.
- **Date:** 11 Feb 2026.
- **URL:** <https://openai.com/index/harness-engineering/>
- **Why primary:** The team’s own account of the repository they built with Codex. This is the source of the map-plus-`docs/` layout, the failed giant `AGENTS.md`, `QUALITY_SCORE`, and doc-gardening. It is one product’s experience, not a general result.

**What they stored.** A short `AGENTS.md`, “roughly 100 lines,” is injected into context and works as a table of contents. The system of record is a structured `docs/` tree. The figure in the post places `ARCHITECTURE.md` beside `docs/`, not inside it, and then:

```text
docs/
  design-docs/          index.md, core-beliefs.md, verification status
  exec-plans/           active/, completed/, tech-debt-tracker.md
  generated/            db-schema.md
  product-specs/        index.md and one spec per product surface
  references/           vendored *-llms.txt extracts
  DESIGN.md  FRONTEND.md  PLANS.md  PRODUCT_SENSE.md
  QUALITY_SCORE.md  RELIABILITY.md  SECURITY.md
```

Design docs are indexed and carry a verification status. `QUALITY_SCORE.md` grades each product domain and architectural layer and tracks gaps over time. The post does not publish the grade labels or the rubric. Small changes get an ephemeral plan. Complex work gets a checked-in execution plan that itself holds progress and a decision log. Active plans, completed plans, and known technical debt sit together so the next run does not need chat or a Google Doc. Anything the agent cannot open from the repo “effectively doesn’t exist.”

**What failed.** They tried one big `AGENTS.md` and dropped it for four reasons: it crowded the task out of the context window; when everything was marked important, the agent pattern-matched locally; the file rotted and humans stopped maintaining it; a single blob could not be checked for coverage, freshness, ownership, or cross-links.

**Freshness.** Dedicated linters and CI check that the knowledge base is up to date, cross-linked, and structured. A recurring doc-gardening agent reads docs against code behavior and opens fix-up pull requests. Background Codex tasks also scan for deviations from “golden principles,” update the quality grades, and open small refactor pull requests. The grade file is not described as the output of a single end-of-module auditor.

**What this does not say.** It does not specify a feature-list schema, a progress-log schema, or an `init.sh`. Legibility of the running app (per-worktree boot, logs, metrics, traces) is a tooling claim, not a file in the tree above.

### 2. Using PLANS.md for multi-hour problem solving

- **Org:** OpenAI, Cookbook. The page is marked archived and may name outdated models.
- **Date:** Undated. The skeleton’s example progress timestamp is `2025-10-01`. The 11 Feb 2026 harness post is the later account of the same practice; this page is the schema that account points at.
- **URL:** <https://developers.openai.com/cookbook/articles/codex_exec_plans>
- **Why primary:** It is the OpenAI document that defines an ExecPlan’s required sections. The harness post describes execution plans in a paragraph. This page is the artifact shape.

**Shape.** `AGENTS.md` gets a short rule, not the plan. The cookbook’s example is: when writing a complex feature or a significant refactor, use an ExecPlan as described in `.agent/PLANS.md`. The plan file is for the novice who has only the working tree and that file. Required, and not optional: `Progress` (checkbox list with timestamps, updated at every stop, including a split of partial work), `Surprises & Discoveries` (observation plus evidence, ideally a test transcript), `Decision Log` (`Decision`, `Rationale`, `Date/Author`), `Outcomes & Retrospective`. Also `Purpose / Big Picture`, `Context and Orientation`, `Plan of Work`, `Concrete Steps`, `Validation and Acceptance`, `Idempotence and Recovery`.

**Constraints that fight a thin plan.** The plan must be self-contained. It must not say “as defined in the architecture doc” or point at an external blog. If a prior ExecPlan is checked in, cite that file; otherwise copy the needed context in. Acceptance is observable behavior, with the command and the expected output. The agent implements the next milestone without asking for the next step, commits frequently, and revises the plan as discoveries land. A stateless agent must be able to restart from the ExecPlan alone.

**What failed, by instruction rather than by measurement.** The page is a prompt contract, not an experiment. It exists to stop the failure where a plan is a definition the code can satisfy without a visible behavior, and the failure where the next session has to remember why a decision changed.

### 3. Effective harnesses for long-running agents

- **Org:** Anthropic Engineering. Written by Justin Young.
- **Date:** 26 Nov 2025.
- **URL:** <https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>
- **Accompanying prompts:** <https://github.com/anthropics/claude-quickstarts/blob/main/autonomous-coding/prompts/initializer_prompt.md> and the coding-agent prompt beside it. The post says the initializer and the coding agent are the same system prompt, the same tools, and the same harness. Only the first user prompt differs.

**Shape.** First session writes three artifacts and a git commit:

- `feature_list.json` at the project root. The published object is `category`, `description`, `steps` (user-visible checks), `passes: false`. No `id`. No `module`. In the claude.ai-clone demo the list held more than 200 features. The quickstart prompt hardcodes “minimum 200,” requires both `functional` and `style` categories, and says later sessions may only flip `passes`. That 200 is the demo’s prompt parameter.
- `claude-progress.txt` at the project root. The post calls it a log of what agents have done and a summary written at the end of the session. It does not specify fields.
- `init.sh`. It starts the development server so the next session does not rediscover how to boot. The quickstart also has it install dependencies and print the URL. It is not an observability stack.
- Git history. The sample session reads `git log --oneline -20`. Bad sessions are reverted to a commit, not reconstructed from the progress file.

**What failed.** Two session failures: the agent tries to one-shot the app and dies mid-feature, and a later session sees some code and declares the project done. Compaction did not fix either. A third failure: the agent marks a feature done after unit tests or `curl`, while the user-visible path is broken. They answered the third with browser automation used as a person would use the product. Puppeteer still could not see native alert dialogs, and features that depended on those dialogs stayed buggier.

**Markdown versus JSON.** They tried other formats and kept JSON because the model was less likely to overwrite or rewrite a JSON file than a Markdown file. The instruction was still required, in the post’s words: it is unacceptable to remove or edit tests. JSON did not replace that sentence. The coding agent, the same session that writes the feature, is told to change only `passes`, and only after it has tested the feature. The post does not describe a second role with exclusive write access to the flag.

**Startup.** The numbered “getting up to speed” list is three steps: `pwd`, read git logs and the progress file, read the feature list and pick the highest-priority feature that is not done. The prose and the sample transcript add `init.sh` and a basic end-to-end check before any new feature. If that check fails, the session repairs the existing app.

### 4. Prompting best practices — long-horizon state tracking

- **Org:** Anthropic, official docs. Living page. The copy read here discusses current Claude model lines, so treat it as the current prompting contract, not a dated paper.
- **URL:** <https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/claude-prompting-best-practices> (section “Workflows across multiple context windows” and “State management best practices”).
- **Why primary:** This is Anthropic’s prompt contract for the same pattern as source 3. It uses different filenames and a different status field, which matters if a harness treats either example as a schema.

**Shape.** First context window writes tests in a structured file, setup scripts, and a todo list. Later windows iterate. The suggested startup lines are: `pwd` and stay in that directory; review `progress.txt`, `tests.json`, and the git logs; run a fundamental integration test before new features. `init.sh` is the example of a script that starts servers, tests, and linters.

**JSON versus prose.** Structured state (test results, task status) goes in JSON so the model has a schema. Progress notes stay freeform text. The worked example is not a boolean `passes` flag. It is:

```json
{ "id": 1, "name": "authentication_flow", "status": "passing" }
```

with `status` of `passing`, `failing`, or `not_started`, plus totals. The progress note is a short session entry: what changed, what is next, and a reminder not to delete tests. Git is the third store: a log and a restore point.

**What this does not say.** It does not mention a separate verifier, `feature_list.json`, or `claude-progress.txt`. Those names live in source 3. Both pages agree on the split: a structured checklist that is hard to silently rewrite, a prose log, a boot script, and git.

### 5. Memory tool — multisession software development

- **Org:** Anthropic, platform docs. The tool identifier is `memory_20250818` (18 Aug 2025). The multisession section cites the 26 Nov 2025 harness post, so that section is later than the tool’s introduction.
- **URL:** <https://platform.claude.com/docs/en/agents-and-tools/tool-use/memory-tool>
- **Why primary:** This is the API pattern for memory that survives a session. It is not the same store as the git files in source 3, and the docs say so by pointing at that post for the git case study.

**Shape.** The app implements a `/memories` directory. Claude requests `view`, writes, and deletes; the application performs them and may map the prefix onto a disk or a database. Memory is not automatically a committed repo file. The injected protocol is: view memory before any other work; record status as you go; assume the context window can be reset at any moment. For software work that spans sessions, the first session creates three things before substantive work: a progress log (done and next), a feature checklist (scope), and a reference to the startup script. Later sessions read those files first. The session ends by updating the progress log. One feature at a time. Mark it complete only after end-to-end verification, not when the code is written.

**Conflict inside this page.** The general memory guidance tells Claude it may rename or delete files that are no longer relevant, and to avoid new files. The feature-list rule in source 3 forbids deleting or rewriting entries. Putting the checklist in the memory tool requires the stricter rule on top. The docs also tell the host to cap file size and to expire memory that has not been read. A feature list that must remain complete is a poor fit for an expiring memory store. The harness post’s git file is the store that matches the immutability rule.

### 6. Long-running Claude for scientific computing

- **Org:** Anthropic Science. Siddharth Mishra-Sharma, Discovery team.
- **Date:** 23 Mar 2026.
- **URL:** <https://www.anthropic.com/research/long-running-claude> (the capitalized slug returns 404).
- **Why primary:** This is the Anthropic page that says failed approaches must be written down or the next session repeats them. The November harness post does not say that.

**Shape.** Instructions and the overall plan live in a root `CLAUDE.md`. The post says Claude keeps that file in context and may edit it as the work proceeds. The progress file is called `CHANGELOG.md` here, “a sort of lab notes,” and `CLAUDE.md` tells Claude to maintain it. A useful entry tracks current status, completed tasks, failed approaches and why they failed, accuracy tables at checkpoints, and known limitations. The example is an integrator that was too stiff, with the method that replaced it. Git is the other coordination mechanism: commit and push after a meaningful unit of work, and do not commit a change that breaks an already passing test. The test oracle is a reference implementation, a numeric objective, or an existing suite, not a self-graded checklist.

**What this is not.** It is not the `feature_list.json` pattern. The plan sits in `CLAUDE.md` and the agent is allowed to revise that plan. That is a different authority model from a frozen spec.

### 7. AGENTS.md

- **Org:** Written as an OpenAI Codex convention with Amp, Jules (Google), Cursor, and Factory. The microsite’s About line lists those parties. Romain Huet (OpenAI) is the lead committer on the spec repo; his 20 Aug 2025 launch note also names Roo Code. OpenAI donated the project to the Agentic AI Foundation, a directed fund of the Linux Foundation, on 9 Dec 2025.
- **Dates:** Repository created 19 Aug 2025. Microsite live by 20 Aug 2025. Stewardship transfer 9 Dec 2025.
- **URLs:** <https://agents.md/> and <https://github.com/agentsmd/agents.md> (MIT). Donation: <https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation> and <https://openai.com/index/agentic-ai-foundation/>.
- **Why primary:** This is the format, not a recap. There is no separate schema document. The microsite FAQ is the spec.

**Shape.** A Markdown file named `AGENTS.md`. No required fields and no required headings. Suggested contents are the things a human README leaves out: project overview, build and test commands, code style, testing instructions, security considerations, commit and pull-request rules. Nested `AGENTS.md` files: the closest file to the edited path wins, and an explicit user message overrides the file. The site says agents will try to run the test commands you list. It says the OpenAI monorepo had 88 `AGENTS.md` files when that sentence was written. “Over 60,000 open-source projects” is the project’s own adoption claim, repeated by OpenAI and the Linux Foundation on 9 Dec 2025. It is not an independent census.

**What this file is.** It is the instruction body. It is not defined as a 100-line map, and it has no freshness linter, no feature list, and no progress log. The failure it exists to stop is a pile of vendor filenames (`.cursorrules`, `CLAUDE.md`, `AGENT.md`) with no predictable location.

### 8. How Claude remembers your project, and Best practices

- **Org:** Anthropic, Claude Code docs. Living pages.
- **URLs:** <https://code.claude.com/docs/en/memory> and <https://code.claude.com/docs/en/best-practices>
- **Why primary:** Official behavior of `CLAUDE.md`, `AGENTS.md`, path-scoped rules, and the instruction-file audit. Vendor docs, not a measured study.

**Shape.** `CLAUDE.md` is instructions a person writes. Auto memory is notes Claude writes. Both load at session start. Auto memory loads the first 200 lines or 25 KB. `CLAUDE.md` is context, not a control: the best-practices page says a line there is followed most of the time, and a rule that must always hold belongs in a hook, a deny rule, or a managed setting.

**Size.** Target under 200 lines per `CLAUDE.md`. Longer files cost attention on every task and adherence drops. `@path` imports are expanded into the same context. They organize the file and do not reduce its cost. Import depth caps at four. Path-scoped rules live in `.claude/rules/*.md` with a `paths` frontmatter and load when matching files are in play. Rules without `paths` load at launch, like `.claude/CLAUDE.md`. Skills hold procedures that should not sit in the always-on file: the session sees the skill’s name and description, and reads the body only when the task calls for it.

**What to put in the short file.** Commands Claude cannot infer. Places where the existing code is a bad pattern to copy. Decisions that are not in the repo. Corrections you have already had to repeat. The best-practices page lists “describe your architecture and file layout in `CLAUDE.md`” among things to stop doing, because Claude can read the tree. `/init` writes a draft; the remaining work is deletion.

**AGENTS.md interaction.** Since Claude Code v2.1.277, a repo with `AGENTS.md` and no `CLAUDE.md` or `CLAUDE.local.md` in the working directory or above it loads `AGENTS.md`. If a `CLAUDE.md` exists, Claude loads the `CLAUDE.md` files and does not load `AGENTS.md` unless the `CLAUDE.md` imports it. Nested files concatenate from the filesystem root downward, so the closer file is read last. They do not replace the parent.

**Freshness.** `/doctor prompt-audit` (v2.1.283 or later) reports instructions written for older models, references to missing files or commands, and contradictions across `CLAUDE.md`, `AGENTS.md`, rules, skills, and commands. It proposes edits and does not apply them. It audits instruction files, not product docs against code.

### 9. Rules

- **Org:** Cursor, official docs. Living page.
- **URL:** <https://cursor.com/docs/rules>
- **Why primary:** Official rule-file shape. The CLI page <https://cursor.com/docs/cli/using> adds that the CLI also reads root `AGENTS.md` and `CLAUDE.md` beside `.cursor/rules`.

**Shape.** Project rules are `.cursor/rules/**/*.mdc` with frontmatter. A plain `.md` file in that directory is ignored. Four inclusion modes come from `alwaysApply`, `description`, and `globs`:

| `alwaysApply` | `description` | `globs` | When it loads |
| --- | --- | --- | --- |
| `true` | ignored | ignored | Every chat |
| `false` | — | set | A matching file is in context |
| `false` | set | omitted | The agent pulls it in from the description |
| `false` | omitted | omitted | Only when `@`-mentioned |

`AGENTS.md` is the plain-Markdown alternative, at the root and in subdirectories. Nested files are combined with parents. The closer file wins on conflict. Team rules, then project rules, then user rules; all applicable texts are merged, and the earlier source wins when they disagree. Team rules can be enforced so a member cannot turn them off. The docs say AI guidance should not be the only security control.

**What failed, as guidance rather than as a measurement.** Keep a rule under 500 lines. Split larger rules. Do not paste a style guide; use a linter. Do not document every command the agent already knows. Do not copy code into the rule. A `@path` mention is a pointer: the contents are not inlined, in the editor, the CLI, or Cloud Agents. Something that must always be in context has to be pasted into the rule body. That pointer behavior is the opposite of Claude’s `@import`, which is inlined and spends the tokens.

### 10. Custom instructions for GitHub Copilot

- **Org:** GitHub Docs. Living pages.
- **URLs:** <https://docs.github.com/en/copilot/concepts/prompting-and-context/custom-instructions> and the path-specific how-to <https://docs.github.com/en/copilot/how-tos/configure-custom-instructions-in-your-ide/add-repository-instructions-in-your-ide>. Coding-agent support for `AGENTS.md` was announced 28 Aug 2025: <https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/>.
- **Why primary:** Official file types and precedence. Support differs by Copilot surface; the support matrix is the page to check before assuming one file feeds every Copilot feature.

**Shape.** Three different files:

- `.github/copilot-instructions.md` — repository-wide, natural-language Markdown, no required headings.
- `.github/instructions/**/*.instructions.md` — path-specific. Frontmatter `applyTo` is a comma-separated glob. Optional `excludeAgent` of `code-review` or `cloud-agent`. The concept page says these exist so the always-on file is not loaded with rules that apply to one directory or one language.
- `AGENTS.md` (anywhere; nearest file to the work wins), or a single root `CLAUDE.md` or `GEMINI.md`. These are “agent instructions.” They are not supported by every Copilot feature. They are not the custom-agent definitions under `.github/agents`.

On Copilot Chat at github.com, precedence is personal instructions, then repository instructions (path-specific, then `.github/copilot-instructions.md`, then agent files such as `AGENTS.md`), then organization instructions. The CLI and the IDE docs say most surfaces concatenate applicable files and define no general precedence. The instruction is to avoid conflicts rather than to rely on an override. Copilot CLI expands `@relative/path` inside `copilot-instructions.md`, `AGENTS.md`, and `CLAUDE.md` immediately, and only for paths inside the repo.

### 11. Agent READMEs: An Empirical Study of Context Files for Agentic Coding

- **Org:** Chatlatanagulchai, Li, Kashiwa, Reid, Thonglek, Leelaprute, Rungsawang, Manaskasemsak, Adams, Hassan, and Iida (Kasetsart University, Queen’s University, NAIST).
- **Dates:** arXiv:2511.12884, first posted Nov 2025. ACM Transactions on Software Engineering and Methodology, accepted 13 Jul 2026, online 15 Sep 2026. DOI <https://doi.org/10.1145/3840295>.
- **Why primary:** The peer-reviewed census of real `CLAUDE.md`, `AGENTS.md`, and `copilot-instructions.md` files. Descriptive. It does not measure whether the files help agents finish tasks. Their earlier Claude-only study is Chatlatanagulchai et al., PROFES 2025, “On the Use of Agentic Coding Manifests,” and this paper supersedes it for the cross-tool counts.

**What they counted.** From the AIDev dataset, 8,370 repositories with at least 5 stars. They listed the root directory only and matched `CLAUDE.md`, `AGENTS.md`, and `copilot-instructions.md` case-insensitively. That yielded 2,303 files in 1,925 repositories: 922 Claude Code, 694 Codex, 687 Copilot. Nested `AGENTS.md` files are outside the sample. The Copilot count is a root file named `copilot-instructions.md`. GitHub’s documented path is `.github/copilot-instructions.md`, so this is not a census of the official Copilot location.

**Stable findings.** The files are maintained like configuration: frequent, small additions, on a scale of days rather than a single prompt-tuning session. Median length is about 535 words (Copilot), 485 (Claude), and 335.5 (Codex). Structure is shallow: one H1, a handful of H2s, rare headings past H4. The published abstract’s content coding, on a manually labeled sample of 332 files, is testing 75.9%, implementation details 70.8%, architecture 68.1%, security 14.8%, performance 14.5%. The arXiv PDF currently served has a table at testing 75.0% and both non-functional categories at 14.5%, and a sentence that calls “build and run” (62.3%) the most prevalent category while that same table ranks testing higher. Use the gap, not the second decimal: functional instructions dominate, and security and performance appear in roughly one file in seven. The authors note that absence from the context file does not mean the project has no security tooling. It means the agent is not told.

**What this does not support.** It is not a study of 2,500 repositories, and it is not a study of feature lists, progress logs, or quality scores.

### 12. Evaluating AGENTS.md: Are Repository-Level Context Files Helpful for Coding Agents?

- **Org:** Gloaguen and Mündler (ETH Zurich), Müller and Raychev (LogicStar.ai), Vechev (ETH Zurich). Preprint.
- **Date:** 13 Feb 2026. arXiv:2602.11988.
- **URL:** <https://arxiv.org/abs/2602.11988>
- **Why primary:** The first controlled measurement of whether a context file changes issue-resolution rate. One sample per instance. Python only. It studies the always-loaded instruction file, not a feature list or a multi-day app build. They named their 138-instance set AGENTBENCH; that is not the older AgentBench agent benchmark.

**Result.** Across Claude Code (Sonnet 4.5), Codex (GPT-5.2 and GPT-5.1 mini), and Qwen Code, LLM-generated context files, produced with each product’s own init command, lowered average resolution by about 0.5 percentage points on SWE-bench Lite and about 2 points on their 138-instance set, and raised cost by about 20% and 23%. The introduction’s coarser summary is a 3% drop for generated files and a 4% gain for developer-written files. Developer-written files beat generated files for every agent they tried, and beat “no file” for every agent except Claude Code. Both kinds of file made the agent explore and test more, which is where the cost went. Agents tended to follow the files. The authors’ conclusion is that extra requirements make the task harder, and a human-written file should state only requirements the agent cannot infer, such as which tool to use.

**How to read it next to sources 1 and 3.** A context file full of style, architecture, and procedure can make a single issue harder and more expensive. That does not test Anthropic’s `feature_list.json`, which is a checklist with an edit rule, not a pile of advice loaded on every turn. It does line up with OpenAI’s decision to keep the always-loaded file short, and with Claude Code’s 200-line target.

## Conflicts

**What `AGENTS.md` contains.** The August 2025 standard says put build, test, style, and security in the file. OpenAI’s February 2026 account says that kind of encyclopedia failed, and the file should be a 100-line map. Claude Code’s best-practices page says the always-loaded file should hold only what the model cannot read off the tree, and should stay under 200 lines. Gloaguen et al. say extra requirements reduce resolution and raise cost. These can all be true at different layers: a predictable filename (the standard), a short file that is always injected (OpenAI, Claude, Gloaguen), and deep docs opened on demand (OpenAI). They cannot all be true of the same always-loaded body of text.

**Who wins when files disagree.** The AGENTS.md spec: closest file to the edit wins; the user message wins over the file. Cursor: nested `AGENTS.md` files are merged, closer wins; team rules beat project rules beat user rules. Claude Code: `CLAUDE.md` shadows `AGENTS.md` unless imported; several `CLAUDE.md` files are concatenated, closer read last. Copilot on github.com has an explicit order; Copilot CLI and the IDE docs concatenate and tell you not to conflict. A harness that assumes one precedence is wrong on at least one of these tools.

**What a pointer costs.** OpenAI’s map and Cursor’s `@file` tell the agent to open a file later. The file is not in the prompt. Claude’s `@import` is expanded immediately and costs the same as pasting. Copilot CLI also expands `@path` immediately. A “short map” written with Claude imports is a long file.

**JSON versus Markdown.** Anthropic kept the feature checklist in JSON after Markdown files were rewritten, and kept the progress log in prose. The prompting guide’s JSON uses `status: passing|failing|not_started`. The engineering post’s JSON uses `passes: false|true`. Same idea, different schema. OpenAI’s plans, quality notes, and architecture docs are Markdown on purpose. Nobody in this set says every persistent artifact should be JSON.

**Where progress lives.** Anthropic’s harness post: `claude-progress.txt`, contents unspecified, plus git. Anthropic’s prompting guide: `progress.txt`. Anthropic’s science post: `CHANGELOG.md`, including dead ends. OpenAI’s ExecPlan: a `Progress` section and a `Decision Log` inside the plan, not a sidecar file. The memory tool: whatever files the model writes under `/memories`, with deletion allowed unless you forbid it.

**Who may edit the plan.** The science post tells Claude to update `CLAUDE.md` as it learns. An ExecPlan is revised by the implementer as decisions change, and must stay self-contained. The harness post forbids editing feature descriptions and steps. A frozen spec, a living plan, and an append-only checklist are three authorities. Combining them without saying which file is which will make the next session treat a guess as a decision, or revise a test so it passes.

**Architecture docs.** OpenAI writes a top-level architecture map because dependency direction is a rule the local file does not state, and they enforce that rule with linters. Claude’s best-practices page says not to narrate the file layout in `CLAUDE.md`. A short list of allowed edges is the OpenAI artifact. A tour of the tree is the thing Claude’s page says to omit.

**Does a context file help?** Chatlatanagulchai et al. show that real files are long, hard to read, and full of build, test, and architecture prose. Gloaguen et al. show that this kind of file often fails to raise, and sometimes lowers, issue-resolution rate. OpenAI’s post says the giant file failed in their repo and the map worked. None of these is a license to delete a feature list or a progress log. Those were not the files under test.

## Corrections to the dry run

The layout in §6 is a synthesis. Several lines attribute a detail to a source that said something narrower.

1. **`features.json` is not Anthropic’s file, and the verifier is not Anthropic’s writer.** The published file is `feature_list.json` at the repo root. Fields in the post: `category`, `description`, `steps`, `passes`. The coding agent flips `passes` after it has driven the feature end to end. It is told not to edit descriptions or steps. A separate verifier with the only write right to the flag is this dry run’s rule. The prompting guide’s example is a third shape: `tests.json` with `id`, `name`, and `status`. Adding `id` and `module` is reasonable and should be marked as an extension. The “200 features” figure is the demo prompt, not a minimum for every product.

2. **JSON was chosen because Markdown checklists got overwritten.** That sentence is about the feature list only. The progress log stayed prose (`claude-progress.txt`, `progress.txt`, or `CHANGELOG.md`). Strong wording was still required. JSON is not a general replacement for specs or plans.

3. **The progress-log field list is the dry run’s.** Slice id, commit, pass, fail, and next slice are not in the November post. The dead-end requirement is real and comes from the 23 Mar 2026 science post: failed approaches and why, or the next session repeats them. That post’s filename is `CHANGELOG.md`. The November file has no published schema.

4. **`init.sh` boots the app.** In the harness post it starts the dev server. Observability per worktree is OpenAI’s legibility work, and it is not an entry in their knowledge-tree figure.

5. **Startup is three numbered steps in the post, plus boot and a regression check in the prose.** §8’s six-step orient matches the sample transcript and the prompting guide. It should cite those, not only the three-item list. “Whose module is unlocked” is the dry run’s module graph.

6. **An OpenAI ExecPlan is the progress log and the decision log.** Required sections include `Progress`, `Surprises & Discoveries`, `Decision Log`, and `Outcomes & Retrospective`. The plan is self-contained and is not supposed to defer to an architecture doc. Small work does not get one. `docs/exec-plans/.../decisions.md` as a separate file, and a thin plan beside `progress.md`, are a different design. The cookbook page that spells this out is archived.

7. **`QUALITY_SCORE.md` has no published A–D scale.** OpenAI grades domains and layers and tracks gaps. Background tasks update the grades and open refactor pull requests. The letter scale and “the auditor writes the grade” are the dry run’s.

8. **Freshness is not a schema check.** OpenAI runs linters and CI for structure, cross-links, and freshness, and a doc-gardening agent opens pull requests when docs disagree with behavior. A schema gate can pass while the prose is stale. Claude’s `/doctor prompt-audit` is a third mechanism and only covers instruction files.

9. **The 100-line map is OpenAI’s later practice, not the AGENTS.md standard.** The standard has no length and no required sections, and its examples are the instruction body. On Claude Code, a `CLAUDE.md` in the tree causes `AGENTS.md` not to load unless imported. A repo that ships only the map under the name `AGENTS.md` will not load it in a Claude Code session that also has a `CLAUDE.md`.

10. **§6’s tree mixes two systems of record.** OpenAI’s `docs/` is the knowledge base. Anthropic’s handoff files sit at the repo root under fixed names the prompt can read without a map. Putting `features.json` and `progress.md` under `docs/` is a legal convention. It is not what either source does, and a prompt that still says `feature_list.json` will miss them.

The dry run already says names are conventional. The corrections that change behavior are who may write `passes`, which file is append-only, which file is a living plan, and which file is always loaded.

## Rejected secondary pages

These were checked because they are what a search returns for this topic. They are not the sources to cite for the claims above.

- **Matt Nigh, GitHub Blog, “How to write a great agents.md: Lessons from over 2,500 repositories,” 19 Nov 2025, updated 25 Nov 2025.** <https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/>. This is the page behind the “2,500+ repositories” figure. Nigh analyzed Copilot custom-agent files, the personas that live at `.github/agents/<name>.md` with `name` and `description` frontmatter. He says so in the opening. There is no published dataset, query, or coding method. The six areas and the Always / Ask first / Never template are his recommendations for those persona files. Augment’s harness guide repeats the figure as “GitHub’s analysis of 2,500+ repositories using `AGENTS.md`” and copies the three-tier boundary table. That identification is wrong. Useful as a primary only for the Copilot custom-agent file format, and only for the examples he printed.

- **Augment Code, “Harness Engineering for AI Coding Agents” (16 Apr 2026, updated 29 Jun 2026) and “How to Build Your AGENTS.md.”** Secondary synthesis. The 2,500-repo claim in the harness guide points at Nigh’s blog, not at a study of the open standard. Their own later write-up (Zhenylenko, 22 Apr 2026, updated 18 Jun 2026) reports internal discovery rates — `AGENTS.md` read in every session they traced, unreferenced docs in under 10% — with no public dataset.

- **agentatwork, “I searched 2,500 repos for invisible instructions.”** A security scan for hidden Unicode in instruction files, not a study of artifact shape. The author corrected the sample on 29 Aug 2026: the scrape was 500 repositories from each of five star bands, not the 2,500 most-starred, and the adoption rate on the set they actually meant was 30.1%, not the published 18.4%.

- **Factory, “Factory joins AGENTS.md,” 19 Aug 2025, and Romain Huet’s 20 Aug 2025 launch note.** Participant announcements. Use them to date the working group and to see that Huet also named Roo Code. The format is the microsite and the spec repo.

- **Third-party notes that restate the Anthropic post and add fields.** Recaps that introduce `id`, `verification_steps`, or a session protocol the post does not contain. The post and the quickstart prompts are the source. The prompting guide is the other primary, and it disagrees on field names honestly.

- **Ryan Lopopolo’s later `lopopolo/harness-engineering` repository.** Notes after the OpenAI post. The 11 Feb 2026 post is the primary for what that team did.
