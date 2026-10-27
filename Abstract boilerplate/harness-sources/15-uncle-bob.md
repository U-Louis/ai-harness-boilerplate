# 15 — Robert C. Martin on reviewing agent-written code

Sources for the metric gates in dry-run [§11](../ai-harness-state-of-the-art.md), and for the one-sentence pointers in [§12](../ai-harness-state-of-the-art.md) and [§13](../ai-harness-state-of-the-art.md). Pages were read on 27 October 2026.

The video named in the request, Nariman Codes, “Uncle Bob: I Don't Review Code Written by AI Agents,” 7 June 2026, <https://www.youtube.com/watch?v=wqYZSCC6Ino>, is a commentary. It reads one of his posts aloud and then argues the other way. It is not him speaking. The sentences it quotes are checked against the other copies below. The host’s objections are not his position.

Rank is by how directly the page is him, on the question of what an agent must pass. A tool README ranks under the interviews when the README adds a number he does not say. x.com did not render on a direct fetch. Post text below is from a second copy (the video, a dated embed, or the archive named on that item), not from the X page itself.

## Ranked sources

### 1. Post of 14 April 2026, reply to @wookash_podcast

- **Who:** Robert C. Martin, @unclebobmartin.
- **Date:** 14 April 2026, as dated on the embed.
- **URL:** The status did not load. The same sentences are read in source 10 and printed, with that date, in Jan Giacomelli, “Is Clean Code Dead?,” 12 August 2026, <https://jangiacomelli.com/blog/is-clean-code-dead/>, and in Mike Hay, “Revisiting Important Questions,” 5 August 2026, <https://mikehay.me/blog/2026/08/05_1213.html>.
- **Why primary:** This is the post the named video is about. Two later pages and the video agree on the wording.
- **What he says:** “I don’t review code written by agents. I measure things like test coverage, dependency structure, cyclomatic complexity, module sizes, mutation testing, etc. Much can be inferred about the quality of the code from those metrics. The code itself I leave to the AI. Humans are slow at code. To get productivity we humans need to disengage from code and manage from a higher level.” No threshold. No cognitive complexity. “Module sizes” is named and not defined.

Hay’s page also quotes a later post, undated there: he does not read any of the code his agents write, and he surrounds them with unit tests, Gherkin tests, QA procedures, quality metrics, mutation testing, test coverage, “and a plethora of others.” That status was not opened. The interviews say the same practices, so the list does not depend on that one post.

### 2. Thread of 20 April 2026

- **Who:** Robert C. Martin.
- **Date:** 20 April 2026.
- **URL:** The rant is <https://x.com/unclebobmartin/status/2046206145597972849>. A direct fetch of the mirror returned 403. The reply text here is the search capture of that thread.
- **Why primary:** Same week as source 1, and it names the tools.
- **What he says:** “Run crap and mutate to get those functions smaller and less coupled. Write acceptance tests (in gherkin) that you manage but the AI must make pass. Run a scrap analyzer to keep your test code under control. Run a dependency checker to keep your architecture under control. Build walls around the AI using hard tools. Don't let the AI escape from those tools.” The numbered reply on the same thread: do not give a big task; Gherkin acceptance tests that the agent must pass, which he manages; unit tests that keep passing; cover uncovered code; mutation testing; CRAP, to drive cyclomatic complexity down and coverage up; dependency checkers for the architectural constraints; and the engineer remains the engineer. No number on CRAP, coverage, or complexity.

### 3. Become an Epic Product Engineer, with Kent C. Dodds

- **Who:** Robert C. Martin, interviewed by Kent C. Dodds.
- **Date:** 22 July 2026.
- **URL:** <https://www.youtube.com/watch?v=RxxxGkFIUJ0>. Transcript: <https://podcastrex.com/shows/become-an-epic-product-engineer/architecture-ai-agents-and-product-empathy-with-robert-c-martin/transcript>.
- **Why primary:** He states the cyclomatic numbers and the CRAP ceiling he was using that day, in his own voice.
- **What he says:** He purposely does not look at the code until the job is done, then he may spot-check. He looks at module structure, and he has agents build a view he can drill into. He asks the agents about dependencies and data flow and tells them to fix a flow he dislikes. For functions he writes himself he tries to limit cyclomatic complexity to four. For agents he lets that go to “maybe six,” because they can deal with a couple of indents. He used to limit a function to three arguments when humans wrote it. Agents get “a little more leeway.” He states no new argument cap, and he does not want fifty. He spot-checks by scrolling, looking at function names and sizes, for something “outlandishly stupid.” The agents must run tools and correct what the tools report. CRAP mixes test coverage with cyclomatic complexity. He does not remember the name or the formula (“cubed or squared or something”). He tells the agents to reduce every score below six. If the score is high because the function is uncovered, they add tests. If it is covered and still 12, they split it. He allows agents to write comments, because they read and maintain them. He does not say the agents have design sense while they are writing. He does not mention cognitive complexity.

### 4. Live with Matt Pocock

- **Who:** Robert C. Martin, interviewed by Matt Pocock.
- **Date:** Listed as 19 August 2026 on <https://skip.watch/en/zcLPGC-tvgk>. The YouTube oembed returned no date. Transcript read from the video and from <https://sozai.app/transcript/uncle-bob-software-fundamentals-ai/>.
- **URL:** <https://www.youtube.com/watch?v=zcLPGC-tvgk>.
- **Why primary:** He separates the CRAP ceiling from the path count, and he describes the dependency file.
- **What he says:** CRAP takes coverage and cyclomatic complexity through “a complicated formula.” He does not expand the acronym. Mutation testing flips signs and comparisons and expects the suite to fail. He looks at CRAP scores and does spot checks, and he says reviewing the code is the slowness he will not impose. For humans he would keep CRAP below four. For agents he had set it at six and was thinking of eight. At full coverage, a CRAP score of six means six paths, all covered. That is the goal he states: cover the paths, and limit the path count. He had “debates with the agents” in which they called six a good cyclomatic complexity, and he says not to trust that debate. A dependency tool takes a file of which module may depend on which, and in which direction. A violation has to be fixed, usually by inverting a dependency, inserting an interface, or splitting a module. He was not yet happy with automating the design of that file. His swarm, as he describes it that day: a specifier turns a human document into Gherkin and a QA procedure written from a human at the UI; a coder writes unit tests and the implementation and gets the Gherkin passing; a later pass cleans up; a hardener runs mutation testing and is “merciless”; a QA agent turns the procedure into a UI script. He will not impose line-by-line test-driven development on an agent. He will impose the values (tested, partitioned). He does not mention cognitive complexity, and he does not mention duplication by name in this transcript.

### 5. Aviator, “Why I Stopped Reading Code and Building Harnesses”

- **Who:** Robert C. Martin, interviewed by Ankur Jain.
- **Date:** 24 September 2026 on the article page.
- **URL:** Video <https://www.youtube.com/watch?v=5kCISBJwoZo>. Article <https://www.aviator.co/podcast/uncle-bob-martin-ai-clean-code>. Where they differ, the video transcript is the source. The article’s expansion of CRAP does not match the video.
- **Why primary:** Latest of the three interviews. The CRAP ceiling he states here is looser than July and August.
- **What he says:** He barely sees code. He tells the agent the constraints and the thresholds and watches. Unit tests are the first constraint: without them the agents “go off into Wonderland.” CRAP is an old metric from about 2007. He cannot remember the acronym (“change something something something”). It combines coverage with cyclomatic complexity, and the number “skyrockets” when a complicated function is not tested. He used to keep CRAP down to four, backed off to six, and at this point thinks “12 was probably okay.” Mutation testing: coverage says what executed, not what was tested. The tool modifies the source and expects each modification to fail. A modification that passes is a surviving mutant. He has the agents kill the survivors “within limits.” Some cannot be killed and some should not. He is the final arbiter on intent: he runs the system and tells the agent what he dislikes. Humans should author Gherkin. When agents wrote and tested the Gherkin, “the farther away a human is from the Gherkin, the weirder the Gherkin gets,” and he backed away from that. On a diagram he looks at high CRAP, a low mutation rate, and misaligned dependencies, and he can drill to the code, which he usually does not. He still looks at a module from time to time. “95% of the code I never see.” Teams should drive coverage “as high as possible,” keep CRAP below “a certain threshold,” and mutation-test “within certain limits.” A human GUI pass may be part of the discipline. Reviewing the code “isn’t very useful anymore” and “slows everything down.” Only humans can be responsible, and they sign off above the syntax. He does not mention cognitive complexity.

### 6. Posts from May and June 2026

- **Who:** Robert C. Martin. Wording from the archive <https://github.com/osolmaz/ai-smell/blob/main/corpus/tweets/unclebobmartin.md>, which stores status URLs. x.com was not opened.
- **Why primary:** These are the posts in which he names dry analysis, property tests, and the play-test that the metrics missed. The interviews do not use the words “dry” or “property.”
- **28 April 2026,** <https://x.com/unclebobmartin/status/2049085958952272291>. Disciplines and tools are not sufficient. The engineer keeps a mental model, corrects a path he dislikes, and probes a suspicion without an exhaustive review.
- **25 May 2026,** <https://x.com/unclebobmartin/status/2058869482815197320>. A rule file will be overturned. He uses “physical constraints”: acceptance tests, unit tests, mutation tests, CRAP analysis, dry analysis, property tests.
- **4 June 2026,** <https://x.com/unclebobmartin/status/2062606758229549545>. One run included Gherkin and QA from an informal spec, Gherkin-driven acceptance tests, unit tests, end-to-end QA through the UI, language mutation, Gherkin mutation, architectural partitioning and dependency management, property tests, and CRAP and dry refactoring.
- **6 June 2026,** <https://x.com/unclebobmartin/status/2063344635150553131>. After that gauntlet on Hunt the Wumpus he inspected the code (“pretty good,” “no AI slop”) and then play-tested. He found ten bugs. Half were specification misses of his own. “The humans still have to be in the loop.”
- **19 June 2026,** <https://x.com/unclebobmartin/status/2067935532580024741>. When the audience is agents he tolerates slightly larger functions, “cyclomatic complexity less than or equal to six,” and slightly more comments, because agents read and maintain comments.
- **19 May 2026,** <https://x.com/unclebobmartin/status/2056856116659704191>. He likes a Gherkin examples table because it is easier to mutate the Gherkin. He does not publish a Gherkin-mutation tool in the repos below.

He does not define property tests, and none of his repos below is a property-test runner. There is no property-test gate in the dry run.

### 7. The checkers he publishes

These are the reference implementations. The dry run is not a Clojure harness. The gate is the metric. The URL is the definition.

- **crap4clj,** <https://github.com/unclebob/crap4clj>. Copyright line: Robert C. Martin. CRAP is “Change Risk Anti-Pattern” on that page. Formula: `CRAP(fn) = CC² × (1 − coverage)³ + CC`. CC is cyclomatic complexity, decision points plus one. Coverage is the fraction of forms Cloverage covered. The same formula is Alberto Savoia and Bob Evans, 2007, source 9, under the name Change Risk Analysis and Prediction, later Change Risk Anti-Patterns. He does not recite it. The README’s own bands are 1–5 low, 5–30 moderate, 30 and above high. Those bands are the README. They are not the ceilings in sources 3, 4, and 5.
- **clj-mutate,** <https://github.com/unclebob/clj-mutate>. Mutation testing for Clojure. A mutant is killed when the specs fail. Exit status 3 means survivors or in-scope uncovered mutations remain. The README’s workflow says to kill every survivor in the file before the next file. He says “within limits” and “important” survivors (source 8). The operator table is in the README (arithmetic swaps, comparison swaps, `true`/`false`, `and`/`or`, `0`/`1`, and others). Known-equivalent mutations are suppressed. Differential mutation is the default once a snapshot exists.
- **dependency-checker,** <https://github.com/unclebob/dependency-checker>. See the metric definitions below. They are from `src/dependency_checker/core/graph.clj` and the README, read 27 October 2026, not from a paraphrase of *Clean Architecture*.
- **dry4clj,** <https://github.com/unclebob/dry4clj>. Created 7 May 2026. Fuzzy structural duplicates. Similarity is Jaccard on normalized forms. The default `--threshold` is 0.82. He does not say 0.82. The README reports candidates “so another mechanism can evaluate” them.
- **scrap,** <https://github.com/unclebob/scrap>. Created 15 March 2026. Structural quality of Speclj specs, aimed at test code the way CRAP is aimed at production code. Recommendations, not directives. The assistant is told to confirm them against the spec’s intent. It includes the nesting checks that `speclj-structure-check` used to do.
- **speclj-structure-check,** <https://github.com/unclebob/speclj-structure-check>. Invalid spec nesting. Still named in source 8.
- **arch-view,** <https://github.com/unclebob/arch-view>. A layered picture of namespace dependencies, including cycles. It is the kind of view he describes clicking through. It is not the pass/fail checker. The checker is dependency-checker.
- **Cloverage,** <https://github.com/cloverage/cloverage>. Not his. crap4clj and clj-mutate call it for coverage. He says “a coverage tool.”

### 8. AIR-J agent instructions

- **URL:** <https://github.com/unclebob/AIR-J/blob/master/AGENTS.md>, raw file read 27 October 2026.
- **Why it is not a spoken threshold:** The file tells an agent to keep CRAP below 8 on changed functions, to kill “important” mutation survivors, and to treat dependency violations and cycles as design problems. He never says “eight” in sources 3, 4, or 5. July and August say six, with a possible move to eight. September says 12 was probably okay. The file is a third number. The dry run does not copy it.

### 9. Origins he relies on and does not restate

- **Mutation, in his words before agents.** Robert C. Martin, “Mutation Testing,” 10 June 2016, <https://blog.cleancoder.com/uncle-bob/2016/06/10/MutationTesting.html>. A semantic change to the code should fail a test. `pitest` (<https://pitest.org/>) flips conditions and drops calls. A mutant the suite does not kill survived. Coverage is the weaker question (which lines ran). This is the definition. It is not a 2016 opinion about agents, and it is not the oracle rule in the dry run.
- **CRAP formula.** Alberto Savoia, “The Code C.R.A.P. Metric Hits the Fan,” Artima, October 2007, <https://www.artima.com/weblogs/viewpost.jsp?thread=215899>. `CRAP(m) = comp(m)^2 * (1 – cov(m)/100)^3 + comp(m)`. Initial “crappy” line: 30. He does not cite Savoia, and he does not use 30.
- **Cognitive complexity.** G. Ann Campbell, Sonar, “Cognitive Complexity, Because Testability != Understandability,” 7 December 2016, <https://www.sonarsource.com/blog/cognitive-complexity-because-testability-understandability/>. White paper: <https://www.sonarsource.com/docs/CognitiveComplexity.pdf>. Sonar’s metric adds for a break in linear flow and again for nesting, and it is meant to track understandability rather than the number of tests. Campbell’s post quotes McCabe’s old limit of 10 as McCabe’s, and as a limit McCabe already found unreasonable for a large switch. That 10 is not Martin’s.

### 10. The named video

Nariman Codes, 7 June 2026, <https://www.youtube.com/watch?v=wqYZSCC6Ino>. Used only as one more copy of the 14 April post. The rest of the transcript is the host.

## Complexity choice

He chooses cyclomatic complexity. He uses it as the path count inside CRAP, and he states a looser path count when the reader is an agent.

He does not mention cognitive complexity. Nothing in sources 1 through 8 is a rejection of Sonar’s metric, and nothing in them is a reason. The dry run does not invent one.

Thresholds he does state:

| What | Number | Where | What it is |
| --- | --- | --- | --- |
| Cyclomatic complexity, functions he writes | 4 | 22 July 2026 | His own hand-written limit. He says *Clean Code* never put it in these terms. |
| Cyclomatic complexity, agent-facing functions | “maybe six,” and “less than or equal to six” | 22 July 2026; post of 19 June 2026 | A tolerance, not a second gate beside CRAP. On 19 August he says the agents suggested six and that he does not trust that debate. |
| CRAP, his own code | below 4 | 19 August 2026; “down to four” on 24 September | The score, not the path count. |
| CRAP, agents | below 6 | 22 July 2026; set at 6 on 19 August | What he told the agents on those days. |
| CRAP, agents, considered | maybe 8 | 19 August 2026 | He had not moved it. |
| CRAP, agents, later | “12 was probably okay” | 24 September 2026 | Looser than July. |
| CRAP in the AIR-J file | below 8 | source 8 | A file. Not said in the interviews. |
| Savoia’s original line | 30 | source 9 | Not his agent ceiling. |
| “Below 10” | — | the request’s summary | Not in any source above. Unverified. Dropped. |

At full coverage he says a CRAP score equals the path count (19 August). The published formula agrees: the first term is zero when coverage is 1, so the score equals CC. A CRAP ceiling is then a cyclomatic ceiling for fully covered functions, and a coverage pressure for the rest. That is why he runs CRAP rather than a bare path count. This document does not pick 4, 6, 8, 10, or 12.

## Metrics, as he defines them

| Metric | His definition | Number he requires |
| --- | --- | --- |
| Cyclomatic complexity | Paths through the function. His Clojure checker counts decision points plus one (`if`, `when`, `and`, `or`, `loop`, `catch`, each `cond`/`case` clause, and the threading forms listed in the crap4clj README). | About 6 for agent-facing functions, as a tolerance. Not a separate block. See the table above. |
| Coverage | What a coverage tool executed. He is explicit that this is not what was tested. “Drive coverage as high as possible” (24 September). “Cover any uncovered code” (20 April). | No percentage. |
| CRAP | Coverage and cyclomatic complexity in one score. High when a complicated function is untested. Remedy he states: add tests, or split the function. Formula: the 2007 one, in crap4clj. | The ceilings in the table. They moved. |
| Mutation | A small semantic edit (sign, comparison, equality, and, in his 2016 account, dropping a call). The suite should fail. A pass is a survivor. Kill survivors, within limits. Some cannot and some should not be killed. | No mutation-score percentage. “Within limits.” The AIR-J file says “important” survivors. |
| Dependency structure | Which component may depend on which, and in which direction. A cycle is a failure. The repair he names is invert, insert an interface, or split. | No distance ceiling in his speech. |
| Instability | In the checker, not in the interviews: fan-out / (fan-in + fan-out). Fan-in is how many other components depend on this one. Fan-out is how many this one depends on. Zero when both are zero. | None. |
| Abstractness | In the checker: public `defprotocol` and `defmulti` vars, divided by public vars. Private vars do not count. Zero when there are no public vars. | None. |
| Distance from the main sequence | In the checker: the absolute value of abstractness + instability − 1. The main sequence is the line where that sum is 1. This is one distance, not two. The summary’s “D or D′” is this value. The checker does not also compute a perpendicular distance. | The README’s default healthy band is 0.3 either side of the line. Inside the band the zone is healthy. Below it, zone of pain (concrete and stable). Above it, zone of uselessness (abstract and unstable). The starter config fails the process on boundary violations and on cycles. It does not fail on distance. He never says 0.3. |
| Module size | Named on 14 April. In July he looks at function sizes while scrolling. | No line count, no module-size number. |
| Duplication | “Dry analysis” (25 May, 4 June). dry4clj: Jaccard similarity of normalized forms. | Tool default 0.82. Not spoken. |
| SCRAP | Test-code structure: complexity of examples, weak assertions, and duplication that is worth extracting. Advisory. | No spoken cutoff. |
| Property tests | Named 25 May and 4 June. | Not defined. No tool. |
| Argument count | Three was his human limit. Agents get more leeway. Fifty is too many. | No agent number. A spot check, not a gate. |

## Tools

| Tool | What it measures | URL | Blocks? |
| --- | --- | --- | --- |
| crap4clj | CRAP from cyclomatic complexity and coverage | <https://github.com/unclebob/crap4clj> | He treats a score over his ceiling as work the agent must do. The ceiling moved. |
| clj-mutate | Killed, survived, and uncovered mutants | <https://github.com/unclebob/clj-mutate> | Exit 3 when survivors or uncovered mutations remain. He carves out mutants that should not be killed. |
| pitest | The same idea, on Java bytecode, in the 2016 post | <https://pitest.org/> and <https://blog.cleancoder.com/uncle-bob/2016/06/10/MutationTesting.html> | Historical. Not the tool he points agents at now. |
| dependency-checker | Allowed edges, cycles, and the package metrics above | <https://github.com/unclebob/dependency-checker> | Violations and cycles, when the config says so. Distance is reported. |
| dry4clj | Near-duplicate forms | <https://github.com/unclebob/dry4clj> | Reports candidates. Does not, by itself, fail the process. |
| scrap | Spec structure and weak examples | <https://github.com/unclebob/scrap> | Recommendations. A zero-assertion example is the finding that matches the dry run’s oracle rule. |
| speclj-structure-check | Illegal nesting in specs | <https://github.com/unclebob/speclj-structure-check> | Fails before the specs run, in the AIR-J file. |
| arch-view | A picture of layers and cycles | <https://github.com/unclebob/arch-view> | No. He looks at it. |
| Cloverage | Which forms the tests executed | <https://github.com/cloverage/cloverage> | Input to CRAP and to mutation. Not a percentage gate. |
| Gherkin | Given / when / then, authored by a human | He names the language. He does not name a product URL. | The agent must make his text pass. Agent-written Gherkin is the failure he backed away from. |

He does not name ESLint, Sonar, Checkstyle, or any other style linter as one of the walls. The walls he names are the rows above.

## What he still wants a human to look at

He says he does not read the diff. He also says all of the following, and they are part of the same account.

- He authors the Gherkin. Agent-authored Gherkin drifted into nonsense (24 September).
- He runs the system and rejects what he dislikes. On 6 June the play-test found ten bugs after the metrics had passed.
- He spot-checks names, sizes, and the occasional module. He looks at the dependency picture, high CRAP, and a low mutation rate.
- He keeps the architectural partition. The agents do not get to widen the allow-list.
- He remains responsible. The agents are not. “You are the engineer” (20 April). The sign-off is “just above the code” (24 September), which is his description of skipping the diff.

## Conflicts with the digest

**He skips the diff. The digest leaves that choice open.** Under Read, [§12](../ai-harness-state-of-the-art.md) still says a complete evidence record is not a reason to leave a diff unread, that a human reads when the blind spot is unnamed, and that a sample of evidence-only merges is still read. Google’s bar, when a person reviews, is that someone other than the author reads the change, including the tests. SWR-Bench’s best single LLM review F1 is 19.38%. His 6 June play-test is the same kind of miss, from his side: the gauntlet was green and the game was wrong. Under Metrics, the person does not read the agent diff and still play-tests the system. His metrics do not retire G3 in either posture.

**Mutation is his metric, not a new oracle theory.** [§13](../ai-harness-state-of-the-art.md) already says a check that goes red when the feature is deleted does not prove the oracle, that a small change to the rule must fail the frozen examples, and that a repository-wide mutation percentage is not installed. His 2016 definition is the metric under that rule: a semantic edit should fail a test, and a survivor means the suite did not check that edit. Killing a survivor by asserting the code’s current return value is the failure the testing notes already record. The digest cites him for the metric.

**Coverage stays a map.** He says to drive it up and to cover uncovered code. He also says a coverage tool does not tell you what was tested. Fowler’s refusal of a global percentage stays. CRAP is how he couples coverage to the path count without a second percentage gate.

**Do not copy a ceiling he did not settle.** 4, 6, maybe 8, 12, and the file’s 8 are all his, at different dates. 10 is not. 30 is Savoia’s. 0.82 and 0.3 are tool defaults he does not say.

**The red invariant stays blocking. The pass flag stays with the verifier. Spec Kit, the three security triggers, and the developertoolkit gates are untouched.** Nothing here is a reason to drop a human evidence gate, to let the implementer set `passes`, or to treat a green CRAP score as G3.

## Checked and not used

- Product Engineers, “Why Hardcore Technical Focus Still Matters Big Time,” 29 September 2026, <https://productengineers.com/episodes/season2/why-hardcore-technical-focus-still-matters-big-time-uncle-bob/>. The page is the show’s “Lessons Learned,” not a transcript. The transcript URL the show cites returned 404. Duplication, dependency rules, Gherkin, and mutation are already in sources 2 through 7. No claim was taken from the lessons.
- blog.cleancoder.com. The indexed AI-era pages are not about reviewing agent code. The 2016 mutation post is source 9. No 2024–2026 post on that blog was found for this question.
- Jeffry Babb, “Fly by wire, and seat of pants,” 30 July 2026, <https://sprintanddrift.substack.com/p/fly-by-wire-and-seat-of-pants>. A commentary that derives coverage floors from a CRAP ceiling of 6. He does not publish those floors. Not used.
- Adrian Bailador, “Uncle Bob’s Agent Pipeline,” June 2026. A .NET reconstruction with Stryker commands. He does not publish those commands. Not used.
- The Aviator article’s phrase “Change Risk Analysis and Predictions.” The video says he cannot remember the acronym. The 2007 post says “Change Risk Analysis and Prediction.” The crap4clj README says “Change Risk Anti-Pattern.” The dry run does not need the expansion.

## Rejected from the request’s summary

- A threshold of “below 10,” for cyclomatic or cognitive complexity. Not found.
- A rejection of cognitive complexity, and a reason for that rejection. He does not discuss the metric.
- Instability, abstractness, and distance as spoken AI gates with a number attached. The checker computes them. His spoken gate is the allow-list and the absence of cycles.
- Two distances, D and D′. The checker has one, `|A + I − 1|`.
- A module-size cap. He names module size and states no count.
- A mutation-score percentage, a coverage percentage, or Savoia’s 30, as his agent bar.
- A style linter he never names.
- The Nariman Codes video’s conclusions. They are the host’s.
