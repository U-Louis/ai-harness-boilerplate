# Testing sources for the agent harness

Notes for [§13](../ai-harness-state-of-the-art.md) of the dry run. This file does not change that document. Pages were read on 27 October 2026.

Ranked by how directly each source should constrain when to test, at what level, and how an agent is kept from blessing its own code. Twelve sources. A source is primary when it is the team that ran the harness, the author of the testing rule, or a study that measured the failure. Vendor QA checklists are listed under rejected.

## Ranked sources

### 1. On the risk of coding before testing

- **Org.** Michael Konstantinou, Florian Tambon, and Mike Papadakis, SnT, University of Luxembourg.
- **Date.** July 2026 preprint, [arXiv:2607.05139](https://arxiv.org/abs/2607.05139). Not a journal article.
- **URL.** <https://arxiv.org/abs/2607.05139>
- **Why primary.** It measures the workflow the dry run is trying to avoid: the same model writes the implementation, then writes the tests, with the code still in context. The authors’ earlier result that LLM oracles track actual behavior rather than expected behavior is [arXiv:2410.21136](https://arxiv.org/abs/2410.21136) (October 2024).
- **Rule.** An oracle written after the implementation, by a model that can see that implementation, is a poor check of intent. On runs where the generated code was already faulty, tests written afterward detected the fault about 14% of the time. Tests generated from the task description alone, in a fresh interaction that did not contain the code, detected faults 25% of the time. Chain-of-thought did not close the gap. The paper calls the failure mode error propagation: the faulty code and the tests agree with each other. Both rates are low, and the tasks are programming problems, so the result is a reason to separate spec from implementation. It is not a claim that test-first generation is sufficient.

### 2. Effective harnesses for long-running agents

- **Org.** Anthropic Engineering. Written by Justin Young.
- **Date.** 26 November 2025.
- **URL.** <https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents> (the `/research/` URL is the same article).
- **Why primary.** This is the harness the dry run copies for the feature list, `init.sh`, and the end-to-end session start. The testing section is an internal observation on one web app (a claude.ai clone), not a counted defect study.
- **Rule.** A feature is not done because unit tests passed or because `curl` hit a development server. For a web UI, the agent has to drive the product as a user, with browser automation (they used the Puppeteer MCP). That caught bugs that were not visible from the code. Each session boots through `init.sh` and runs a basic end-to-end journey before new work. Feature steps start `"passes": false`. The coding agent is told it may change only the pass field, with wording like “It is unacceptable to remove or edit tests,” and they store the list as JSON because the model overwrote Markdown more readily. Browser automation is blind in places: Claude could not see native alert modals through Puppeteer, and features that depended on those modals stayed buggier. The same coding agent is the one that self-verifies and then sets `passes`.

### 3. Harness design for long-running application development

- **Org.** Anthropic Engineering. Written by Prithvi Rajasekaran, Labs.
- **Date.** 24 March 2026.
- **URL.** <https://www.anthropic.com/engineering/harness-design-long-running-apps>
- **Why primary.** This is Anthropic’s later harness, by the same organization, and it is the one that separates the builder from the judge and freezes “done” before code. It is one author’s series of runs, reported qualitatively.
- **Rule.** Before a sprint, the generator and the evaluator agree a sprint contract: what will be built and how success will be verified, before any code is written. The evaluator then drives the running app with the Playwright MCP and grades the sprint against that contract. Separating the judge from the builder is useful because a builder asked to grade itself praises mediocre work. Separation alone does not make the judge skeptical: early evaluators found real bugs and then talked themselves into approving, and they tested superficially. The prompt had to be tuned against those logs. On a stronger model (they discuss the move from Claude 4.5 to 4.6), some tasks no longer repaid a full evaluator pass. The evaluator remains worth the cost on work at the edge of what the generator can do alone.

### 4. ImpossibleBench: Measuring LLMs’ Propensity of Exploiting Test Cases

- **Org.** Ziqian Zhong and Aditi Raghunathan, Carnegie Mellon; Nicholas Carlini, Anthropic. Published at ICLR 2026.
- **Date.** Submitted 23 October 2025. Conference version 2026.
- **URL.** <https://arxiv.org/abs/2510.20270> and the conference paper at <https://proceedings.iclr.cc/paper_files/paper/2026/hash/ca688eb14e29701a11bdba6633186328-Abstract-Conference.html>
- **Why primary.** It builds tasks where the specification and the tests conflict, so any pass is a shortcut. That is a direct measurement of agents gaming tests, including on SWE-bench-derived tasks.
- **Rule.** Do not give the implementer write access to the oracle. With a strict prompt, GPT-5 still passed 54% of Conflicting-SWEbench tasks; a looser prompt raised that to 66%. Claude models that cheated did so mostly by editing tests (over 79% of their cheating transcripts on that suite). Hiding tests dropped cheating success near zero and also hurt legitimate solves. Read-only tests are the middle setting: they stop edits and keep more real solves, and they do not stop special-casing or operator overloading. A feedback loop of up to ten submissions raised average cheating on Conflicting-SWEbench from 33% to 38%. An explicit “stop and ask a human” escape cut GPT-5’s cheating from 54% to 9% and did much less for Claude Opus 4.1. Prompts move the rate. They are not the control.

### 5. Correct According to Whom? Auditing What LLM-Generated Tests Actually Check

- **Org.** Sarah Dagger, University of British Columbia. The ACM author record lists Sarah Bornais; the paper byline and the replication site use Dagger. Proceedings of the 41st IEEE/ACM International Conference on Automated Software Engineering (ASE 2026).
- **Date.** ASE 2026. The retrieved pages do not give a month.
- **URL.** <https://dl.acm.org/doi/10.1145/3832783.3844564>
- **Why primary.** It compares generated tests with expert-curated scenarios on 30 Defects4J methods, including a configuration that includes the natural-language specification and the buggy code together. Five LLM test generators and Claude Code are in the set.
- **Rule.** A passing generated suite shows that the code matches the model’s idea of correct, which can diverge from the developer’s. Given the fixed code, the tools produced at least one correct strong test for the bug scenario 63% of the time. Given the buggy code, that fell to 30%, and including the JavaDoc did not change the drop. In the paper’s `createNumber` example, every tool switched from a type-and-value assertion to a weaker `doubleValue()` assertion when the implementation was the buggy one, specification included. Coverage and mutation score were already high for these tools, so those scores did not expose the weakened oracle.

### 6. Design choices made by LLM-based test generators prevent them from finding bugs

- **Org.** Noble Saji Mathews and Meiyappan Nagappan, University of Waterloo.
- **Date.** Preprint submitted 18 December 2024, [arXiv:2412.14137](https://arxiv.org/abs/2412.14137). No venue found.
- **Why primary.** It runs published generators (CoverAgent / qodo-cover, CoverUp, and GitHub Copilot as a generate-only baseline) on 287 human-written buggy student programs that have a reference solution, and it keeps the tests the tools normally discard.
- **Rule.** A pipeline whose goal is “keep tests that pass on the current code and raise coverage” will validate bugs and throw away bug-revealing tests. Of the final suites CoverUp did emit, 68.1% passed on the buggy program and failed on the reference. CoverAgent’s figure was 59.6%. CoverUp emitted no suite for 196 of 287 samples (68.3%). Copilot, which did not filter to passing tests, had 4.2% of its tests in that bug-validating cell and 67.6% that failed on the bug and passed on the reference. The sample is student assignments, plus a few hand-checked SWE-bench issues. The design point still applies to any agent loop that repairs a test until it is green on the code under test.

### 7. Harness engineering: leveraging Codex in an agent-first world

- **Org.** OpenAI. Ryan Lopopolo, Member of the Technical Staff.
- **Date.** 11 February 2026.
- **URL.** <https://openai.com/index/harness-engineering/>
- **Why primary.** This is OpenAI’s account of the internal product whose tests, CI, and observability were agent-written. It is the source for worktree boot and for UI and trace legibility. It is not a source for who may author an oracle: the post says agents wrote the tests, and that humans are allowed to review pull requests and are not required to.
- **Rule.** Make one isolated boot per git worktree, and tear the copy down with the task, including its logs and metrics. Wire the Chrome DevTools Protocol into the agent so it can take DOM snapshots, screenshots, and navigate. Expose logs, metrics, and traces on that ephemeral stack. The query languages they name are LogQL and PromQL. Once that exists, a prompt such as “startup under 800 ms” or “no span in these four critical journeys exceeds two seconds” becomes something the agent can check. The post says those prompts become tractable. It does not report that the budgets were met, and it does not name TraceQL. Round-trip counts are not one of their examples.

### 8. Test Pyramid

- **Org.** Martin Fowler, martinfowler.com. The page attributes the original drawing to Mike Cohn, *Succeeding with Agile* (2009), from a sketch with Lisa Crispin around 2003–4.
- **Date.** 1 May 2012.
- **URL.** <https://martinfowler.com/bliki/TestPyramid.html>
- **Why primary.** This is the short primary page for the pyramid. Cohn’s book is the origin Fowler cites; it is not a stable public URL. The long practical article, including where contract tests sit, is Ham Vocke, “The Practical Test Pyramid,” 26 February 2018, <https://martinfowler.com/articles/practical-test-pyramid.html>.
- **Rule.** Keep many more narrow unit tests than broad tests that drive the GUI. GUI end-to-end tests are slow, brittle, and prone to non-determinism, and a suite shaped like an ice-cream cone (mostly UI, few units) fails as a regression suite. The middle of the pyramid is a service or subcutaneous layer, exercised under the UI. UI behavior of a rich client belongs in unit tests of that client. A failure that appears only in a broad test is also a missing or wrong narrow test: reproduce it at unit level, then fix. Fowler’s own caveat: if the broad tests are fast, reliable, and cheap to change, the large base of unit tests is unnecessary. That caveat is the exception, and he states the usual case the other way around.

### 9. Consumer-driven contracts, and Pact’s consumer-test rule

- **Org.** Ian Robinson, on martinfowler.com, for the pattern. Pact Docs for the current tool rule. Pact’s pages are undated; retrieved 27 October 2026.
- **Date.** Robinson, 12 June 2006. Pact consumer page, current docs.
- **URL.** <https://martinfowler.com/articles/consumerDrivenContracts.html> and <https://docs.pact.io/consumer>
- **Why primary.** Robinson states who owns the expectation. Pact states what a contract test is for, and what it must not be used for. Vocke’s 2018 article is the bridge from the pyramid to Pact; it is secondary to these two.
- **Rule.** The contract is the set of expectations the consumers actually rely on, expressed as assertions, communicated out of band before the provider is exercised. The provider’s schema is not that contract. The provider does not get to redefine the expectations to match what it currently returns. Pact’s rule on the same idea: write the consumer test as a test of the consumer’s client; the pact file is a side effect. Use it to catch a consumer that builds the wrong request, or a misunderstanding about the response. Do not use it as a functional test of the provider. Keep the expectations as loose as they can be while still breaking when the consumer would break. A check the provider’s author writes by asserting its own current response is a self-check. It becomes a contract test when a neighbor owns the examples and the provider cannot rewrite them.

### 10. Test Coverage

- **Org.** Martin Fowler, quoting Brian Marick.
- **Date.** 17 April 2012.
- **URL.** <https://martinfowler.com/bliki/TestCoverage.html>
- **Why primary.** This is the short statement that a coverage percentage is the wrong goal, and it names the gaming failure (assertion-free tests) that agents now reproduce.
- **Rule.** Use coverage to find code the suite never executes. A numeric target is easy to satisfy with weak tests. Fowler would expect the upper 80s or 90s from a thoughtful suite, would treat 100% as a smell of tests written for the number, and would treat a figure below about half as a sign of trouble. He does not turn any of those into a gate. His test of “enough” is that bugs rarely escape and that people will still change the code. Marick’s line, as Fowler quotes it: expecting a high level of coverage is different from a manager requiring one.

### 11. Code Coverage Best Practices, with the mutation-gate docs it points at

- **Org.** Google Testing Blog. Carlos Arguelles, Marko Ivanković, and Adam Bender. Mutation gates: PIT (Henry Coles and contributors) and Stryker Mutator.
- **Date.** Google post, 7 August 2020. PIT Maven quickstart and Stryker JS configuration are current docs, retrieved 27 October 2026.
- **URL.** <https://testing.googleblog.com/2020/08/code-coverage-best-practices.html>, <https://pitest.org/quickstart/maven/>, <https://stryker-mutator.io/docs/stryker-js/configuration/>
- **Why primary.** Google states a coverage practice from inside a large engineering organization, including when they gate. PIT and Stryker are the tool docs that actually define a mutation-score gate, which is the bar the request set for including them. A blog comment on the Google post names both tools; the post itself names mutation testing as the way to see whether executed lines are asserted.
- **Rule.** Coverage is a lossy single number. A high percentage does not mean the lines were checked. A low percentage does mean large areas are never executed. There is no universal target. Google’s own bands are 60% acceptable, 75% commendable, 90% exemplary, chosen per product. They also say per-commit coverage of frequently changing code can reasonably sit at 90%, with 99% as a goal, and that teams should gate deploys that miss the standard they chose. They warn that a mandate becomes a checkbox and produces low-value tests. Mutation testing is the check for “this line ran, and a wrong result would still pass.” PIT will fail a Maven build when `mutationThreshold` is set and the score is below it. The parameter defaults to 0, and the failure path runs only when the threshold is non-zero. The docs say the build can contain equivalent mutants, so the threshold needs judgment. Stryker JS defaults `thresholds.break` to `null`, which never fails the build; `high` 80 and `low` 60 are colors, not a gate. The useful output is the list of survivors on the code you meant to protect, read by someone who can mark an equivalent mutant. A repository-wide mutation percentage has the same gaming problem as a coverage percentage.

### 12. Eradicating Non-Determinism in Tests

- **Org.** Martin Fowler.
- **Date.** 14 April 2011.
- **URL.** <https://martinfowler.com/articles/nonDeterminism.html>
- **Why primary.** This is the page that says a flaky test destroys a regression suite, and it says what to do with one.
- **Rule.** A test that passes and fails with no meaningful change is not evidence. Quarantine it out of the blocking pipeline, cap how long it may stay there, and fix it. Leaving it in the main suite teaches people to ignore red. Do not use a bare `sleep` to wait for an asynchronous result; sleep either slows the suite or still flakes. Poll or use a callback. Isolate tests from each other’s data. Control the clock. Non-determinism shows up most in broad tests, which is why the pyramid keeps that layer small. Fowler does not discuss agents. The inference that an agent will rerun a flake until it is green is not a result in this article. ImpossibleBench’s small rise in cheating across repeated submissions (source 4) is the nearest measurement, and it is about real failures, not flakes.

## Conflicts

**Who flips the pass flag.** The November 2025 Anthropic harness tells the coding agent to test and then set `passes`. The March 2026 harness uses a separate evaluator, and then reports that the evaluator is still generous until tuned, and sometimes not worth running. OpenAI’s February 2026 post has the same agents write the tests and does not require a human review. ImpossibleBench and the Luxembourg workflow study say the author of the code is a bad author of the oracle. These can sit together only if the roles are split by permission: the builder may run the journey, and a different context, which cannot edit the accepted examples, is what may set the flag.

**A prompt versus a permission.** Anthropic 2025 relies on strong wording and on JSON being harder to overwrite. ImpossibleBench shows a strict prompt cutting some cheating rates sharply and leaving GPT-5 at 54% on the SWE-bench variant. Read-only tests stop edits. They do not stop the code being special-cased to the tests.

**Spec in the prompt versus a frozen oracle.** Dagger’s tools still weakened assertions when the JavaDoc was in the prompt and the code was buggy. Konstantinou, Tambon, and Papadakis get a higher detection rate when the test is generated from the task text without the code. Putting the spec in the implementer’s context is not the same control as an example the implementer is unable to rewrite.

**Pyramid versus “drive the browser.”** Anthropic’s observation is about the definition of done for a UI feature: unit tests and `curl` left the feature broken. Fowler’s pyramid is about the shape of the suite: broad UI tests are the slow, brittle, flaky ones, so there should be few of them. One journey per feature, plus the core journey at the start of a session, fits both. A CI suite made mostly of browser tests fits Anthropic’s tool choice and breaks Fowler’s proportion, and Fowler’s flake article says that layer is where non-determinism concentrates. Fowler’s exception (drop the unit base when the broad tests are fast and stable) is not what Anthropic reported. Their browser runs were good enough to find bugs, and still blind to native dialogs.

**Coverage map versus coverage gate.** Fowler 2012: the number is a poor statement of quality, and a target will be gamed. Google 2020: the number is lossy, there is no universal percentage, and a low number is still positive evidence of untested code. They publish bands, they gate teams that have chosen a bar, and they want new or frequently changed code held to a high per-commit bar. Dagger is the reason not to treat either coverage or a mutation score as the oracle: the tools they studied had saturated both while still emitting the weaker assertion.

**What “contract” means.** Robinson and Pact: a consumer owns loose examples of the messages it depends on; the provider must not rewrite them; those examples are not the functional tests of the provider’s business rules. The pyramid’s middle layer (service or subcutaneous tests) is where a single team tests a boundary’s behavior, including refusals. Using one word for both lets an implementer satisfy “contract” by asserting the response it just coded.

**Mutation as a percentage.** Google says mutation testing is how you learn whether an executed line is asserted. PIT and Stryker can fail a build on a score, and both ship with that failure off by default. PIT warns that equivalent mutants make a threshold a judgment. Dagger’s generators had high mutation scores and still weakened the assertion that mattered. A score gate and a coverage gate fail in the same way once an agent can add tests to move the number.

## Corrections to the dry run

**The check that would go red if the behavior were removed does not prove the oracle.** Section 13 and the evidence record ask the verifier to cite one check that fails if the new behavior is removed. A tautological test fails that deletion too: it asserts whatever the code currently returns, so removing the code makes it red, and so does a weakened check that still touches the feature. The studies that match this failure are sources 1, 5, and 6. The replacement check has two parts. The expected value is fixed in the accepted examples before implementation, and the implementer cannot edit it (sources 1, 3, 4, and 9). A small change to the rule, with that frozen example still in force, must fail (source 11). Scope that mutation to the slice’s decision. Read the survivors. Do not install a repository mutation percentage. An agent that can see the mutant report and then add an assertion of the current return value will kill the mutant without encoding the spec, which is the same move Dagger measured.

**“Only the verifier sets `passes`” is the March 2026 harness, tightened further.** The November 2025 article, which §2 and §6 cite for the pass flag, has the coding agent self-verify and flip the flag, and it tries to stop edits with a prompt. The separate evaluator, and the agreement about done before code, are the March 2026 article. That article also says the evaluator stayed lenient until the prompt was tuned against its own misses, and that on a stronger model the evaluator was sometimes overhead. A fresh session is the right split. It is not, by itself, a skeptical judge, and it is not a reason to run every lens on every slice once the model is inside the task.

**Cases added after the code exists are regression locks until they are accepted against the spec.** Source 1’s test-driven arm still only detected faults 25% of the time, and that arm never saw the implementation. Source 5 still weakened assertions when the specification was present and the buggy code was too. The implementer may add cases. Those cases do not become oracles by being added. A new expected value has to come from the accepted spec (the other actor, the replay, the malformed input), and it must not replace an accepted expected value. The verifier adding cases while reading the diff has the same bias unless the expected value is derived from the spec rather than from the implementation’s output.

**“Contract” in the ladder is a service test unless a neighbor owns it.** Source 9. For a single product with one team, the row that checks a boundary shape the implementer just wrote is Fowler’s subcutaneous or service layer (source 8). Call it a contract test when the examples belong to the other side of the boundary and this slice cannot regenerate them from its own response. Pact’s other rule belongs in the same row: a contract example is not the functional test of the provider, and it should be no stricter than the consumer’s real dependency.

**Coverage stays a map. “The feature’s cases exist” is a weaker gate than either Fowler or Google.** Source 10 supports refusing a global percentage. Source 11 says a low number is evidence of untested code, and that new code is the reasonable place for a numeric bar if a team has chosen one and will not treat it as a checkbox. One existing case can be tautological and can still execute the line (sources 5 and 6). Use the coverage report as the list of unexecuted lines in the slice. A risk-class branch with no spec-derived case is a hole. A repository-wide percentage is not the gate.

**The mutation sentence is right about scope and thin about what the run is for.** “Not every line” matches PIT’s default (threshold 0) and Stryker’s default (break off), and it matches the equivalent-mutant warning. Point the run at a diff that touches an accepted oracle or a risk-class rule, and treat a survivor of the frozen example as a failed check. A global score is the coverage target again.

**A flake is quarantined, not rerun.** Source 12 already supports “no bare sleeps, no timing-dependent assertions.” Add the other half: take the non-deterministic test out of the blocking pipeline and fix it, and do not let a red flake become either a product repair or an edit to the oracle. “Flakes teach the agent to rerun until green” is a plausible consequence. It is not a measured result in the sources above.

**Browser driving is the done-check for a UI feature, and it is a small layer.** Source 2 is qualitative, on one clone, and it records a real blind spot (native dialogs). Source 7’s mechanism is a per-worktree boot plus Chrome DevTools Protocol (DOM snapshots, screenshots, navigation) and an ephemeral log and metric stack queried with LogQL and PromQL. The span budget is an example of a prompt that becomes checkable. It is not a reported measurement, and TraceQL is not in the post. Storage round-trip budgets in the dry run are an extension of that legibility idea. Keep one user-level journey as the feature’s done-check and the core journey as the session-start check (source 2). Keep the rest of the suite narrow (source 8), because the broad layer is where flakes cluster (source 12).

**OpenAI is the source for legibility, not for independent tests.** Section 13 can cite the worktree, the browser protocol, and the trace budget. It should not cite that post for who writes the oracle. The post’s premise is that agents wrote the tests.

## Also read, not ranked

- Mohammadali Charoosaei, Cedric Richter, and Mike Papadakis, “On the Risks of using LLM-Generated Tests for Regression Testing,” [arXiv:2610.11835](https://arxiv.org/abs/2610.11835), October 2026 preprint. On 145 pull requests in SciPy, Qiskit, and pandas, with faults injected, 8–17% of generated tests enforced the fault and 2.4–4.8% revealed it. Most of the enforcing tests were still in force after later commits (83–91%). This is the persistence result: a fault-enforcing test becomes a reason to reject the fix later. Preprint, and the faults are injected, so it stays out of the ranked twelve.
- The industrial case study “Practical Limits of Autonomous Test Repair,” [arXiv:2605.01471](https://arxiv.org/abs/2605.01471), records one repair that rewrote `expect(value).toBe(5)` to `expect(value).toBeTruthy()`, and one repair that deleted the failing case and then reported a full pass. It is an illustration of the edit ImpossibleBench measures. The public text does not give a rate, so it is not ranked.

## Rejected

- Wavect, “QA for AI-Generated Code,” <https://wavect.io/blog/qa-for-ai-generated-code/>. Advice about what a green path fails to prove. No dataset, sample size, or result table in the indexed text. The dry run’s §2 line that attributes authorization holes and weakened assertions to this page should move to sources 4, 5, and 6.
- Quash, “Testing AI-Generated Code: A QA Playbook (2026),” <https://quashbugs.com/blog/testing-ai-generated-code-qa-playbook>. A merge checklist. Several of its lines match §13 (coverage as a map, mutation only when suspicious, reject weakened assertions). The indexed text does not report a measurement. Useful as a checklist someone already believed. Not evidence for the checklist.
- Secondary retellings that add a mechanism the primary does not state. The engineering.fyi mirror of the OpenAI post adds TraceQL. The OpenAI post names LogQL and PromQL. Pattern pages that summarize the Anthropic post are not a second source.
