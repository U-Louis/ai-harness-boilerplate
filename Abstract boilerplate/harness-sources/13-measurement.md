# 13. Measuring whether a harness works

Primary sources for [§18](../ai-harness-state-of-the-art.md) of the dry run. Read 27 October 2026. The dry run file was not edited.

Ranked by how directly the source tells you whether an AI coding system is delivering working software, rather than whether it is producing text. Vanity counts (lines, suggestions accepted, prompts) are not success metrics. Each number below is the source’s own, with the denominator it used.

The six rows in the dry run’s table are not the DORA five. They are a local narrowing of a vendor guide’s harness table. DORA’s delivery metrics are defined in sources 1–3. Two-week line replacement is source 7. Benchmark “resolved” is source 5, and maintainer merge is source 4.

---

## Ranked sources

### 1. Accelerate State of DevOps 2024

- **Org:** DORA, Google Cloud. Research leads named on the announcement: Nathen Harvey and Derek DeBellis.
- **Date:** 22 October 2024 (report marked v.2024.3). Survey fielded early 2024. Nearly 3,000 respondents this year. “More than 39,000” is the decade-long program, not this sample.
- **URL:** <https://dora.dev/research/2024/dora-report/2024-dora-accelerate-state-of-devops-report.pdf>
- **Announcement (same authors, same day):** <https://cloud.google.com/blog/products/devops-sre/announcing-the-2024-dora-report>
- **Why primary:** This is the survey that estimated the AI-adoption associations. Later pages that quote “1.5%” and “7.2%” are citing this PDF.

**Metric definitions used in that model.**

| Factor | Metrics | Meaning in this report |
| --- | --- | --- |
| Software delivery throughput | Change lead time, deployment frequency, failed deployment recovery time | Speed of getting a change into production, including recovery from a failed deployment |
| Software delivery stability | Change failure rate, deployment rework rate | How often a production deployment causes immediate extra work |

Change failure rate: the percentage of deployments that cause failures in production and require a hotfix or rollback. A deployment counts only if users can experience the failure. A change stopped before production is a successful catch, not a failure.

Deployment rework rate, asked this year as: for the primary application or service, approximately how many deployments in the last six months were not planned but were performed to address a user-facing bug. Together with change failure rate, this is the stability factor. It is a count of deployments, not a count of lines replaced.

Code review speed, in the same chapter, is self-reported: “the average time required to complete a code review” for that primary service. Code quality is self-reported satisfaction with the code over six months. Neither is a defect count.

**Reported associations.** For every 25% higher AI adoption, the structural model estimates:

| Outcome | Estimated change |
| --- | --- |
| Documentation quality | +7.5% |
| Code quality (satisfaction) | +3.4% |
| Code review speed | +3.1% |
| Approval speed | +1.3% |
| Code complexity | −1.8% (treated as an improvement) |
| Delivery throughput | −1.5% |
| Delivery stability | −7.2% |
| Organizational performance | +2.3% |
| Team performance | +1.4% |

Individual attitudes in the same survey, not in that 25% model: 75% reported some productivity gain from AI in the prior three months; 39.2% reported little or no trust in AI-generated code; 75.9% relied on AI for at least one daily responsibility.

**Caveat.** These are estimated associations from self-report, not a randomized effect of turning AI on. The 25% is a step in their adoption factor, not “teams that use AI versus teams that do not.” The authors’ own reading of the throughput and stability drops is a hypothesis: AI lets people produce more code in the same time, so change size grows, and large changes are slower and less stable. They also write that faster review and approval do not mean a more thorough review. Product performance had no clear association.

### 2. State of AI-assisted Software Development 2025

- **Org:** DORA, Google Cloud. Outcomes chapter by Derek DeBellis. Announcement by Nathen Harvey and Derek DeBellis.
- **Date:** Published 23 September 2025 (report v.2025.2). Survey 13 June–21 July 2025. Nearly 5,000 technology professionals, plus more than 100 hours of interviews.
- **URL:** <https://dora.dev/research/2025/dora-report/>
- **PDF:** <https://services.google.com/fh/files/misc/2025_state_of_ai_assisted_software_development.pdf>
- **Announcement:** <https://cloud.google.com/blog/products/ai-machine-learning/announcing-the-2025-dora-report>
- **Why primary:** This is the next DORA wave. It restates the 2024 coefficients and then reports a new model in which the throughput sign has changed.

**Definitions.** Same five metrics. The 2025 text names the second factor **instability** (higher is worse). 2024 named it **stability** (higher is better). A “7.2% reduction in stability” and a “7.2% increase in instability” are the same 2024 estimate, relabeled.

2025 short forms, from the report:

- Change lead time: commit in version control to deployed in production.
- Deployment frequency: deployments over a period, or time between deployments.
- Failed deployment recovery time: time to recover from a deployment that needs immediate intervention.
- Change fail rate: share of deployments that need immediate intervention, such as a rollback or hotfix.
- Rework rate: share of deployments that were unplanned and happened because of a production incident.

**Reported result.** The 2025 model does **not** publish a new “percent per 25% adoption” figure. Figure 28 is standardized effects with 89% credible intervals. The prose states the direction, comparing two otherwise similar people:

- Higher software delivery throughput (a reversal of 2024).
- Higher software delivery instability (the unfavorable association remains).
- Higher individual effectiveness, code quality, team performance, organizational performance, product performance, and share of time in work the person calls valuable.
- No clear association with friction or burnout.

They report no evidence that the throughput gain blunts the harm instability still has on product performance and burnout.

Adoption context, same survey: 90% use AI at work; more than 80% believe it increased their productivity; 30% report little or no trust in AI-generated code (down from 39.2% in 2024). Median time interacting with AI on the most recent workday: two hours.

When this report quotes “1.5% reduction in throughput and 7.2% increase in instability per 25% adoption,” it is citing the 2024 PDF, not a new measurement.

**Caveat.** Still a survey association. The authors say the data cannot separate learning, tool changes, and organizational change. A contributor section (Matt Beane) says tracking lines generated and pull requests merged does not measure skill. That is an argument inside the report, not a measured coefficient.

### 3. DORA’s software delivery performance metrics (current definitions)

- **Org:** DORA.
- **Date:** Living guide. The five-metric split and the rework question enter in the 2024 report (source 1). The “instability” label is the 2025 wording (source 2). Wording checked 27 October 2026.
- **URL:** <https://dora.dev/guides/dora-metrics-four-keys/>
- **History of the split:** <https://dora.dev/guides/dora-metrics/history/>
- **Why primary:** Short canonical definitions, separate from any one year’s AI coefficients.

Throughput: change lead time, deployment frequency, failed deployment recovery time. Instability: change fail rate (deployments that need immediate intervention after they land) and deployment rework rate (unplanned deployments caused by a production incident). The guide says the five are outcomes. They do not explain why a team is slow or unstable.

**No AI coefficient on this page.** Use sources 1 and 2 for the adoption results.

### 4. Many SWE-bench-passing PRs would not be merged into main

- **Org:** METR. Parker Whitfill, Cheryl Wu, Joel Becker, Nate Rush.
- **Date:** 10 March 2026.
- **URL:** <https://metr.org/notes/2026-03-10-many-swe-bench-passing-prs-would-not-be-merged-into-main/>
- **Why primary:** This is the study that scores the same patches twice: SWE-bench’s automated grader, then a current maintainer’s merge decision. Leaderboard posts that mention the gap in a sentence are citing this note.

**Metric.** SWE-bench resolved means the automated tests pass (source 5). Maintainer merge means the current maintainer would accept the patch onto main. METR also records a softer bar: maintainer says the patch is at least 80% of the way to mergeable.

**Numbers.**

- 4 active maintainers, 3 of 12 SWE-bench Verified repos (scikit-learn, Sphinx, pytest), covering 95 of 500 issues (19%).
- 296 AI-generated pull requests reviewed. Patches that fail the grader are imputed as maintainer failures. A small check of grader failures found 1 of 27 issues (3.7%) where a maintainer would still have accepted a patch.
- Human “golden” patches that were actually merged historically: maintainers accepted 68% of them on this re-review (47 patches, half of the golden set, randomly chosen). Scores are also reported as a fraction of that 68% baseline.
- Averaged across models, the automated grader sits **24.2 percentage points** above the maintainer-merge decision (standard error 2.7).
- The summary line: roughly half of test-passing agent PRs from mid-2024 to mid/late-2025 would not be merged, after that noise adjustment.
- Unnormalized, maintainer merge rates are about one-third to one-half of the automated pass rates.
- The yearly gain in maintainer-merge rate is about 9.6 percentage points per year slower than the grader (standard error 5.5). METR calls this suggestive only: it is significant at 10%, not 5%, and it moves if the trend is fit on frontier models alone.

**Caveat.** Agents were not allowed to revise after review, so this is not a claim that they cannot reach a mergeable patch. Review had no CI. Maintainers were told the tests passed. Test-writing requirements were waived, which favors the agent. Issues are old, and a maintainer might recognize their own historical patch. The review is hypothetical, not a merge onto today’s main. Three repos, paid reviewers.

### 5. SWE-bench: Can Language Models Resolve Real-World GitHub Issues?

- **Org:** Princeton Language and Intelligence (Jimenez, Yang, Wettig, Yao, Narasimhan), with Pei (Chicago) and Press.
- **Date:** arXiv:2310.06770, 10 October 2023. Conference version: ICLR 2024.
- **URL:** <https://arxiv.org/abs/2310.06770>
- **Why primary:** Defines the benchmark score that later write-ups treat as “the agent solved the issue.”

**Metric.** A task is resolved only if the model’s patch applies and every `FAIL_TO_PASS` test and every `PASS_TO_PASS` test passes. `FAIL_TO_PASS` tests fail before the original fix and pass after it. `PASS_TO_PASS` tests pass both before and after, and are there to catch breakage. The reported number is the percentage of task instances resolved. Maintainer acceptance of the model’s patch is not part of the score.

The tasks themselves are built from pull requests that humans already merged and that edited tests. That construction fact is not a merge decision about a model patch.

**Numbers in the paper.** 2,294 tasks, 12 Python repositories. With a BM25 retriever, the best model they report, Claude 2, resolves **1.96%**. That is a 2023 result. It is not a current capability number. Do not replace it with a later leaderboard percentage and still call it this paper.

**SWE-bench Verified** (OpenAI, with the SWE-bench authors, 13 August 2024): <https://openai.com/index/introducing-swe-bench-verified/>. A human-filtered subset of **500** samples, screened for a well-specified issue and appropriately scoped tests. The pass rule is unchanged: both test sets must pass. Verified does not ask a maintainer whether a model patch should merge. Source 4 uses this subset. Source 6 explains a remaining hole in the same test rule.

**Caveat.** Tests are not an exhaustive specification. Passing them is the benchmark’s definition of success, which sources 4 and 6 then challenge on different grounds (maintainer standards, and behavior versus the developer’s patch).

### 6. Are “solved issues” in SWE-bench really solved correctly?

- **Org:** You Wang and Zhongxin Liu (Zhejiang University); Michael Pradel (University of Stuttgart).
- **Date:** arXiv:2503.15223, 19 March 2025 (revised 9 September 2025). ICSE 2025.
- **URL:** <https://arxiv.org/abs/2503.15223>
- **Proceedings:** <https://doi.org/10.1145/3744916.3764576>
- **Why primary:** Measures how often a patch that SWE-bench counts as correct still disagrees with the repository’s own tests or with the developer’s patch. This is not a maintainer-merge study. Source 4 is that study.

**Metric.** A plausible patch is one the SWE-bench harness accepts. The harness runs tests from files the original fixing pull request modified, not every test file in the repo. The authors re-run the developer test files, and they generate tests meant to expose a behavioral difference from the developer’s patch (PatchDiff).

**Numbers**, on plausible patches from three tools (CodeStory, LearnByInteract, OpenHands) on SWE-bench Verified, at most one plausible patch per tool per issue:

- **7.8%** of plausible patches fail once all developer-written test files are run. Issue resolution rate drops by **4.5** percentage points on average.
- **29.6%** of plausible patches can be driven into different behavior from the developer patch.
- Of those differences, a manual taxonomy on 77 patches (30% of the suspicious set) finds similar-but-divergent implementations (46.8%) and patches that change more behavior than the developer patch (27.3%).
- **28.6%** of that 77 are certainly incorrect.
- The abstract’s combined claim: these weaknesses inflate reported resolution rates by **6.2** percentage points. The body of the 19 March version extrapolates a similar figure (about 6.4 points) from the manual sample. Quote the abstract’s 6.2 as the authors’ summary, and the 7.8% / 4.5-point figures as the direct re-test.

**Caveat.** A behavioral difference from the historical developer patch is not automatically a wrong fix; only the manual slice is called certainly incorrect. The 28.6% is a sample of suspicious patches, not of all patches. Three agent systems, one benchmark subset. Does not answer “would the maintainer merge it?”

### 7. AI Copilot Code Quality (2025 research)

- **Org:** GitClear. William Harding, Alloy.dev Research.
- **Date:** February 2025.
- **URL:** <https://www.gitclear.com/ai_assistant_code_quality_2025_research/>
- **PDF:** <https://gitclear-public.s3.us-west-2.amazonaws.com/GitClear-AI-Copilot-Code-Quality-2025.pdf>
- **Why primary:** This is the public measurement behind “lines replaced within two weeks.” The dry run’s Rework row matches this definition. It does not match DORA’s deployment rework rate (sources 1–3).

**Metric.** A line is churned if it was authored, pushed, and then reverted or substantially revised within the next two weeks. GitClear describes that as a line that was incomplete or wrong when first pushed. Denominator below is meaningful changed lines in their corpus that year (whitespace-only edits excluded), not “lines an AI wrote.”

**Numbers** from appendix A1 (the summary table rounds the same series):

| Year | Changed lines | Churned lines | Churn share | Moved share | Copy/pasted share |
| --- | --- | --- | --- | --- | --- |
| 2020 | 27,433,911 | 837,906 | 3.05% | 24.17% | 8.86% |
| 2024 | 58,755,703 | 3,331,468 | 5.67% | 9.47% | 12.32% |

The summary table prints churn as 3.1% (2020) and 5.7% (2024), and copy/paste as 8.3% rising to 12.3%. Moved lines fall from about a quarter of changes to under a tenth. The corpus total cited in the report is 211 million meaningful changed lines, 2020–2024. The 2025 row in the appendix is a projection, not a measurement.

**Caveat.** Year-over-year mix in a vendor corpus during the years Copilot spread. Lines are not labeled AI-authored versus human-authored. The customer and repo mix changes (the appendix says the line counts are not identical to the prior year’s extract). Not peer-reviewed. The report treats the 2024 DORA stability drop as confirmation of a rising “defect rate.” That sentence is a secondary citation. GitClear did not count production defects. Use source 1 for the 7.2% figure.

### 8. Measuring the impact of early-2025 AI on experienced open-source developer productivity

- **Org:** METR. Joel Becker, Nate Rush, Beth Barnes, David Rein.
- **Date:** 10 July 2025. Paper: arXiv:2507.09089.
- **URL:** <https://metr.org/blog/2025-07-10-early-2025-ai-experienced-os-dev-study/>
- **Paper:** <https://arxiv.org/abs/2507.09089>
- **Replication:** <https://github.com/METR/Measuring-Early-2025-AI-on-Exp-OSS-Devs>
- **Why primary:** A randomized trial of task time on real issues in repositories the developers already maintain. It is the cleanest public split between felt speed and measured time. It is not a delivery-stability result, and it is not a harness result.

**Metric.** Each pre-specified issue is randomized to AI allowed or AI disallowed. Outcome is implementation time. The headline ratio is regression-adjusted because the AI-allowed issues were forecast, before the coin flip, as slightly harder. The unadjusted ratio of mean times is larger (about 34% more time) and is not the number METR asks readers to use.

**Numbers.** 16 developers, 246 tasks (136 AI-allowed, 110 not), mean about 2.0 hours, on repositories averaging about 23,000 stars, where they had about 5 years of experience. Tools at the February–June 2025 frontier, mostly Cursor Pro with Claude 3.5 or 3.7 Sonnet.

- Forecast before the task: AI would make the task **24%** shorter.
- After the study, the same developers estimated AI had made them **20%** faster.
- Adjusted measurement: AI-allowed tasks took **19%** longer. The replication script reports 0.188, with a 95% interval about 0.013 to 0.395 (roughly 1% to 40% longer). METR’s 24 February 2026 note rounds that interval to +2% to +39%.
- Economics and ML experts forecast speedups of about 39% and 38%.

**Caveat.** Experienced maintainers, familiar codebases, short pre-specified tasks, early-2025 tools, paid study. Scope cannot grow with the tool, because the issue is fixed before randomization. METR’s own 24 February 2026 update (<https://metr.org/blog/2026-02-24-uplift-update/>) says a late-2025 rerun is not a reliable replacement estimate: developers who most wanted AI declined the no-AI arm, pay was lower, and concurrent agents made time-on-task hard to count. Raw point estimates in that rerun lean toward a speedup and their intervals include zero. Do not cite them as the new effect. Do not cite the 19% from the 2025 DORA report; that report is quoting METR.

### 9. Deep learning-based code reviews: a paradigm shift or a double-edged sword?

- **Org:** Rosalia Tufano, Alberto Martin-Lopez, Ahmad Tayeb, Ozren Dabic, Sonia Haiduc, Gabriele Bavota. ICSE 2025 research track.
- **Date:** Presented 1 May 2025. arXiv:2411.11401.
- **URL:** <https://arxiv.org/abs/2411.11401>
- **Proceedings:** <https://doi.org/10.1109/icse55347.2025.00060>
- **Why primary:** A controlled measurement of review time and of which defects a human finds when an automated review is already on the table. DORA’s “+3.1% code review speed” (source 1) is a survey answer about speed. This paper times the review.

**Metric.** Review cost is minutes spent by a professional reviewer. Review quality is issues found, split by severity. Three conditions: manual review; review starting from a model-written review; review starting from a correct automated review.

**Numbers.** 29 professional developers, more than 50 hours of recorded review.

| Condition | Mean | Median |
| --- | --- | --- |
| Manual | 42 min | 37 min |
| Model-written review provided | 56 min | 42 min |
| Correct automated review provided | 57 min | 50 min |

Their regression does not find a statistically significant effect of condition on time. The means are higher when an automated review is present, which is the opposite of a time saving, and the authors say the automated review did not save time. Reviewers who started from the model found more low-severity issues and not more high-severity issues, and they looked where the model pointed rather than searching the rest of the change. They judged most model-flagged issues valid. Confidence did not rise.

**Caveat.** Short constructed programs, not a production diff and not this harness. The “correct review” condition is an oracle, not a live agent. Small sample. “No significant time effect” and “means were higher” should both be reported; neither is a precise percent saving.

### 10. Productivity assessment of neural code completion

- **Org:** GitHub. Albert Ziegler, Eirini Kalliamvakou, Shawn Simister, Ganesh Sittampalam, Alice Li, Andrew Rice, Devon Rifkin, Edward Aftandilian.
- **Date:** Survey 10 February–6 March 2022. MAPS ’22, 13 June 2022.
- **URL:** <https://arxiv.org/abs/2205.06537>
- **DOI:** <https://doi.org/10.1145/3520312.3534864>
- **Why primary:** This is the study that applies the SPACE framework to an AI coding tool and then checks which telemetry tracks the survey. SPACE itself (Forsgren et al., ACM Queue, 2021, <https://queue.acm.org/detail.cfm?id=3454124>) is the framework paper and is not an AI study. Use this paper, or source 11, when SPACE is tied to AI coding.

**Metric.** SPACE dimensions in the survey: satisfaction and well-being, performance, activity, communication and collaboration, efficiency and flow. Acceptance rate: fraction of shown completions the developer accepts into the file. They also measure whether accepted code is still unchanged after 30 and 120 minutes (a short persistence / anti-churn signal).

**Numbers.** 2,047 responses. In this Copilot population the acceptance rate is **27%**, and mean daily accepted completions per user exceed 31. Acceptance rate is the strongest correlate of aggregate **perceived** productivity (ρ = **0.24**, p < 0.0001), stronger than the persistence measures. The authors write that acceptance rate is a poor way to judge the value of a system across products, and that perceived productivity is not necessarily actual productivity.

**Caveat.** Perception in a 2022 technical preview. The correlation is with how productive people felt. Source 8 is later evidence that this feeling can have the wrong sign. Do not promote acceptance rate, or “suggestions accepted,” to a harness outcome. The ρ of 0.24 is a modest correlation, not an effect on delivery.

### 11. The SPACE of AI: real-world lessons on AI’s impact on developers

- **Org:** Brian Houck, Travis Lowdermilk, Cody Beyer, Steven Clarke, Ben Hanrahan.
- **Date:** arXiv:2508.00178, 31 July 2025.
- **URL:** <https://arxiv.org/abs/2508.00178>
- **Why primary:** A later study whose outcome is the SPACE dimensions applied to AI coding. The 2025 DORA report cites it for the claim that developers rate AI positively on those dimensions. This entry uses the abstract, not a line-by-line audit of the PDF.

**Metric.** Self-reported effect of AI on satisfaction, performance, activity, collaboration, and efficiency. Not change failure rate, not churn, not merge.

**Numbers in the abstract.** Survey of over 500 developers, plus interviews and observation. Broad adoption. Developers report higher efficiency and satisfaction, especially on routine tasks. Less evidence of an effect on collaboration. Benefits vary with task complexity, how the person uses the tool, and whether the team has adopted it.

**Caveat.** Perception. No delivery denominator. DORA’s citation of this paper does not turn those ratings into the instability result in sources 1 and 2.

### 12. Harness engineering: leveraging Codex in an agent-first world

- **Org:** OpenAI. Ryan Lopopolo.
- **Date:** 11 February 2026.
- **URL:** <https://openai.com/index/harness-engineering/>
- **Label:** Single-team anecdote. One internal product. Not a benchmark and not a multi-team study.

**What they counted.** Lines in the repository, pull requests opened and merged, engineers, and calendar time. They do not report change failure rate, deployment rework, two-week churn, escaped defects, or review minutes.

**Numbers, as stated.**

- Zero lines of manually written code. Application, tests, CI, docs, observability, and internal tooling included.
- Five months in, on the order of **a million lines**, across application logic, infrastructure, tooling, docs, and internal utilities.
- About **1,500** pull requests opened and merged.
- Team of three engineers driving Codex, stated average **3.5 pull requests per engineer per day**. The team later grew to seven, and they say throughput rose as it grew. The post does not publish the day count behind the 3.5.
- They estimate the build took about one tenth the time of writing it by hand. No measured baseline is in the post.
- Hundreds of internal users, including daily power users. The post uses that to say the volume was not for its own sake. It is still not a defect rate.
- Their scarce resource, in their words: human time and attention. At this throughput they run with minimal blocking merge gates, because “corrections are cheap, and waiting is expensive.” The next sentence limits that tradeoff: it would be irresponsible in a low-throughput environment.

**Caveat.** One team, one internal beta, no stability denominator. Later talks that quote a different PR rate are not this post. Do not import those rates here.

---

## Corrections to the dry run’s §18 table

The table is a reasonable local scoreboard. It should not be described as DORA’s metrics. Section 18 says the rows are “drawn from the DORA-style list in the Augment write-up.” Augment’s page (source rejected below) defines task-resolution-by-tests, two-week churn, verification tax, and defect escape. DORA’s five are lead time, deployment frequency, recovery time, change fail rate, and deployment rework rate. Oracle integrity and false-green are local to this dry run; no source above measures them.

| Dry-run row | What to treat it as | Correction |
| --- | --- | --- |
| Slice resolution | Share of slices that reach `AUDIT_PASS` without a steward rewrite | Keep as a local harness signal. Do not score it with SWE-bench “% resolved.” That percentage means the patch passed `FAIL_TO_PASS` and `PASS_TO_PASS` (source 5). On Verified, maintainers then reject on the order of 24 percentage points more of those passes (source 4), and a separate study finds about a 6 point inflation against the developer’s own behavior (source 6). |
| Rework | Share of a slice’s lines replaced within a short window | This is GitClear churn: authored, pushed, reverted or substantially revised within **two weeks** (source 7). 2020: 3.05% of changed lines (837,906 / 27,433,911). 2024: 5.67% (3,331,468 / 58,755,703). That series is not labeled by AI authorship. DORA’s **deployment rework rate** is a different metric: unplanned deployments to fix a user-facing bug or production incident (sources 1–3). Do not put both under one name. |
| Escape | Defects found after merge, tagged by module | Keep the local tag. DORA change failure is narrower: a deployment that fails **in production**, where a user can experience it, and then needs a hotfix or rollback. A defect caught after merge and before production is a DORA success (source 1, footnote). The 2024 “−7.2% stability” estimate is not a count of escaped defects per module. |
| Verification tax | Steward time per slice, mostly at G2 rather than in the diff | No primary above measures steward time at a spec gate. The closest timed review is source 9: providing an automated review did not reduce reviewer minutes (means 42 manual vs 56–57 with an automated review; regression not significant) and did not surface more high-severity issues. DORA’s +3.1% “code review speed” (source 1) is a self-report that reviews got faster, and the same chapter says faster does not mean more thorough. |
| Oracle integrity | Attempts to weaken an accepted example, target caught | No external primary. Keep it local. It is the right kind of metric: a count of a specific failure the harness is supposed to stop. |
| False-green | Verifier rejects where the implementer’s tests were already green | No external primary under this name. Sources 4 and 6 are the public versions of the same idea: tests green, maintainer or developer-oracle not satisfied. |

**Instability sentence.** “Higher AI adoption can raise delivery instability” matches both DORA waves, and it is the finding to cite. Attach the year and the denominator:

- **2024 (source 1):** estimated **−7.2% delivery stability** and **−1.5% delivery throughput** per **25%** higher AI adoption, nearly 3,000 respondents. Association, not an experiment. Same chapter: perceived code quality +3.4% and perceived review speed +3.1%, which did not show up as better delivery.
- **2025 (source 2):** throughput association **turns positive**. Instability association **stays unfavorable**. No new 7.2% estimate. Instability still hurts product performance and burnout in their checks; they do not find that extra throughput cancels that harm.

The gates-until-escape-is-measured stance is stronger than OpenAI’s post. Source 12 says low merge friction is reasonable only at their throughput, and it publishes no escape rate. Source 2 says the instability association did not disappear in the year throughput turned positive.

**Perception and acceptance rate.** Source 10 is why “suggestions accepted” stays off the scoreboard: it was the best predictor of **felt** productivity (ρ 0.24) in a Copilot survey, and the authors say that is not actual productivity. Source 8 is the later measured contradiction for experienced maintainers on their own repos, early 2025: 19% slower, while they believed they were 20% faster. Source 11’s positive SPACE ratings are also self-report.

**OpenAI volume.** The dry run’s “on the order of a million lines” and “about 1,500 merged pull requests” match source 12. Add the denominator the post itself prints: three engineers, five months, stated average 3.5 merged pull requests per engineer per day, later seven engineers. One internal product. The post’s scarce resource is “human time and attention,” and the cheap-corrections sentence is explicitly limited to a high-throughput setting. It does not say that output is a success metric.

---

## Rejected secondary citations

Do not use these for a DORA coefficient or a SWE-bench pass rate.

| Citation | Why it is out |
| --- | --- |
| Augment Code, “Harness Engineering for AI Coding Agents,” 16 April 2026, updated 29 June 2026. <https://www.augmentcode.com/guides/harness-engineering-ai-coding-agents> | This is the page §18 is drawing from. It says “the DORA report” found higher AI adoption correlated with **both** higher throughput and higher instability, with no year, no URL, and no “per 25% adoption.” That sentence matches only the 2025 direction and drops the 2024 throughput sign. It also states top-agent SWE-bench Verified resolution of 65–76.8% without a paper, and mentions the METR merge gap only as a clause. Its two-week churn definition is GitClear’s, without the corpus denominator. |
| “Impact of Generative AI in Software Development,” <https://dora.dev/ai/gen-ai-report/dora-impact-of-generative-ai-in-software-development.pdf> | A PDF that still prints the 2024 “−1.5% throughput / −7.2% stability” result under a 2025 version stamp. It is not the September 2025 finding that throughput turned positive. Cite sources 1 and 2 instead. |
| GitClear’s prose that the 2024 DORA result is a rising defect rate | Secondary use of source 1. GitClear’s own data is churn and copy/paste shares, not defects found in production. |
| The 2025 DORA report’s sentence quoting METR’s 19% and 20% | Accurate pointer. The measurement is source 8. |
| LeadDev, “AI-generated code passes far more automated tests than human,” summarizing the METR maintainer note | Journalism on source 4. The 24.2 point gap and the 68% golden-patch baseline belong to the METR note. |
| TechCrunch, 21 February 2025, “Report: AI coding assistants aren’t a panacea” | News story on the GitClear report. Use source 7 for the line counts. |
| SWE-bench leaderboard pages, if “% resolved” is read as “maintainers would merge” | The leaderboard uses source 5’s test rule. Source 4 is the merge comparison. The 1.96% Claude 2 figure is the 2023 paper’s result, not a live score. |
| Podcasts, newsletters, and reposts of Lopopolo that quote a later pull-request rate | Not the 11 February 2026 post. Source 12 is the primary for the million-line, 1,500-PR, and 3.5-per-engineer-per-day figures. |
| Apiiro security-finding volume, as repeated by Augment | Left unverified. The dry run already marks it second-hand. Not promoted. |
