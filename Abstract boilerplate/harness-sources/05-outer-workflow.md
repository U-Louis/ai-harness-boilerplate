# Outer workflow: primary sources

Scope: the path from a rough product idea to an execution plan for an AI coding agent. Glimpse, intent, research, constitution, feature enumeration, then slices. The inner implement/verify loop and CI/CD mechanics are out of scope.

Read on 27 Oct 2026. Living docs (Spec Kit, Kiro, BMad) have no single publication date; the dated claim is the page or template retrieved that day. The dry run in `ai-harness-state-of-the-art.md` was not edited.

No primary source publishes the dry run’s §7 sequence as one workflow. The sequence is a synthesis. The notes below say which piece comes from which source, and where the sources disagree.

## Ranked sources

### 1. Spec Kit documentation and templates

- **Title:** What is Spec-Driven Development?; Agentic SDD; Idea Assessment Quickstart; Spec Persistence Models; `spec-template.md`, `plan-template.md`, `tasks-template.md`
- **Org:** GitHub (Spec Kit)
- **Date:** Docs and `main` templates retrieved 27 Oct 2026. The command set has moved past the 2 Sep 2025 launch essay (source 2). Birgitta Böckeler already saw a constitution in September 2025 (source 3).
- **URL:** <https://github.github.io/spec-kit/reference/agentic-sdd.html>
- **Also:** <https://github.github.io/spec-kit/concepts/sdd.html>, <https://github.github.io/spec-kit/guides/assessment.html>, <https://github.github.io/spec-kit/concepts/spec-persistence.html>, <https://github.com/github/spec-kit/blob/main/templates/spec-template.md>, <https://github.com/github/spec-kit/blob/main/templates/plan-template.md>, <https://github.com/github/spec-kit/blob/main/templates/tasks-template.md>
- **Why primary:** This is the toolkit’s own current contract: commands, gates, and the markdown the agent is told to fill. The templates are the artifact, not a recap.

**Claims**

- Core order: `/speckit.constitution` once, then per feature `/speckit.specify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.implement` → `/speckit.converge`. Only `specify` is strictly required before `plan`. `clarify`, `checklist`, and `analyze` are optional quality gates “for anything with meaningful ambiguity.”
- Constitution: project principles every later phase is checked against. Examples in the docs are library-first, strict TDD, functional style, or quality, testing, UX, and performance. Stored as project memory (`.specify/memory/constitution.md` in the evolving-specs guide). The existing-project guide says to take principles from the repo’s README, architecture decisions, contribution guide, and CI, and not to invent standards to fill the template.
- Specify is what and why: user scenarios, not the tech stack. The spec template requires, per user story, an **Independent Test** and **Given / When / Then** acceptance scenarios, plus functional requirements, measurable success criteria, and an **Assumptions** section. Unresolved requirements are marked `[NEEDS CLARIFICATION: …]`. Out of scope appears only as an example assumption (“Mobile support is out of scope for v1”). There is no required Non-goals section.
- `clarify` asks up to five questions and writes the answers back into `spec.md`.
- The plan template is where technology, storage, performance goals, and constraints go. Unknowns there are also `NEEDS CLARIFICATION`. **Constitution Check** is a gate that must pass before Phase 0 research and is re-checked after Phase 1 design. Phase 0 writes `research.md`. Phase 1 writes `data-model.md`, `contracts/`, and `quickstart.md`. A constitution violation must name the simpler alternative rejected.
- Tasks are grouped by user story so each story can be implemented and tested on its own. Before any story, **Phase 2: Foundational** blocks all stories: database schema, auth framework, API routing, base models, error handling, logging. Inside a story the order is models, then services, then endpoints. The MVP checkpoint is “stop and validate User Story 1” only after that foundation.
- Idea assessment is an opt-in extension, separate from SDD, and it does not modify source code: intake → research → define → shape → decide, with verdicts `go`, `needs-clarification`, or `kill`. `problem.md` includes users, problem, goals, **non-goals**, success metrics, and the cost of doing nothing. Research must include evidence against the idea. Unsourced claims stay marked `ASSUMPTION`. Shape stays at concept level; architecture, data models, APIs, and tasks are out of scope. A `go` does not start SDD. The handoff is: establish a constitution if needed, then pass the decision summary to `specify`.
- Persistence is explicitly not prescribed. Three models after delivery: flow-back (any artifact may absorb a discovery), flow-forward (finished feature directories stay immutable; a change is a new directory), living spec (`spec.md` is the contract; plan and tasks are regenerated). `converge` is append-only on `tasks.md`. It does not rewrite the spec to match the code.

### 2. Spec-driven development with AI: Get started with a new open source toolkit

- **Title:** Spec-driven development with AI: Get started with a new open source toolkit
- **Org:** GitHub Blog
- **Date:** 2 Sep 2025
- **Author:** Den Delimarsky
- **URL:** <https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/>
- **Why primary:** GitHub’s own launch essay for Spec Kit. It is the public statement of the process. It is already behind the current docs.

**Claims**

- Four phases, each with one job, and “you don’t move to the next one until the current task is fully validated”: Specify (user journeys and what success looks like, not stack or app design), Plan (stack, architecture, constraints, compliance, performance targets; multiple plan variants allowed), Tasks (small chunks you can implement and test in isolation; the example is “create a user registration endpoint that validates email format,” not a full user journey), Implement.
- The human steers and verifies. The agent writes the artifacts. At each phase the human checks for omissions and edge cases before continuing.
- Security policy, compliance, design-system rules, and integration needs belong in the spec and the plan, where the agent will read them, rather than in a wiki.
- The spec is described as a living artifact and as the source of truth. The essay does not say whether that means spec-anchored or spec-as-source, and it does not mention a constitution, clarify, or a separate idea-assessment stage.

### 3. Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl

- **Title:** Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl
- **Org:** Martin Fowler’s site; author Birgitta Böckeler, Distinguished Engineer at Thoughtworks
- **Date:** 15 Oct 2025
- **URL:** <https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html>
- **Why primary:** Independent hands-on comparison by a named engineer, published on martinfowler.com. Martin Fowler did not write it. It is the piece that separates three levels the vendors blur. Her own site records a September 2026 update of the same essay; the quotes here are from the Fowler page.

**Claims**

- Three levels, her terms: **spec-first** (a spec is written and used for this task), **spec-anchored** (the spec is kept and used for later changes), **spec-as-source** (humans edit the spec, not the code). Every tool she saw was spec-first. Not all aim higher, and most are vague about maintenance.
- A spec is a structured, behavior-oriented natural-language artifact that guides an agent. A memory bank (rules, product overview, codebase map) is a different thing: it applies to every session. Specs apply to the change that creates or alters that behavior. Kiro’s memory bank is “steering” (`product.md`, `structure.md`, `tech.md`) and the spec workflow did not require those files. Spec Kit’s memory bank is the constitution: principles that apply to every change.
- Kiro, as she used it: requirements (user stories, and in her trial Given/When/Then) → design → tasks, three markdown files, tasks traced to requirement numbers. Mostly spec-first, per task.
- Spec Kit, as she used it: constitution, then specify → plan → tasks, many files per spec, checklists used as a definition of done for each step, interpreted by the model. A branch per spec looked to her like a spec for the life of a change, not the life of a feature. She therefore calls that version spec-first, despite the launch essay’s “living source of truth” language.
- Tessl’s then-private-beta framework was the only one of the three aiming at spec-anchored and trying spec-as-source. She saw a 1:1 spec-to-file mapping and a generated-file banner telling humans not to edit. Regenerating the same spec did not produce the same code.
- Her trial results: a small bug became four user stories and sixteen acceptance criteria. A 3–5 point brownfield story produced more markdown than she was willing to review; she would rather have reviewed the code. The agent researched existing classes during planning, then generated duplicates of them. One constitution article made the agent over-build. She does not think one heavyweight workflow fits every change size. She also says the profession already fails at keeping functional specs free of implementation, and that spec-as-source inherits the awkward abstraction level of model-driven development plus LLM non-determinism.

### 4. Kiro specs, steering, and the launch post

- **Title:** Introducing Kiro; Specs; Requirements-First; Quick Spec; Steering
- **Org:** Kiro (AWS). Launch post by Nikhil Swaminathan and Deepak Singh
- **Date:** Launch post 14 Jul 2025. Docs retrieved 27 Oct 2026
- **URL:** <https://kiro.dev/blog/introducing-kiro/>
- **Also:** <https://kiro.dev/docs/specs/>, <https://kiro.dev/docs/specs/feature-specs/requirements-first/>, <https://kiro.dev/docs/specs/quick-spec/>, <https://kiro.dev/docs/steering/>
- **Why primary:** Vendor’s own announcement and the current product docs for the requirements → design → tasks workflow. Earlier than Spec Kit’s public launch.

**Claims**

- Launch: a single prompt (“Add a review system for products”) is expanded into user stories with EARS acceptance criteria (WHEN / THE SYSTEM SHALL) so prompt assumptions become explicit. Design is generated from the codebase and from **approved** requirements: data flow, interfaces, schemas, API endpoints. Tasks and subtasks are sequenced by dependency and linked back to requirements, and include unit tests, integration tests, loading states, responsive layout, and accessibility. Specs are meant to stay in sync; a developer can edit code and ask Kiro to update the specs, or edit the specs and refresh the tasks.
- Current docs: every spec is three files under `.kiro/specs/<feature>/`: `requirements.md` (or `bugfix.md`), `design.md`, `tasks.md`. Requirements-first asks the human to review stories and acceptance criteria and to confirm before design. Design-first starts from architecture or non-functional constraints and derives requirements. **Quick Spec** asks clarifying questions once, then writes all three files with **no approval gate between phases**, for work the user already understands.
- Steering files under `.kiro/steering/` (`product.md`, `tech.md`, `structure.md`) are included in every interaction by default. They are project context (purpose, stack, layout), not a phase the human must accept before a spec exists. Böckeler’s September trial found the spec workflow did not depend on them.

### 5. Thoughtworks Technology Radar: spec-driven development, then GitHub Spec Kit

- **Title:** Spec-driven development (technique); GitHub Spec Kit (languages and frameworks)
- **Org:** Thoughtworks
- **Date:** 5 Nov 2025 (technique, Assess, not on the current edition); 15 Apr 2026 (Spec Kit, Assess)
- **URL:** <https://www.thoughtworks.com/radar/techniques/spec-driven-development>
- **Also:** <https://www.thoughtworks.com/en-us/radar/languages-and-frameworks/github-spec-kit>
- **Why primary:** The Radar is Thoughtworks’ own published assessment, not a blog recap of Böckeler’s article. The April 2026 blip is the only primary here that reports what teams actually put in a Spec Kit constitution on brownfield work.

**Claims**

- November 2025: SDD means a structured functional specification, then a breakdown into pieces, solutions, and tasks. The spec may be one document or several. Kiro is requirements, design, tasks. Spec Kit is a similar three-step process plus a constitution of “immutable principles.” Tessl Framework, still private beta as of September 2025, makes the spec the maintained artifact rather than the code. The workflows are elaborate and opinionated, behave differently by task size, and can emit specs too long to review. When they emit PRDs or user stories, it is unclear who is supposed to read them. “Handcrafting detailed rules for AI ultimately doesn’t scale.”
- April 2026: two camps, minimal structure versus defined workflows. Teams using Spec Kit, mostly brownfield, found a useful constitution held project scope, domain context, technology versions, coding standards, and repository structure (hexagonal or layered modules). That context kept agents inside architectural boundaries. The same teams hit instruction bloat and context rot, verbose markdown, and extra defensive checks. One team moved repeated context into skills and shortened the prompts. The lifecycle that helped was spec → plan → tasks → coding → review, because brownfield rework was coming from unclear intent, hidden assumptions, and constraints discovered late. Customizing the templates to emit fewer files helped. The blip’s judgment is that experienced engineers get the value.

### 6. Harness engineering: leveraging Codex in an agent-first world

- **Title:** Harness engineering: leveraging Codex in an agent-first world
- **Org:** OpenAI
- **Date:** 11 Feb 2026
- **Author:** Ryan Lopopolo
- **URL:** <https://openai.com/index/harness-engineering/>
- **Why primary:** OpenAI’s own account of where specs and plans live in a repository that agents actually build from. This is the source of the dry run’s `exec-plans/active` and `exec-plans/completed` layout. It is not a spec-phase tutorial.

**Claims**

- Humans steer, agents execute. Humans prioritize work, turn user feedback into acceptance criteria, and validate outcomes. In that matured setup, humans may review pull requests and are not required to.
- `AGENTS.md` is a map of about 100 lines, not the encyclopedia. The system of record is a `docs/` tree: `design-docs/` (including `core-beliefs.md`), `exec-plans/active/`, `exec-plans/completed/`, `exec-plans/tech-debt-tracker.md`, `product-specs/`, plus `PLANS.md`, `PRODUCT_SENSE.md`, `QUALITY_SCORE.md`, `RELIABILITY.md`, `SECURITY.md`, `DESIGN.md`, `FRONTEND.md`, and a top-level `ARCHITECTURE.md`.
- Small changes get an ephemeral plan. Complex work gets an execution plan with progress and a decision log, checked in. Active plans, completed plans, and known debt sit together so the next agent does not need chat history.
- Design docs are indexed and carry a verification status. Core beliefs are the agent-first operating principles. Architecture is a map of domains and package layering. A quality document grades each domain and records gaps. Golden principles are encoded as mechanical rules, with a recurring cleanup pass. That is a different artifact from Spec Kit’s constitution, even though both are “principles in the repo.”

### 7. Using PLANS.md for multi-hour problem solving

- **Title:** Using PLANS.md for multi-hour problem solving
- **Org:** OpenAI Cookbook
- **Date:** 7 Oct 2025 (registry author `aaronfriel`). The current cookbook registry marks the article `archived: true`. The live page says it may reference outdated models.
- **URL:** <https://developers.openai.com/cookbook/articles/codex_exec_plans>
- **Registry:** <https://github.com/openai/openai-cookbook/blob/main/registry.yaml>
- **Why primary:** OpenAI’s own definition of an ExecPlan: the document the February 2026 harness article points at when it says execution plans live in the repo. The prompting text is included in full in the article.

**Claims**

- A human can read the plan and check the approach **before** a long run. The plan is also a living document **during** the run.
- The implementing agent does not ask the user for the next step. It resolves ambiguity itself, commits often, and rewrites the plan as discoveries happen. A later session must be able to restart from the plan file alone.
- Acceptance is observable behavior: what the user can do, the command to run, and the output to expect. “Added a HealthCheck struct” is not acceptance. “After starting the server, this URL returns HTTP 200 with body OK” is.
- Assumptions the plan relies on are repeated inside the plan. Knowledge is embedded, not linked out. Milestones are independently verifiable increments. Required sections include Progress, Surprises & Discoveries, Decision Log, and Outcomes & Retrospective.
- Prototyping milestones and temporary parallel implementations are allowed when they reduce risk. The plan must say how the prototype is promoted or deleted.

### 8. Effective harnesses for long-running agents

- **Title:** Effective harnesses for long-running agents
- **Org:** Anthropic Engineering
- **Date:** 26 Nov 2025
- **Author:** Justin Young
- **URL:** <https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents>
- **Why primary:** The primary for a product-wide feature list written before implementation sessions. Only the enumeration and the shape of a feature belong in this note. Session startup, the pass flag, and end-to-end driving are the inner loop.

**Claims**

- The initializer, working from the user’s prompt, writes a comprehensive JSON feature list before later sessions write product code. In the claude.ai-clone experiment that was 200-plus features. Each feature is an end-to-end behavior (“a user can open a new chat, type in a query, press enter, and see an AI response”) with steps, and starts failing.
- The list exists so a later session cannot one-shot the app or look at partial progress and declare the product finished. The coding agent is told to work one feature at a time.
- There is no human acceptance step, no non-goals field, and no constitution in this write-up. The initializer expands the prompt. The article leaves open whether one general agent or several specialists is better. The two “agents” are the same harness with different first prompts.

### 9. Harness design for long-running application development

- **Title:** Harness design for long-running application development
- **Org:** Anthropic Engineering
- **Date:** 24 Mar 2026
- **Author:** Prithvi Rajasekaran
- **URL:** <https://www.anthropic.com/engineering/harness-design-long-running-apps>
- **Why primary:** Anthropic’s own later revision of the November harness. It changes what “the spec” is allowed to contain and who agrees “done” before a slice is coded. It is an autonomous-build experiment, not a recommendation to remove humans from a product team.

**Claims**

- The earlier harness “required the user to provide a detailed spec upfront.” This experiment replaces that with a planner that expands a one-to-four-sentence prompt into a product spec. The planner is told to be ambitious about scope, to stay on product context and high-level technical design, and **not** to specify granular implementation, because a wrong detail in the spec cascades. The planner was also asked to weave AI features into the product that the prompt did not name.
- The generator still takes one feature at a time. Before code for that chunk, generator and evaluator negotiate a **sprint contract**: what done means for that chunk, as testable behaviors. The contract exists because the product spec is deliberately high-level. In the reported game-maker run, a 16-feature spec was spread across ten sprints; one sprint contract had 27 criteria.
- Context resets, which the November harness depended on, were dropped for this run once the model no longer showed the same failure. The article’s general rule is to delete harness machinery the current model no longer needs.

### 10. BMad Method planning docs

- **Title:** Define Requirements and a Specification; Choose a Planning Path; Break Work into Stories and Track It; Set Up the Ticket Tree
- **Org:** BMad Method, `bmad-code-org/BMAD-METHOD`
- **Date:** Public docs retrieved 27 Oct 2026. Repo created 13 Apr 2025. Stable line named on the README that day: v6.12 (latest tag v6.12.4, 14 Oct 2026), with v6.12 docs on the `V6.12` branch. Main is described as a v7 preview whose docs change often. The pages quoted here are the public site, which already talks about migrating a v6 tree. Treat them as the current public method, not as a frozen v6.12 file.
- **URL:** <https://docs.bmad-method.org/plan/define-requirements-and-a-specification/>
- **Also:** <https://docs.bmad-method.org/plan/choose-a-planning-path/>, <https://docs.bmad-method.org/plan/break-work-into-stories-and-track-it/>, <https://docs.bmad-method.org/plan/set-up-the-ticket-tree/>
- **Why primary:** The method’s own planning docs. This is the fullest published outer arc among the sources read: idea, research, brief or PRFAQ, PRD, then a sized split into epics and stories, with assumptions and non-goals as fields. It is a community method with a moving preview line, not a vendor-neutral standard.

**Claims**

- Planning is sized. A small, clear change goes straight to build. An epic (several sessions, one outcome) gets requirements and a story breakdown. A project (many epics, or high risk) adds a brief or PRFAQ, a PRD, and optionally UX, architecture, and research. The homepage loop is Clarify → Plan → Build and verify → Learn, and Learn goes back to Plan.
- The brief is one to two pages. Rejected alternatives, options, technical constraints, and sizing sit in an addendum, not in the brief. A PRFAQ stress-tests the concept before commitment: press release, customer FAQ, internal FAQ, verdict. Market claims are checked against research. If the author cannot name a customer or a problem, the workflow sends them back to brainstorming.
- The PRD is what and why: capabilities, stable functional-requirement ids, non-functionals in their own section. Technical choices go to the addendum. It does not split stories. The fast path drafts the document and tags inferences `[ASSUMPTION]` for a person to correct. The coaching path refuses thin answers.
- The product-owner step writes the requirements the implementer will read. Each line says what is possible and what shows it was met. Non-goals from the input go to **Boundaries**. Open questions go to Notes. After the draft, the human is given every `Assumption:` and `Open question:` line to walk. A requirement the human would not defend is changed before anything is cut from it. Sparse input (“an app for hikers”) is rejected and sent back to the PRD.
- Stories are recorded in build order in the repo (`tickets.toml`), each with what it delivers, how it will be verified, what it waits on, and what is still `unknown`. The human reviews the order and chooses which stories need a checkpoint. The breakdown is “an execution plan, not a promise”: when implementation reveals a constraint, requirements are updated and the remaining stories are re-sliced, with a dated decision line. Story-level acceptance criteria are written when that story is built, unless the human asked to refine first or the run will be unattended. Early, risky, or pattern-setting stories get human attention; later repetitions may run unattended. Plans stay beside the tickets and are kept after the epic closes.

### 11. How Tessl’s products pioneer spec-driven development

- **Title:** How Tessl’s Products Pioneer Spec-Driven Development
- **Org:** Tessl
- **Date:** 16 Sep 2025
- **Author:** Guy Podjarny
- **URL:** <https://tessl.io/blog/how-tessls-products-pioneer-spec-driven-development>
- **Short launch post the same day:** <https://tessl.io/blog/announcing-tessls-products-to-unlock-the-power-of-agents/>
- **Why primary:** The founder’s own product essay, not a podcast recap. The Framework was closed beta that day. Böckeler’s file-level “generated from spec, do not edit” observation (source 3) is about that beta and is not a sentence in this essay.

**Claims**

- Without a spec, a short prompt forces the agent both to define the behavior and to implement it, and the agent will claim success when it is wrong. Specs are how the human and the agent align, and how later agents know which behavior they must not break.
- Three in-repo artifacts before and around code: a **plan** the human can review and edit before execution, which is then updated during execution as an audit trail; a **spec** in natural language written before coding; **tests** linked to the spec so regressions are mechanical. Specs and tests stay in the codebase as the long-term memory of what the product should do.
- The Spec Registry is a different thing: versioned usage specs for libraries and for a team’s own policies, installed like dependencies. It is not the product-idea workflow.
- The essay’s horizon is spec-as-source (“software will be defined by its specs”). The product it describes that day is spec-plus-plan-plus-tests, with humans still able to edit code.

### 12. How far can we push AI autonomy in code generation?

- **Title:** How far can we push AI autonomy in code generation?
- **Org:** Martin Fowler’s site; Birgitta Böckeler, Thoughtworks
- **Date:** 5 Aug 2025
- **URL:** <https://www.martinfowler.com/articles/pushing-ai-autonomy.html>
- **Why primary:** The empirical piece, earlier than the SDD survey, on what agents do with gaps and with missing non-goals. The experiment itself generated by horizontal layers; that was the setup, not a recommendation.

**Claims**

- Across repeated generations of a small Spring Boot app, the model added endpoints, features, and business calculations nobody requested. Reminding it to build only what was specified, and a reviewer agent, reduced that and did not stop it.
- Gaps were filled with assumptions, and the assumptions moved between runs. A `priority` string was one run’s `"1" | "2" | "3"` and a later run’s `"low" | "medium" | "high"` without a request to change it. She treats complete requirements as necessary and as something people are historically bad at, so autonomy stops at the requirements step.
- The generation pipeline was itself horizontal: requirements analyst, then bootstrapper, then backend designer, then persistence, service, and controller generators. The write-up does not present that layering as the cure. The conclusion is that a human supervising generation was still required.
- The model also declared success while tests were red. That claim belongs to the inner loop; it is listed here only so it is not used as evidence about specs.

## Conflicts

| Question | What the sources actually say |
| --- | --- |
| When does research happen? | Spec Kit core: inside planning, as `research.md`, after a constitution check. Spec Kit assessment (opt-in): research for and against the idea, before any specification, and it must not design the architecture. BMad: optional, and only when the stakes need it. Kiro design reads the existing codebase. The November Anthropic harness does not have a research stage. |
| What is a constitution? | Spec Kit: principles, checked as a gate on each plan, not a feature list. Thoughtworks’ April 2026 teams also put scope, domain, tech versions, and module structure there. Kiro steering is product, tech, and structure, loaded as context, not accepted as a phase. OpenAI’s analogue is `core-beliefs.md` plus golden principles, separate from `ARCHITECTURE.md` and from product specs. |
| What is “done” before code? | Spec Kit spec: Given/When/Then plus a measurable success criterion, and an independent test per story. Kiro requirements-first: EARS criteria the human confirms (Böckeler’s September trial saw Given/When/Then instead). ExecPlans: a command and an observable output. Anthropic, March 2026: a per-sprint contract of testable behaviors, because the product spec stays coarse. BMad: “what shows it was met” on the requirement; story acceptance may be written at build time. |
| Who accepts the spec? | GitHub’s September essay: the human validates each phase before the next. Current Spec Kit: that clarify gate is optional. Kiro requirements-first: the human confirms requirements before design. Kiro Quick Spec: no gate between the three files. ExecPlans: the human may read the plan first; the executing agent then resolves ambiguity and does not ask. OpenAI’s February harness: humans set priority and acceptance criteria and are not a blocking spec gate. Anthropic, November 2025: no human accept. Anthropic, March 2026: agents negotiate the sprint contract. BMad: the human walks assumptions and accepts the story breakdown; unattended runs must be refined first. |
| One feature list, or one spec at a time? | Anthropic, November 2025: the whole list, all failing, up front. Spec Kit and Kiro: one feature (or one change) at a time, and that feature’s spec contains many stories. BMad: epics and stories in build order, revised when the work teaches something. Anthropic, March 2026: a planner writes the whole spec from a short prompt and is told to add scope. |
| Vertical slices or horizontal layers? | Spec Kit’s task template does both, in a fixed order: a horizontal foundation blocks every story, and each story is then built models → services → endpoints, with an independent-test checkpoint after the story. Anthropic’s feature unit is a user-visible path, which says nothing about layering inside the slice. Böckeler’s August experiment generated persistence, then services, then controllers, and still needed a person. No source here states the dry run’s rule that a vertical slice is the only legal unit. |
| Do plans stay frozen? | ExecPlans and Tessl: the plan is updated as it is executed. Kiro: specs are updated from code, or tasks are refreshed from specs. Spec Kit: the team chooses flow-back, flow-forward, or living spec; the toolkit refuses a default. OpenAI’s harness: active plans move to completed, and the decision log stays. BMad: the plan stays, and a change in what is required updates the requirements before the remaining stories are re-cut. |

## Corrections to the dry run

The dry run already marks vertical slicing, and anything not tagged **Source**, as its own inference. These corrections stop that inference from being read back onto the sources.

1. **Vertical slices are not Spec Kit’s rule.** §2 says to build one user-visible capability through every layer, and not horizontal layers. Spec Kit’s current `tasks-template.md` does the opposite first: Phase 2 (schema, authentication framework, routing, base models, error handling, logging) blocks every user story. Inside a story the order is still model, service, endpoint. The story is then supposed to be independently testable. That is a foundation, then a story checkpoint, not “no horizontal work.” The September 2025 task example, a registration endpoint, is the same shape. Keep vertical slicing labeled as this dry run’s inference. Do not cite Spec Kit or Kiro as the source. Anthropic’s November feature is a user-visible scenario, which supports the feature-list shape and does not support a ban on layering inside the slice.

2. **§7’s order is not a published order.** Research-then-constitution-then-one-red-list-then-slices matches no single source. Spec Kit core is constitution (principles) → specify this feature → optional clarify → plan, and the research file is produced by the plan. Product-wide research with non-goals, and no architecture yet, is the opt-in assessment extension, and it does not continue into the feature list by itself. Kiro is per feature, with steering as ambient context. BMad sizes the front of the process and will skip it. Anthropic’s November initializer writes the feature list from the prompt in the same session that writes `init.sh`, with no constitution stage.

3. **A human freeze of every guess is not the OpenAI plan rule.** §6’s `exec-plans/active` and `completed` layout is the February 2026 harness article, and it is missing that article’s `tech-debt-tracker.md`, `design-docs/core-beliefs.md`, `PLANS.md`, and `PRODUCT_SENSE.md`. The behavior of an ExecPlan, from the October 2025 cookbook, is the opposite of G2: repeat assumptions inside the plan, resolve ambiguity without asking, and revise the plan during the work. G2 (stop until a guess is confirmed or explicitly accepted) is closer to Spec Kit’s `[NEEDS CLARIFICATION]` plus BMad’s assumption walk. It is a stricter rule than either of those, and it should stay labeled as this dry run’s gate, not as OpenAI’s.

4. **“Constitution” in §7 is a merge of three artifacts.** Spec Kit’s constitution is a principles file with a plan-time gate. Thoughtworks’ April 2026 report says brownfield teams also put scope, domain, versions, and module structure in it. OpenAI splits core beliefs, architecture, security, reliability, and quality grades across files. The dry run’s single `constitution.md` plus `architecture.md`, accepted together at G2 before any feature exists, is a reasonable bundle and not what any one of those sources requires. Numeric budgets and the risk-class list are not in the Spec Kit constitution examples. Performance targets in Spec Kit sit in the plan’s Technical Context.

5. **The all-red feature list is Anthropic, November 2025, and Anthropic has already revised the surrounding idea.** The list of end-to-end behaviors, all failing, written before the coding sessions, is real and is the right citation for §6 `features.json` and for stage 2. That article does not have a steward scan for invented scope. The March 2026 article says a detailed upfront spec was what they had, that wrong implementation detail in it cascades, and that the planner should stay coarse and was prompted to add features. “Done” in that later experiment is a sprint contract negotiated before the chunk, not a product-wide list accepted by a person. Stage 2’s “steward scans for invented scope” is supported by Böckeler’s August 2025 finding that models add unrequested behavior, not by Anthropic’s initializer.

6. **Non-goals are not a required field in the default Spec Kit spec.** They are required in the assessment extension’s `problem.md`, they are where BMad puts input non-goals (Boundaries), and the spec template’s Assumptions section can record an out-of-scope line. Making them a hard stop at G1 is this dry run’s rule. The August 2025 autonomy article is the reason to keep it: unstated boundaries get filled in, and the fillings change between runs.

7. **Human acceptance before code is a choice of workflow size, and the vendors now say so.** The September 2025 GitHub essay is the strong form (do not advance until the phase is validated). The current Spec Kit reference makes clarify optional. Kiro Quick Spec removes the gates. BMad writes story acceptance at build time unless the run will be unattended. Böckeler’s October 2025 trial is the cost: the specs were too long to review, and the agent still ignored them. A single G2 on every slice, including small ones, is stricter than the current primaries. The dry run’s own “one workflow for the whole product” will hit the objection Thoughtworks and Böckeler both published: one heavyweight front door is the wrong tool for a small change.

8. **Plans do not have one lifecycle.** The dry run treats the accepted spec as the contract and the plan as the thing a later session executes. That is allowed by Spec Kit’s flow-forward model and is not the default, because Spec Kit has no default. ExecPlans and Tessl update the plan during the run. Kiro will refresh specs from code. Pick one persistence model in the constitution and name it. The February 2026 article’s active → completed move is a filing rule, not a freeze rule.

## Examined, not ranked as primary

**Spec-driven development with Claude Code, Codex and Cursor.** QualityLayer. Max Ritter. 25 Oct 2026. <https://qualitylayer.dev/blog/spec-driven-development-guide>. The product manual repeats the same steps: <https://qualitylayer.dev/docs>.

This is first-party marketing. The author says he built QualityLayer around the method, that the essay shows the method itself, and that the product automates parts of it. It is not an independent report.

The steps it describes are the closest verbal match to the dry run’s outer language: agree what “done” means as observable outcomes, research in a separate session, a plan a person approves before code, a “Not doing” list, then slices whose first slice is the thinnest end-to-end version. Those phrases should not be cited as an industry finding. Where the same idea has a primary, use that instead: observable acceptance from ExecPlans and from the Spec Kit spec template, non-goals from Spec Kit assessment and from BMad, a human checkpoint from the September 2025 GitHub essay (with the later weakening in source 1), and the cost of a heavy spec from Böckeler and from the Radar.

The Tessl registry tile “spec-driven-development” (<https://tessl.io/registry/tessl-labs/spec-driven-development>) tells an agent to ask questions, write a spec, and wait for approval. It is Tessl’s own later packaging. The 16 Sep 2025 essay is the source used above; the tile was not needed for a separate rank.

OpenSpec is named beside Spec Kit in the April 2026 Radar volume’s theme text. Its own docs were not read for this note, so it is not ranked.

## Rejected pages

SEO explainers, news rewrites, and mirrors. None were used for a claim.

- <https://www.sefism.com/roadmaps/ai-coding/ship-with-an-agent/spec-and-slices/> — anonymous roadmap page. States vertical slices as settled practice.
- <https://curiosive.com/blog/breaking-large-features-into-slices-ai-coding-agents-can-finish> — vendor-style blog. Same vertical-slice claim, no primary artifact.
- <https://www.cnbc.com/2025/07/14/aws-launches-kiro-ai-coding-program.html> — news recap of the Kiro launch. Use the Kiro post.
- LinkedIn reshare of the Fowler article, and podcast pages that restate the Tessl launch essay.
- <https://www.mintlify.com/bmad-code-org/BMAD-METHOD/workflows/phase-4-implementation> — third-party rendering of BMad, not the method’s docs.
- Mirrors of the March 2026 Anthropic article (personal blogs, daily.dev, a GitHub research note). Use <https://www.anthropic.com/engineering/harness-design-long-running-apps>.
- Community prompt repositories that prescribe a walking skeleton and vertical slices (GSD, C-Spec). They are other people’s harnesses, not a published primary, and they were not used to correct the dry run.
