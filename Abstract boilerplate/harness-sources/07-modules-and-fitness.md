# 07 — Modules, slices, and fitness

Sources for [dry run §9](../ai-harness-state-of-the-art.md) (how each module is built, and how a change is judged against the rest of the code). Read 27 Oct 2026. The sample product in the dry run was not researched.

Ranked by how much of §9 the page actually constrains, then by how close the page is to the people who made the claim. Eleven sources. A page that only restates one of these is listed under rejected.

Two vocabularies share words and are different methods:

- **Delivery slice.** A thin user-visible path through the system, built so the system already runs. Cockburn, Freeman & Pryce, Kniberg.
- **Fitness check.** An objective test, metric, or monitor that says whether a chosen architectural characteristic still holds after a change. Ford, Parsons, Kua; applied to agents by OpenAI and, later, by Niessen and by the InfoQ cohort.

§9 uses both, and also uses “vertical slice” for delivery. Jimmy Bogard’s “vertical slice architecture” is a third thing: a way to lay out code.

---

## Ranked sources

### 1. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI. Ryan Lopopolo, Member of the Technical Staff.
- **Date:** 11 Feb 2026.
- **URL:** https://openai.com/index/harness-engineering/
- **Why primary:** The team’s own account of the repository these module and grade ideas are taken from. Later summaries add a letter scale this page does not contain.

**Claims that bear on §9:**

- Humans steer and agents execute. When work fails, the response they describe is a missing capability made legible and enforceable, written back into the repository by the agent.
- The first commit, to an empty repository in late August 2025, already contained repository structure, CI configuration, formatting rules, package manager, application framework, and `AGENTS.md`. CI is part of the initial scaffold.
- A short `AGENTS.md` is a map. The tree they publish includes `ARCHITECTURE.md`, `QUALITY_SCORE.md`, `RELIABILITY.md`, `SECURITY.md`, product specs, and execution plans.
- “A quality document grades each product domain and architectural layer, tracking gaps over time.” Background tasks “scan for deviations, update quality grades, and open targeted refactoring pull requests.” The page does not define letter grades, meanings, or a rule that a grade blocks the next module.
- Each business domain has a fixed layer order. Code may depend only forward: Types → Config → Repo → Service → Runtime → UI. Cross-cutting concerns (auth, connectors, telemetry, feature flags) enter only through Providers. Custom linters and structural tests enforce the edges. They call this an early prerequisite “with coding agents,” because the constraints are “what allows speed without decay or architectural drift.” They say this kind of architecture is usually postponed until there are hundreds of engineers.
- “Taste invariants” are a small set enforced statically. The examples they give are structured logging, schema and type naming, file-size limits, and platform-specific reliability requirements. Lint text is written to inject the remedy into the agent’s context. They also name “golden principles,” with two examples: prefer shared utility packages over hand-rolled helpers, and validate boundaries or use typed SDKs rather than probing data on a guess.
- Invariants are enforced; implementations inside them are not prescribed. Their example is parsing data at the boundary without naming the library.
- Agents replicate existing patterns, including bad ones, and “this inevitably leads to drift.” Manual Friday cleanup (they say about 20% of the week) did not scale. The replacement is the encoded principles plus the recurring cleanup, which they compare to garbage collection. Most of those pull requests are reviewed in under a minute and automerged.
- Numeric prompts such as “startup under 800 ms” or “no span over 2 s” become tractable once each worktree can boot an isolated app and the agent can query that copy’s logs, metrics, and traces.
- They do not publish a module sequence (identity, then writes, then queries, then operability, then a quality-attribute phase).

### 2. Walking skeleton

- **Org:** Alistair Cockburn. The essay is his; the book passage it quotes is from *Crystal Clear* (2004).
- **Date:** Practice found about 1994; he says he named it between then and 1997. His current bio lists the meme as 1996, “a thinly connected functioning architecture.” Web essay posted 19 Jun 2008, last modified on that copy 20 Mar 2014. The long definition is the 2004 book passage.
- **URL:** https://web.archive.org/web/20140329213351/http://alistair.cockburn.us/Walking+skeleton — the full essay. The live site no longer carries it. The 1996 gloss is at https://alistaircockburn.com/Bio.
- **Why primary:** Cockburn’s own definition and chronology. Conference slides and handbooks quote this page.

**Claims:**

- “A Walking Skeleton is a tiny implementation of the system that performs a small end-to-end function. It need not use the final architecture, but it should link together the main architectural components. The architecture and the functionality can then evolve in parallel.”
- It is permanent code, with production habits and regression tests, and it stays running. A spike is the opposite: a short throwaway used to see whether a direction is wrong.
- What counts depends on the system: one screen to the database and back; a connection across tiers; for a compiler, the simplest language element; for a business process, one simple transaction. Subsystems stay incomplete and stay hooked together.
- He pairs it with Incremental Rearchitecture. The early architecture is expected to be revised while function is added. His client-server story is “read a number, write a number,” then more function on top of a connection that already exists.
- He treats tracer bullets (Hunt and Thomas, 1999) and the Poppendiecks’ spanning application (2003) as other names for similar ideas. He later noticed Vic Basili and Joe Turner, “Iterative Enhancement,” IEEE Transactions on Software Engineering, Dec 1975, using “skeleton” for a similar incremental start. That paper is ancestry, not the method §9 is using.
- He does not define modules for identity, query, operability, or a late quality-attribute stage. On the essay’s comment thread he says a from-scratch skeleton can be “Hello World” written through the stack into one database cell, then one input back down. He does not require a refusal case in that first slice.

### 3. Growing Object-Oriented Software, Guided by Tests

- **Org:** Steve Freeman and Nat Pryce. Addison-Wesley Professional (Signature Series, Beck).
- **Date:** 12 Oct 2009. Chapter 4, “First, Test a Walking Skeleton”; the worked skeleton is chapter 10. Publisher record: https://www.informit.com/store/growing-object-oriented-software-guided-by-tests-9780321503626. Authors’ contents: https://growing-object-oriented-software.com/toc.html. A public preview of chapter 10: https://www.oreilly.com/library/view/growing-object-oriented-software/9780321574442/ch10.html.
- **Why primary:** The book that turned Cockburn’s skeleton into a test-driven start: automate build, deploy, and the end-to-end test before the first meaningful feature. They cite Cockburn 2004.

**Claims, limited to the contents, the chapter 10 preview, and the definition sentence in chapter 4:**

- A walking skeleton is “an implementation of the thinnest possible slice of real functionality that we can automatically build, deploy, and test end-to-end.” The point of that first step is the infrastructure. Functionality stays simple so attention can stay on build, deploy, and test. The first meaningful feature comes after that infrastructure exists.
- Chapter 10’s preview: the skeleton is there “to help us understand the requirements well enough to propose and validate a broad-brush system structure.” They say the structure can change later, once there is something concrete to learn from.
- Chapter 5’s process is: start each feature with an acceptance test; start testing with the simplest success case. In the worked example, “Handling Failure” is chapter 19, after a long run of success-path growth. That is evidence about their order in the example. It is not a rule that a refusal is forbidden in an early slice.
- They do not describe letter grades, a constitution module, or a quality-attribute module that waits until several features exist. Deploying and testing from the start is the point of the skeleton. A pipeline stub that does not deploy is, on their definition, not yet the skeleton.

### 4. Elephant Carpaccio facilitation guide

- **Org:** Henrik Kniberg and Alistair Cockburn. Kniberg published it; the guide says the exercise is Cockburn’s, with minor adaptations by Kniberg. Cockburn’s bio dates the exercise to 2010.
- **Date:** Guide dated 11 Jul 2013. Announcement: Henrik Kniberg, Crisp, 25 Jul 2013, https://blog.crisp.se/2013/07/25/henrikkniberg/elephant-carpaccio-facilitation-guide. Current workshop page, which describes 15–20 growth stages and does not carry the slicing rules: https://alistaircockburn.com/Elephant-Carpaccio.
- **URL:** https://docs.google.com/document/d/1TCuuu-8Mm14oxsOnlk8DqfZAA1cvtYu9WGv67Yj_sSk/pub
- **Why primary:** The inventor’s exercise, written up with him, for what “vertical slice” means as a delivery cut. This is the sense §9 uses. It is not Bogard’s package style.

**Claims:**

- “Story = vertical, testable, user-valuable. Cuts across multiple architectural layers.” Slicing makes stories thinner and keeps them vertical.
- A slice is valid only if it has a UI, an input, and an output, and is visibly different from the previous slice. A slice that is only a mockup, a data structure, or a test case does not count. Anything bigger than hello-world or echo-input-to-output is too big for the first slice.
- The first slices may optimize for risk reduction. The guide tells the facilitator to discuss a walking skeleton: key architectural components connected, “no meat.”
- Order follows the business situation. In the exercise, five states come before any discount “so we can deploy,” because state tax is a legal requirement and the discount is not. “Validation & fancy GUI comes after 5 states & 5 discounts.”
- Their sample backlog puts an error message in a later slice (“any other state gives error message”), after a hard-coded tax path that could already go live.
- Slicing decisions “are based on the business situation, not the technology aspects” on Cockburn’s current course page. The guide does not build “all writes, then all queries,” and it does not make identity, operability, or security into phases.

### 5. Building Evolutionary Architectures

- **Org:** Neal Ford, Rebecca Parsons, Patrick Kua. Second edition adds Pramod Sadalage. O’Reilly. Thoughtworks authors. Martin Fowler wrote the foreword to the first edition.
- **Date:** First edition September 2017 (O’Reilly; ACM records 8 Oct 2017), subtitle *Support Constant Change*. Second edition first release 22 Nov 2022 (copyright page also says December 2022), subtitle *Automated Software Governance*.
- **URLs:**
  - Author’s definition page: https://nealford.com/books/buildingevolutionaryarchitectures.html
  - Second-edition chapter 1, free from Thoughtworks: https://www.thoughtworks.com/content/dam/thoughtworks/documents/books/bk_building_evolutionary_architectures_second_edition_free_chapter.pdf
  - Technology Radar blip, Trial, published 30 Nov 2017, last updated 15 May 2018, not on the current radar: https://www.thoughtworks.com/radar/techniques/architectural-fitness-function
  - Fowler’s foreword, 5 Oct 2017: https://martinfowler.com/articles/evo-arch-forward.html
  - A Thoughtworks-hosted sample that contains the first edition’s JDepend dependency example: https://www.thoughtworks.com/content/dam/thoughtworks/documents/books/bk_building_evolutionary_architectures_en.pdf
- **Why primary:** The definition of an architectural fitness function used everywhere else in this note. Chapter 2 of the second edition was not re-read here; it is behind the publisher paywall. The one-sentence definition below is the author’s page and the radar, both attributing it to the book. The second-edition free chapter carries the evolutionary-computing wording and the “prevent degradation” claim.

**Claims:**

- “An evolutionary software architecture supports guided, incremental change across multiple dimensions” (second-edition chapter 1). Fowler’s foreword: the heart of the practice is small changes plus feedback loops; fitness functions monitor the architecture. Continuous delivery is what made it practical.
- Author’s page: “An architectural fitness function provides an objective integrity assessment of some architectural characteristic(s).” Mechanisms include tests, metrics, monitoring, and logging. Categories they name there include triggered versus continual, and atomic versus holistic.
- Fitness functions are chosen per system. Improving one dimension (their example is caching for performance) can harm another (security). The function exists so change does not silently harm a dimension you meant to protect.
- Identify dimensions and fitness functions at inception and keep doing it. Incremental change assumes a deployment pipeline. The pipeline is where you decide which functions run, and how often. The first-edition sample says the pipeline runs them “every single time the system changes in any way,” with different stages for atomic and holistic checks. Expensive holistic checks can sit in a later stage. They are still defined with the architecture, not invented in a cleanup phase.
- Dependency direction is one fitness function. The first-edition sample is a JDepend unit test: persistence and web may depend on util; an illegal import fails before commit. Component cycles are the case they highlight. Ownership can sit with the team or with a shared group such as security. They prefer the executable test to a written guideline.
- A letter grade of a domain is not this definition. The function is an objective assessment of a characteristic. A scorecard can sit beside it. It does not replace it.
- The second-edition free chapter calls the same idea “architecture characteristics” and says organizations also say nonfunctional requirements, cross-cutting requirements, and system quality attributes. Those are one concern under several names.

### 6. Fitness function-driven development

- **Org:** Thoughtworks. Paula Paul and Rosemary Wang.
- **Date:** 11 Jan 2019.
- **URL:** https://www.thoughtworks.com/insights/articles/fitness-function-driven-development
- **Why primary:** Named Thoughtworks practitioners, writing after the book, on how fitness functions sit in the delivery pipeline. This is the page that ties the term “architectural drift” to those functions before the agent literature.

**Claims:**

- They quote the 2017 book for evolutionary architecture, then treat fitness functions as the analogue of TDD for architectural aims: feedback while the change is being made, “rather than after the fact.”
- Their worked examples are pipeline checks for code quality, resiliency under latency, observability (metrics, parseable logs, correlation or tracing ids), performance, compliance, secrets and known vulnerabilities, and operability (runbook, alerts, tracing ids). Logging is their example of a quality that teams otherwise add too late.
- They use the phrase architectural drift for what these checks are there to catch, and they say expressing the checks as code avoids “organic architectural drift.”
- A maintainability rating appears as a numeric gate inside a test (`B` as a rating threshold). That is a threshold on a tool’s metric. It is not a per-module letter that licenses the next feature.
- They do not discuss coding agents. The transfer to agents is later work (sources 1, 9, and 10).

### 7. Hexagonal architecture (ports and adapters)

- **Org:** Alistair Cockburn. Humans and Technology Technical Report 2005.02.
- **Date:** 4 Sep 2005 (v0.9).
- **URL:** https://alistair.cockburn.us/hexagonal-architecture/
- **Why primary:** His pattern for the dependency shape §9’s constitution actually states (domain behavior not tied to delivery or storage). It is a different graph from OpenAI’s Types → UI stack.

**Claims:**

- Intent: the application can be driven by users, programs, automated tests, or batch scripts, and developed without its eventual UI or database.
- The rule he states: code for the inside does not leak to the outside. The application talks through ports; technology-specific adapters sit outside. A UI and a test harness are adapters on a driving port. A database, a flat file, and an in-memory mock are adapters on a driven port. The conversation across the port stays stable when the adapter changes.
- He is explicit that a one-dimensional layered drawing is how business logic leaks across boundaries, because there is no detection mechanism. The pattern exists so tests can catch that leak.
- Several adapters per port is the design. “One storage mechanism, forever” is stronger than this pattern. The pattern wants one port protocol and a swappable adapter, including a mock, so the inside can run with the real database absent.
- His development sequence in the report is: tests through a port against a constant or mock; then a UI; then integration against a real database; then live use. That is an isolation sequence for one application. It is not a product plan of identity, then writes, then queries.

### 8. Vertical Slice Architecture

- **Org:** Jimmy Bogard, on his own blog.
- **Date:** 19 Apr 2018.
- **URL:** https://www.jimmybogard.com/vertical-slice-architecture/
- **Why primary:** The page that gave this name to a code-organization style. Cited so §9 is not backed by the wrong “vertical slice.”

**Claims:**

- He had been organizing by vertical slices for years and names the style here, against onion and layered architectures, which he calls monolithic even when the layers are drawn as concentric circles.
- A slice is one request, front to back. Couple along the axis of change. Minimize coupling between slices. Commands and queries fall out as different requests (he presents that as CQRS arriving naturally).
- He argues against rigid dependency management of the form “Controller MUST talk to a Service that MUST use a Repository,” and against shared layer abstractions that exist only to satisfy the layering. New features should mostly add code rather than edit a shared stack.
- This is a structural claim about cohesion. It is not a build order, a walking skeleton, or a fitness function. It pulls against both OpenAI’s mandatory forward-only layers and a rule that every slice must reuse one shared service layer. It does not forbid a port. It forbids making the layer ceremony the architecture.

### 9. Fitness functions for AI-generated code

- **Org:** Lukas Niessen, writing as creator and maintainer on archunit.net. That site is his multi-language architecture-test family (ArchUnitTS and others). It is not the Java library at https://www.archunit.org/, which Peter Gafert created. Niessen’s own team page says the TypeScript library was started because teams wanted something with the practical depth of the Java library. Treat them as related tools and different publications.
- **Date:** 20 Aug 2026.
- **URL:** https://www.archunit.net/blog/fitness-functions-for-ai-generated-code/
- **Why primary:** A tool author’s essay on what changes when agents produce structural change faster than people can read every edge. It is vendor-adjacent. It is still the author speaking for this line of tools, and it is specific about agents. It is not a substitute for Ford.

**Claims:**

- A change can compile and pass behavioral tests and still move dependencies the wrong way, close a cycle, or put a capability in the nearest folder. Behavioral tests, types, and ordinary linters answer different questions from a graph-wide architecture test.
- The useful loop is the same command locally and in CI, with the failure naming the exact dependency path, so the next edit is constrained.
- Deterministic facts (a forbidden edge, a cycle, an empty selector, a size threshold) can block a merge. Semantic duplication and “too many responsibilities” are interpretive. He says to keep those out of the deterministic policy.
- The agent is optimizing for a green suite. If it can edit the rule, raise a threshold, widen an ignore, or delete the CI step, it can weaken the check. Rules, ignore files, and workflows need an owner who is not the author of the change. A rationale (“because”) belongs with the rule so a real decision can be distinguished from a workaround.
- Brownfield adoption starts with one clean boundary or one painful area, and a ceiling near the current measurement, tightened as cleanup lands. A permanently red suite teaches no one.
- The same graph supports a pull-request gate and a scheduled cleanup of one bounded finding. He calls that architecture garbage collection. The cleanup is small and behavior-preserving.
- Prefer a few strong invariants (domain independence, allowed direction, cycle freedom, integration ownership, a measurable threshold). Encode a preference only when violating it has a real cost.

### 10. Agentic fitness functions: extending evolutionary architecture beyond deterministic rules

- **Org:** InfoQ. Hemant Kumar Mahato, Łukasz Sieczkowski, Vijayasenthilkumar Kuppusamy. Written as a capstone of the InfoQ Certified Architect Program. Luca Mezzalira is listed with them on the byline.
- **Date:** 17 Aug 2026.
- **URL:** https://www.infoq.com/articles/agentic-fitness-functions-evolutionary-architecture/
- **Why primary:** A first-party essay that extends Ford into agent judgment. It is a cohort capstone, not a Thoughtworks or lab standard. Ranked for the distinction it draws, and for that limit.

**Claims:**

- Deterministic fitness functions stay the enforcement mechanism for dependency direction, contract shape, latency budgets, security posture, and policy.
- An “agentic fitness function,” as they define it, is a calibrated agent applying a versioned rubric to a bounded evidence pack and returning a structured verdict (score, confidence, rationale, evidence). It starts as an advisory signal. Low confidence, judge disagreement, or high blast radius escalates to a person.
- The cases they give it are ones a dependency rule cannot settle: boundary fidelity, semantic contract drift, whether an ADR’s assumptions still hold.
- “A single architecture quality score is not actionable; a criterion-level verdict is.” Repeated, agreed findings should be promoted into a deterministic rule.
- They cite Ford, Parsons, Kua, and Sadalage, second edition, 2022. They do not claim OpenAI uses this pattern.

### 11. Effective harnesses for long-running agents

- **Org:** Anthropic Engineering. Written by Justin Young.
- **Date:** 26 Nov 2025.
- **URL:** https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents
- **Why primary:** The vendor’s account of incremental agent construction. Included because §9 is about building module by module with agents. The page constrains session shape. It does not constrain architecture fitness or the M0–M7 order.

**Claims:**

- One coding session works on one feature, then leaves a commit and a progress note. The initializer, once, writes a feature list that starts failing, a progress log, and `init.sh`.
- Every later session reads progress and git history, chooses the highest-priority feature that is not done, starts the app, and checks that fundamental behavior still works before new work. If that check fails, the session repairs it.
- End-to-end means driving the product as a user. Unit tests and ad-hoc calls left features broken in their experiment until they required that.
- They leave open whether one general agent or several specialists is better. They do not describe dependency linters, taste invariants, quality grades, or a module taxonomy.

---

## What in §9 is classic engineering, and what is agent-specific

| §9 idea | Where it comes from |
| --- | --- |
| M1 is one thin end-to-end path that links the main components and is kept | Cockburn 2004/2008. Classic. |
| That path is automatically built, deployed, and tested, and the first meaningful behavior follows the skeleton | Freeman & Pryce 2009. Classic. OpenAI’s first commit also includes CI, so the agent account agrees. |
| Each later slice is vertical: user-visible input and output, thinner than a layer | Kniberg & Cockburn 2013. Classic. |
| Order of slices follows business risk and value. Early slices may exist to reduce technical risk. Validation is its own later slice in their exercise | Kniberg & Cockburn. Classic, and explicitly not a technology phase plan. |
| Simplest success case first; acceptance test per feature | Freeman & Pryce, chapter 5 titles. Classic. |
| Architecture may be revised while the skeleton stays up | Cockburn, Incremental Rearchitecture. Classic. |
| Inside does not depend on UI or database; storage is behind a port; tests drive the application | Cockburn 2005, hexagonal. Classic. |
| Allowed dependency edges are an executable check on every change; cycles fail the build; holistic checks (load, contract) can run at a coarser cadence | Ford, Parsons, Kua 2017/2022. Classic. |
| Observability, performance, security, and operability are fitness functions in the pipeline while features are built | Paul & Wang 2019. Classic. |
| Already-green core behavior is re-checked before new work, one feature per session | Anthropic 2025. Agent-specific packaging of ordinary regression. The “before new work” part is also what their “getting up to speed” steps say. |
| M0 as a module: edges, taste, and bars before feature speed; custom linters whose errors tell the agent the fix; a quality document per domain and layer; recurring cleanup because agents copy bad patterns | OpenAI 2026. Agent-specific. The underlying ideas of an early dependency rule and a pipeline are classic. The reason they give for doing the rigid form immediately is agent throughput. |
| Layer graph Types → Config → Repo → Service → Runtime → UI, cross-cutting only through Providers | OpenAI’s application. Agent-specific instance. §9’s constitution graph is the hexagonal one, not this one. |
| Do not let the author of a change weaken the architecture check | Niessen 2026, in agent terms. The classic form is Ford’s executable test that developers do not satisfy by editing the guideline. §9 already routes linter and oracle edits through a human risk class. |
| Judgment-heavy questions (did this reuse the one auth mechanism, is this a second concept) sit beside deterministic gates, and start advisory | InfoQ cohort 2026. Agent-specific proposal, not an established standard. |
| M0–M7 as the module types and that sequence; happy path plus one refusal inside the first slice; query only after the write lifecycle; operability and “quality attributes” as their own modules; A–D meanings; M1 may be C; M7 promotes nothing below B; M2 and M3 must be A or B before M4 | Dry run. Not in the sources above. |

Bogard 2018 is classic engineering and does not support the module table. It supports a different structure: one request, one slice of code, few shared layers.

---

## Corrections

1. **The letter scale is the dry run’s.** OpenAI says a quality document grades each product domain and architectural layer, and that background tasks update those grades. The A/B/C/D meanings, “M1 may ship at C,” “M7 may not promote anything below B,” and “M2 and M3 must be A or B before M4” are not in that post. A public template that uses A–D (rejected below) uses still different meanings. Ford’s fitness function is an objective check of a characteristic. The InfoQ cohort, which is only a capstone, says a single architecture quality score is not an actionable gate. A grade file can tell a gardener where to look. It is not, on these sources, the mechanism that unlocks the next module.

2. **Structural fitness is part of the skeleton, not of M6.** Ford: choose the functions at inception and evaluate them in the pipeline whenever the system changes. Paul & Wang: the point is feedback during development, and logging-as-an-afterthought is the failure mode. OpenAI: the layer rules are an early prerequisite, and the first commit already has CI. GOOS: if you cannot automatically build, deploy, and test the thin slice, you do not have the skeleton yet. §9 already runs dependency fitness on every slice, and it is right that a latency budget needs a running path and a legible trace (OpenAI’s 800 ms / 2 s example). The correction is to describe M6 as the campaign that measures runtime budgets, and to keep dependency direction, boundary parsing, structured logging, and secret checks in force from M1. “Do not build M6 before M1 because there is nothing to measure” covers load and fan-out. It does not cover those structural checks.

3. **“Vertical slice” in §9 is the delivery cut.** Cockburn’s skeleton, the GOOS thin slice, and the Kniberg/Cockburn rule (UI, input, output, visibly different) match the dry run’s sentence “one user-visible capability through every layer.” Bogard’s 2018 post is a code layout that couples along one request and treats mandatory service/repository layers as a smell. Citing Bogard as the source of the module order would be the wrong paper. Citing him as a requirement for OpenAI’s forward-only stack would contradict him.

4. **The dependency rule in the constitution is hexagonal, not the OpenAI stack.** Cockburn 2005: the inside does not know the UI or the database; one port can have several adapters, including a mock. OpenAI: forward-only Types → Config → Repo → Service → Runtime → UI, cross-cutting only through Providers, enforced by custom linters. Ford: whichever graph you choose, encode the allowed edges as a test that fails on the violating import and on cycles. §9 can keep the hexagonal edges. The OpenAI citation supports “make the edges mechanical, early, and remedial in the error text.” It does not supply those particular edges. “No new storage mechanism” is closer to OpenAI’s golden principles (one shared helper, no second hand-rolled path) than to hexagonal architecture, which expects the real store and the mock to be interchangeable adapters.

5. **The inside-a-module order is tighter than the classics, and looser than Anthropic at one point.** GOOS says to start a feature with the simplest success case. The carpaccio guide puts validation after the slices that make the exercise deployable, and puts a refusal in a later sample slice. Cockburn’s skeleton stories are one small function, kept green, with flesh added after. §9’s “happy path plus one refusal, then boundary cases” is a reasonable dry-run rule. It is not Cockburn’s or GOOS’s published sequence. Separately, §9 lists cross-module regression as the third step inside the module. Anthropic’s session starts by proving the core journey still works, and only then takes new work. §8 of the dry run already says that. The §9 list should match the session start, or a reader will treat regression as something to do after the new boundary cases.

6. **M2–M5 and M7 are a product plan for a multi-user workflow.** Nothing in Cockburn, GOOS, carpaccio, Ford, or Anthropic says to finish identity, then all state changes, then query, then operability, then release. Carpaccio’s only ordering principle is current business risk and value, with the first slices allowed to be pure risk reduction (the skeleton). Bogard separates commands from queries in the code of each request. He does not say “build every write before any read.” “Do not build the list if it would freeze the wrong lifecycle” can stay as a reason for this product. It should stay marked as inference. OpenAI’s narrative also cuts against holding the real pipeline until M7: CI configuration is in their initial scaffold. A promotion rehearsal can still be a late slice. The deploy-and-test path the skeleton needs is the GOOS skeleton, not the M7 rehearsal.

7. **Cockburn does not freeze the skeleton’s architecture.** He writes that it need not be the final architecture, and that incremental rearchitecture is likely, while the end-to-end path stays up. OpenAI freezes a layer graph early because agents will otherwise drift. §9 is with OpenAI: edges hold unless the constitution changes in a reviewed slice. That is a coherent agent rule. The citation for it is OpenAI, plus Ford’s “protect the characteristic.” The citation for “M1’s structure is provisional and will be revised in public” would be Cockburn. §9 currently reads as if those were the same claim.

8. **Plan alignment and the grade are different kinds of check from a fitness function.** “Does this import violate an allowed edge?” is Ford’s function, and Niessen’s deterministic gate. “Did this slice reuse the one auth path?” is a judgment. The InfoQ cohort would start that as a rubric with confidence and escalation, then promote a repeated finding into a linter. §9’s table can keep the row. Calling every row a fitness function, including the grade, stretches the 2017 definition. Paul & Wang’s `B` rating is a number in a test, which shows the word “grade” appearing inside a real fitness function only when it is a measured threshold.

9. **Wording, if §9 is ever aligned with the OpenAI sentences.** They say “taste invariants” and “golden principles,” not a published rubric. Their cleanup pull requests are mostly automerged. §9’s gardener, with the same gates as a feature, is the dry run’s stricter posture. That difference is already the point of the dry run’s opening. It should not be described as OpenAI’s merge policy.

---

## Rejected pages

These were opened or returned in search and are not sources for §9.

- **Fan restatements of the OpenAI post.** https://zby.github.io/commonplace/sources/harness-engineering-leveraging-codex-agent-first-world/ and https://github.com/dudgeon/ai-pm/blob/main/knowledge-base/horizontal/agents/system-design/architecture/mechanical-architecture-enforcement.md repeat the layer graph and the lint-as-remedy idea. Use the OpenAI URL.
- **A quality-grade template that is not OpenAI’s.** https://github.com/walkinglabs/learn-harness-engineering/blob/main/docs/en/resources/openai-advanced/repo-template/docs/QUALITY_SCORE.md defines A–D as verified / minor gaps / partial / broken. Those sentences are the template author’s. They are also different from §9’s A–D. Neither scale is in the OpenAI post.
- **Ryan Lopopolo’s public notes repository**, as surfaced in search (https://github.com/lopopolo/harness-engineering). Not used. The 11 Feb 2026 OpenAI post is the source for what that team published.
- **Secondary walking-skeleton explainers.** The C2 wiki mirror (https://kidneybone.com/c2/wiki/WalkingSkeleton), Tech Lead Handbook, Smart Gecko Academy, Ben Christel’s notes, the 2012 ACCU slides (https://accu.org/conf-docs/PDFs_2012/Walking_skeleton.pdf), Paul Grenyer’s InfoQ talk, and the Stack Exchange answer. They quote Cockburn or GOOS. Some add “production” and “CD pipeline” without separating Cockburn 2004 from GOOS 2009.
- **developertoolkit.ai, “Architecture fitness functions.”** https://developertoolkit.ai/en/workflows/test/fitness-functions/ A practice guide: which linters, how to ratchet, how to stop an agent editing the gate. Useful as a procedure, derivative of Ford plus the agent posts. The dry run already cites that site for a different claim. It is not a primary for §9.
- **Full-text copies of GOOS.** A PDF turned up on a third-party host. It was not used as the bibliographic source. Cite the book via the authors’ contents and the Addison-Wesley record.
- **Java ArchUnit (archunit.org), as an agent paper.** The library is a real way to write the kind of test in Ford’s JDepend example. There is no Gafert essay in this set about agents. Do not merge it with Niessen’s 20 Aug 2026 post on archunit.net.
- **Martin Fowler, “Governing data products using fitness functions.”** https://martinfowler.com/articles/fitness-functions-data-products.html Same term, applied to data-mesh metadata and catalog assertions. Off the module-order question. His 2017 foreword is the Fowler page that belongs here, and it is filed under source 5.
- **Ford and Parsons, “Microservices as an Evolutionary Architecture,” 11 Mar 2016.** https://www.thoughtworks.com/insights/blog/microservices-evolutionary-architecture An earlier, looser sketch. The book’s definition replaces it for this note.
- **Cockburn’s current Elephant Carpaccio and Hexagonal workshop pages** on alistaircockburn.com. Course advertisements. The 2005 report and the 2013 guide are the texts. The bio page is used only for his own dates.
