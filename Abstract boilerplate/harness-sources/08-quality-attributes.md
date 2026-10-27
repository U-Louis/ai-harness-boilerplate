# Quality attributes — primary sources

Checked 27 October 2026. This note ranks primary sources for the bars and checks in section 10 of `ai-harness-state-of-the-art.md`. It does not edit that file.

Scope is the product an agent writes: security, privacy, traffic and capacity, efficiency, reliability, and operability, plus the change-safety checks that keep a release candidate tied to the evidence. Prompt-injection defenses for the coding agent are out of scope except where a source uses them to decide who may merge.

A bar is something the constitution can fail: a binary rule, or a number that names the SLI, the threshold, the proportion, the measurement point, and the window. A study percentage is not a bar. An example prompt from one internal product is not a bar.

## Corrections to the dry run

### Security is three gates, and “trust boundary crossed” is the wrong trigger

Section 2 says security is checked on every slice that crosses a trust boundary, with a person reading the diff when the risk class says so. Section 10 then runs the whole security set (cross-actor cases, secret scan, dependency scan, threat notes) on every risk-class slice and on any new entry point, and runs it again in full at M6.

That trigger is too wide and too narrow.

Too wide: OWASP ASVS 5.0.0 is a verification standard for the application, about 350 requirements, at a level the organization chooses. Level 1 is the minimum and about 20% of the requirements. Level 2 is what most applications should aim at, and meeting it means the L1 set plus the L2 set, about 70% of the standard. The standard does not prescribe a CI pipeline and does not say to re-verify the whole level on every change. NIST SSDF v1.1 says to decide, on risk, whether code review and executable security tests apply to a change (`PW.7.1`, `PW.8.1`). Replaying the full set on every boundary crossing is how batches get large. Both DORA and Apiiro tie large changes to the instability and the review miss.

Too narrow: secrets, new dependencies, and input validation fail on slices that never cross a new trust boundary. A logging change can print a token. A UI slice can add a package. A copy change on an existing endpoint can drop a field check. Those are every-change checks. The other miss is the opposite shape: the dangerous diff often does not look like “new entry point.” It edits the one allow function, a migration, or a workflow permission. Apiiro’s own example is one pull request that changed an authorization header across services and left one service behind.

Use three triggers.

| Trigger | What fails the slice | Source |
| --- | --- | --- |
| Every agent change | Known-pattern secret blocks the push. A pipeline secret scan still runs, because forge push protection skips or times out on very large pushes. A new dependency is named and reviewed, or the slice stops. New input is validated at the trusted service layer against an allow-list, a shape, or a logical limit. | ASVS `v5.0.0-2.2.1`, `v5.0.0-2.2.2`, `v5.0.0-13.3.1`; SSDF `PW.4`; GitHub and GitLab secret push protection; Top 10:2025 A03 |
| The diff touches a security-critical path | One independent allow and one deny for the access this diff changes: other actor, missing actor, and the field that must not come back. A person reads that path. Critical paths are authentication, authorization, cryptography, data shape and migrations, and pipeline or deployment policy. A spec flag that says “trust boundary crossed” is not the list. | ASVS `v5.0.0-8.2.1`, `v5.0.0-8.2.2` (L1), `v5.0.0-8.2.3` (L2); API1 and API3; AISVS v1.0 `AC.4.4`; the dry run’s existing G4 list is already close to this path list |
| Release, at the chosen ASVS level | The full level the constitution adopted, with evidence. M6 is this verification. It is not a waiver session, and it is not a substitute for the two rows above. | ASVS levels; SSDF `PW.8.2`; AISVS `AC.1.2` (the gates stay mandatory whether or not AI wrote the code) |

AISVS v1.0 Appendix C is stricter than the dry run in one place, and the dry run should say so rather than look compliant. `AC.4.1` is Level 1: every AI-generated change is reviewed by a qualified human who is not the person who asked for the generation, and the agent does not count as that human. `AC.8.1` is Level 1: an autonomous agent cannot approve, merge, sign, or deploy its own artifact, and the forge, the CI system, and the registry enforce that. Policy text is not enough. The dry run’s evidence-merge for low-risk slices is a deliberate departure from `AC.4.1`. The role split matches `AC.8.1` only when the forge actually refuses the implementer’s self-merge.

`AC.4.2` is Level 2 and asks for SAST, IAST, DAST, secret scanning, IaC scanning, and SCA on every pull request that contains AI-generated code. That is the level target, not the M1 pipeline. Secret scan, dependency check, and the allow and validation cases above are the checks that can fail closed on a slice now. Claiming `AC.4.2` while skipping IAST and DAST is a level miss, not a reading of the requirement. `AC.4.3` blocks merge on a critical finding, defined as CVSS 9.0 or greater or the organization’s own severity policy. The 9.0 figure is their example of “critical,” with an explicit escape to local policy. Do not hard-code 9.0.

### Placeholder budgets stay out of the constitution

Section 10 already says the Desk numbers are examples to replace. Section 19 says a budget without a legible trace is not a gate. Two sharper corrections:

OpenAI’s “startup under 800 ms” and “no span in these four critical user journeys exceeds two seconds” are example prompts in one internal product, written to show that a prompt becomes tractable once the agent can query logs, metrics, and traces. The post does not report that the product met either number. They are not p50, not p95, and not a SLO. Copying them into `constitution.md` makes an anecdote look like a bar.

A latency bar has the shape in the Google SRE book. Name the requests, the proportion, the threshold, where it is measured, and the window. The book’s own illustration is “99% of Get RPC calls will complete in less than 100 ms,” and that illustration is not a target for Desk. Meeting the objective 100% of the time is the wrong target; the complement of the SLO is the error budget. Record the four golden signals: latency of successes and of failures separately, traffic, errors, and saturation. A request slower than the committed threshold counts as an error, which is how the same book defines an error “by policy.” One span under a ceiling can pass while the user request fails, because fan-out multiplies the tail (Dean and Barroso). The user-facing journey is the SLI. Fan-out, page size, payload size, and operations per request are bounds you can fail before any millisecond number exists (OWASP API4:2023, ASVS `v5.0.0-2.2.1`).

Full load at a stated concurrency stays an M6 and pre-promote check. On the slice that adds a list, the fail is static: the page size cannot be “all,” and the trace’s storage round-trips do not grow with the page length. Running the full load scenario on every new public entry point works against the small-batch finding in DORA and Apiiro.

The SRE book’s “N+1” means a spare for redundancy. It is not the query anti-pattern. The efficiency fail stays: a list request’s storage round-trips scale with the list length, or one request can force an unbounded read.

### Smaller corrections

Privacy classes named `public`, `account`, and `secret` are a dry-run vocabulary. The requirement is a documented protection level per sensitive field, covering return, logging, retention, and access (`v5.0.0-14.1.1`, `v5.0.0-14.1.2`, `v5.0.0-14.2.4`, all L2), plus field-level allow (`v5.0.0-8.2.3`, L2; API3:2023). Re-check on a schema change. A status read that returns notes fails that field rule. L1 does not require the classification document; deferring it inside a grade-C walking skeleton matches the level model, and promoting without it does not match L2.

“The process refuses to start without required configuration” is a sound harness check. It is not a sentence in ASVS. The quoted requirements are: secrets are not in source or build artifacts (`v5.0.0-13.3.1`, L2), and an unexpected failure does not fail open (`v5.0.0-16.5.3`, L2). A timeout becomes a known error through a deadline on the downstream call and a generic consumer error (`v5.0.0-16.5.1`, `v5.0.0-16.5.2`). A retried claim must not create two holders (`v5.0.0-2.3.4`, L2, limited resources cannot be double-booked). Retries are bounded, and a non-retriable error is not retried. The SRE figures for queue depth, a 5-second RPC deadline, and 60 retries per minute are illustrations.

Operability: a correlation id and one structured log shape are how a request meets `v5.0.0-16.2.1` (when, where, who, what) and `v5.0.0-16.2.4` (a processor can correlate the entries). Credentials and payment data are not logged; session tokens are masked if logged at all (`v5.0.0-16.2.5`). Logging every authorization decision, including sensitive-data access, is L3 (`v5.0.0-16.3.2`). Failed authorization attempts are L2. The OpenAI bar on top of that is legibility: the agent can query the journey trace in the isolated boot. Until that query works, a numeric budget is not a gate.

Rate limits on account and item creation are real, and they are not L1. They sit in ASVS `v5.0.0-2.4.1` (L2, anti-automation), Top 10:2025 A01 (rate-limit API access), and API4:2023 (a missing rate limit is one way the API is vulnerable).

DORA’s 2024 estimates, a 1.5% throughput drop and a 7.2% stability drop per 25% more AI adoption, are survey-model estimates. The 2025 report keeps a negative relationship with delivery stability and reports a positive relationship with throughput. Section 18’s sentence that higher AI adoption can raise delivery instability still matches 2025. Reading it as “throughput also fell” does not. Neither year’s percentage is a gate. The control DORA names is small changes, automated tests, and fast feedback.

## Ranked sources

### 1. OWASP Application Security Verification Standard 5.0.0

- Org: OWASP
- Date: 30 May 2025
- URL: https://asvs.dev/ and the release text https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/en/0x03-What-is-the-ASVS.md
- Requirements used below are from the v5.0.0 CSV: https://github.com/OWASP/ASVS/blob/v5.0.0/5.0/docs_en/OWASP_Application_Security_Verification_Standard_5.0.0_en.csv
- Why primary: the current OWASP verification standard for the application itself. A requirement is written to pass or fail. Version 5.0.0 says it specifies product outcomes and does not prescribe the CI pipeline. Cite requirements as `v5.0.0-<id>` so a later renumber does not silently move the bar.

Bars and checks:

- One allow decision, enforced in a trusted service layer, not in client-side script (`v5.0.0-8.3.1`, L1). Function-level allow (`v5.0.0-8.2.1`, L1). Data-item allow, the IDOR and BOLA check (`v5.0.0-8.2.2`, L1). Field-level allow (`v5.0.0-8.2.3`, L2). The rules are documented (`v5.0.0-8.1.1`, L1; `v5.0.0-8.1.2`, L2).
- Input is validated against an allow-list, a shape, or logical limits. At L1 that covers input used for a business or security decision, including a number that controls how many times a loop runs. At L2 it covers all input. Validation is at a trusted service layer (`v5.0.0-2.2.1`, `v5.0.0-2.2.2`).
- Limited resources cannot be double-booked by replaying a request (`v5.0.0-2.3.4`, L2). That is the idempotent-claim check.
- Secrets are created and stored in a secrets manager. They are not in application source and not in build artifacts (`v5.0.0-13.3.1`, L2).
- Sensitive data has a protection level, and the level states encryption, retention, logging, and access (`v5.0.0-14.1.1`, `v5.0.0-14.1.2`, `v5.0.0-14.2.4`, L2). The consumer error for an unexpected or security-sensitive failure is generic: no stack trace, query, secret, or token (`v5.0.0-16.5.1`, L2). The application fails securely rather than fail-open (`v5.0.0-16.5.3`, L2), and a dead dependency degrades or opens the circuit instead of hanging (`v5.0.0-16.5.2`, L2).
- Security logs carry when, where, who, and what, in a form the log processor can correlate (`v5.0.0-16.2.1`, `v5.0.0-16.2.4`, L2). Logging follows the protection level (`v5.0.0-16.2.5`, L2). Failed authorization is logged at L2; logging every authorization decision is L3 (`v5.0.0-16.3.2`).
- For each external service, L3 asks the documentation to state pool limits, timeouts, and retry limits, delays, and back-off, and to keep synchronous retries short or off (`v5.0.0-13.1.2`, `v5.0.0-13.1.3`). That is release documentation for a high-assurance app, not an M3 slice gate.
- Anti-automation on functions whose abuse exfiltrates data, fills storage, or burns quota is L2 (`v5.0.0-2.4.1`).

Check: the verifier or the tool shows pass or fail for the requirement ids the slice’s level makes due, with the command and the output. A prose threat note does not satisfy a requirement. ASVS’s own rule is that each requirement ends in pass or fail.

### 2. OWASP API Security Top 10, 2023 edition

- Org: OWASP
- Date: 2023 edition
- URL: https://owasp.org/API-Security/editions/2023/en/0x11-t10/ with API1 https://owasp.org/API-Security/editions/2023/en/0xa1-broken-object-level-authorization/ API3 https://owasp.org/API-Security/editions/2023/en/0xa3-broken-object-property-level-authorization/ and API4 https://owasp.org/API-Security/editions/2023/en/0xa4-unrestricted-resource-consumption/
- Why primary: the API-specific fail conditions for the dry run’s cross-actor cases, private fields, and unbounded reads. ASVS points at the same failure names (BOLA, BOPLA). This list says when the API is vulnerable without inventing a latency number.

Bars and checks:

- API1:2023 Broken Object Level Authorization. Object-level authorization is considered in every function that accesses a data source with an identifier from the user. Check: member B cannot read or change A’s item; a missing actor is refused. This is the per-access case in the table above, including a function that reuses an old entry point.
- API3:2023 Broken Object Property Level Authorization. The response returns only properties the caller may see, and the request cannot write properties the caller may not set. Check: the status read returns state and holder, and does not return notes. A schema change re-runs this case. That is the privacy bar, in place of the three homemade class names.
- API4:2023 Unrestricted Resource Consumption. The API is vulnerable if any of these is missing or set inappropriately: execution timeout, maximum memory, file descriptors, processes, upload size, number of operations in one client request, number of records per page, and a spending limit on paid third parties. Prevention adds a maximum on string length and array length, a rate limit, and server-side validation of the parameter that sets the page size. Check: the list rejects an unbounded page size; the journey trace shows the read does not grow with total data; a payload over the stated size is rejected. The dollar figures in API4’s scenario 3 (a bill moving from about $13 to $8k) are an illustration. Do not encode them.

### 3. OWASP Top 10:2025

- Org: OWASP
- Date: 2025 edition, the current release on the OWASP Top 10 project page as of 27 October 2026
- URL: https://owasp.org/Top10/2025/ and A01 https://owasp.org/Top10/2025/A01_2025-Broken_Access_Control/ and A10 https://owasp.org/Top10/2025/A10_2025-Mishandling_of_Exceptional_Conditions/
- Why primary: the current awareness ranking for web application risk. It is not a verification standard. ASVS is the pass/fail list. Top 10 tells you which failures are common enough to deserve a case, and its prevention bullets are concrete.

Bars and checks:

- A01:2025 Broken Access Control, still ranked first. Prevention that maps onto the dry run: deny by default except for public resources; implement access control once and reuse it; enforce record ownership; log access-control failures; rate-limit API access; keep the check in trusted server-side code. Check: a second access mechanism fails plan alignment; the other-actor and missing-actor cases exist for the path the diff changes.
- A10:2025 Mishandling of Exceptional Conditions, new in this edition. It covers failing open, errors that reveal internals, and partial transactions that are not rolled back. Prevention: one centralized error path, catch the failure where it happens, roll the whole transaction back, and put a limit on rate, quota, and resources because “nothing in information technology should be limitless.” Check: a storage timeout returns the known error and does not report success; a replayed claim does not leave two holders; the consumer body has the error code and not the stack or the query.
- A03:2025 Software Supply Chain Failures is the expanded home for dependency and build compromise. The check is still SSDF `PW.4` and the dependency row: a new dependency is an explicit decision, pinned, and scanned. Top 10 does not define the pin format.
- A09:2025 Security Logging and Alerting Failures names the operability gap. The testable requirements are the ASVS V16 ids, not the Top 10 chapter.

The incidence table is not a bar. The introduction says that on average 3.73% of applications in the contributed data had one or more of the 40 CWEs mapped to A01. The A01 score table lists an average incidence rate of 3.74% and a maximum coverage of 100%. The A01 prose also says “100% of the applications tested were found to have some form of broken access control.” That sentence does not match the incidence column. Coverage is how widely testers looked. Do not encode 100%, 3.73%, or 3.74% as a probability that a slice is broken.

### 4. OWASP AISVS 1.0, Appendix C, AI-assisted secure coding

- Org: OWASP
- Date: 24 June 2026 (the project’s stated 1.0 release). The GitHub release object dated 16 October 2026 is a retrospective record of that same tag.
- URL: https://owasp.org/www-project-artificial-intelligence-security-verification-standard-aisvs-docs/ and https://github.com/OWASP/AISVS/blob/v1.0/1.0/en/0x92-Appendix-C_AI_for_Code_Generation.md
- Why primary: the stable OWASP catalogue of testable requirements for code that an assistant or agent wrote. The main AISVS chapters are about AI systems (models, prompts, agents as the product). Appendix C is the part about the product code and the pipeline. It is an SSDLC catalogue with levels. It is not a latency SLO.

Bars and checks, limited to the ones that change section 10:

- `AC.4.1` (L1). Human review of every AI-generated change, by someone other than the requester. The agent is not the human reviewer. The dry run does not do this. Record the departure.
- `AC.4.2` (L2). Automated security testing on every AI pull request: SAST, IAST, DAST, secret scanning, IaC scanning, SCA. See the correction above for what is in force before that level is claimed.
- `AC.4.3` (L2). A critical finding blocks merge. CVSS greater than or equal to 9.0, or the organization’s equivalent, with a written human exception to bypass. The 9.0 value is not mandatory if local policy names another critical threshold.
- `AC.4.4` (L2). Elevated review when AI touched authentication, authorization, cryptography, IAM, CI/CD workflow, deployment manifests, or sandbox and network policy. This is the human-read trigger. It is a path class.
- `AC.4.5` (L3). Property-based or differential fuzz tests for input validation, authorization, and deserialization on security-critical behavior. Release hardening, not every slice.
- `AC.8.1` (L1). The agent cannot approve, merge, sign, or deploy what it generated. Enforced in the forge, CI, and registry.
- `AC.7.2` (L2) and `AC.7.4` (L2). AI-generated pipeline and infrastructure changes need a human before they run outside a hermetic sandbox. High-impact workflow permissions need dual control. That matches the dry run’s pipeline risk class, and it is enforced by the platform.
- `AC.3.1` (L1). Secrets, credentials, and personal data are not placed in prompts. Enforced in pre-commit, the IDE, and CI. This is a harness check on the agent’s context, separate from secrets in product source.

The rest of Appendix C (tool threat models, prompt injection through pull-request text, fork campaigns, AI bill of materials) is about the coding agent as an attack surface. Useful later. It does not set a Desk latency, capacity, or privacy bar.

### 5. NIST SP 800-218, Secure Software Development Framework (SSDF) Version 1.1

- Org: NIST
- Date: 3 February 2022 (final; CSRC lists publication as February 2022)
- URL: https://doi.org/10.6028/NIST.SP.800-218 and https://csrc.nist.gov/pubs/sp/800/218/final
- Why primary: the risk-based federal baseline for secure development practices. It maps to ASVS and tells you the practice is an outcome, not a tool. No NIST publication turned up under the name “SSDE.” SP 800-218r1 (SSDF 1.2) was still an initial public draft under comment review as of this check. Do not cite 1.2 as the final bar.

Bars and checks:

- Not every practice applies to every change. The publication says to use a risk-based approach, and that any stated frequency is notional. `PW.7.1` is “determine whether” human-readable review or code analysis should be used. `PW.8.1` is “determine whether” executable testing should be used, and which kinds. `PW.7.2` and `PW.8.2` then say: do the review or the test you selected, and record and triage the issues in the workflow. A missing field in that record stops the slice. A summary is not the record.
- `PW.1` is where security requirements are identified and tracked before the code. That is the constitution and the spec, not a scan after the fact.
- `PW.4` is reuse of software that has already been secured, and verification that third-party components still meet the requirements, including a fresh look when the context changes. Check: a new dependency is an explicit spec decision; an undeclared dependency fails.
- `PS.2` and `PS.3` are release integrity and archived provenance. The release candidate’s evidence points at the artifact that was actually built. SLSA Build L2 below is one way to satisfy the integrity half.

NIST SP 800-218A (26 July 2024, https://doi.org/10.6028/NIST.SP.800-218A) is the SSDF community profile for generative AI and dual-use foundation models. Its scope is model development: data sourcing, training, fine-tuning, evaluation, and integrating a model into other software. Deployment and operation of AI systems are out of scope. It says its tasks do not distinguish human-written from AI-generated source code, because all source code is evaluated before use. That sentence supports “the generator is not the assurance boundary.” It does not add a product bar for an agent-written application. Do not use 800-218A as the quality-attribute standard for Desk.

### 6. Site Reliability Engineering (Google)

- Org: Google, published by O’Reilly. Edited by Betsy Beyer, Chris Jones, Jennifer Petoff, and Niall Richard Murphy.
- Date: 2016
- URLs: four golden signals https://sre.google/sre-book/monitoring-distributed-systems/ service level objectives https://sre.google/sre-book/service-level-objectives/ cascading failures https://sre.google/sre-book/addressing-cascading-failures/
- Why primary: the engineering primary for what a numeric budget is, and for rejecting excess work instead of queueing without bound. The numbers in the chapters are worked examples.

Bars and checks:

- An SLO states the proportion, the threshold, the request class, the measurement point, and the window. “99% of Get RPC calls will complete in less than 100 ms” is the book’s example of that shape. It is not Desk’s budget. Several thresholds belong on one journey when the tail matters (the book’s further illustration: 90% under 1 ms, 99% under 10 ms, 99.9% under 100 ms). Insisting on 100% is described as unrealistic and undesirable. The error budget is the rate at which the SLO may be missed. A breach of the budget stops non-urgent change. The dry run’s promote gate is the place for that, once a real SLO exists.
- The four golden signals are latency, traffic, errors, and saturation. Latency of successful requests and latency of failed requests are separate. Errors include explicit failures, implicit wrong content, and policy failures: if the commitment is one second, a slower request is an error. The “one second” is the example of a commitment. Saturation is the constrained resource, and many systems degrade before 100% utilization. A load test that reports only a mean is the failure mode the chapter warns about: an average of 100 ms at 1,000 requests per second can hide 1% of requests at 5 seconds. Those two figures are an illustration (“might easily”), not a measurement of Desk and not a Google SLO.
- Capacity check, once a real SLO and a stated load exist: record the four signals against that SLO. Fail on error rate over the budget, latency over the budget, or saturation with no reject path.
- Cascading failures. Prefer a short queue, and reject when it is full, rather than letting latency grow with queue length. One pattern given is HTTP 503 once in-flight requests exceed a chosen number. The suggestion of a queue at 50% or less of the thread pool is an example for steady traffic (“usually better,” “e.g.”). Always set a deadline; a missing deadline lets a finished incident keep consuming the caller. Propagate the deadline. Limit retries per request. A process-wide retry budget is described with the example “only allow 60 retries per minute.” A code fragment sets an RPC timeout of 5 seconds as an illustration. Do not copy 50%, 60 per minute, or 5 seconds. The binary rules are: excess work is rejected, every downstream call has a deadline, a retry is capped, and a permanent error is not retried.
- “N+1” in this book is spare capacity (N+0 is zero redundancy). It does not define the query anti-pattern.

### 7. The Tail at Scale

- Org: Google. Jeffrey Dean and Luiz André Barroso, Communications of the ACM.
- Date: February 2013 (CACM page dated 1 February 2013)
- URL: https://cacm.acm.org/research/the-tail-at-scale/
- Why primary: the research note on why a per-span ceiling is the wrong user-facing bar once a request fans out.

Bars and checks:

- The user-visible request is the SLI. Variability in one component is magnified by parallel fan-out. The paper’s hypothetical: a server that usually answers in 10 ms, with a 99th percentile of 1 second, makes 1 user request in 100 slow if the request hits one server; if the request waits on 100 such servers, 63% of user requests take more than one second. That arithmetic is a hypothetical, marked as such in the paper. A separate table from one real Google service reports a 99th percentile of 10 ms for a single random leaf request and 140 ms for all leaves to finish, measured at the root. That pair is one service’s measurement, not a universal ratio.
- Check: the constitution bounds fan-out and page size. The M6 latency SLO is on the journey, measured at the boundary the user hits. A trace check that every span is under two seconds can pass while the joined request misses the SLO. The two-second span prompt in the OpenAI post has this hole.
- “Systems that respond within 100 ms feel more fluid” cites earlier UX work. It is not a Desk SLO. The hedged-request result later in the paper (a Bigtable benchmark, 99.9th percentile from 1,800 ms to 74 ms) is one benchmark of one technique. It is not a required design.

### 8. Harness engineering: leveraging Codex in an agent-first world

- Org: OpenAI. Ryan Lopopolo.
- Date: 11 February 2026
- URL: https://openai.com/index/harness-engineering/
- Why primary: the published note on making runtime behavior legible to the agent that has to enforce a budget. It is one team’s internal product, not a standard.

Bars and checks:

- The agent can boot an isolated copy and query that copy’s logs, metrics, and traces (the post names LogQL and PromQL). The check fails when the journey produces no trace the verifier can query. A number written in the constitution while this is false is not a gate. The dry run’s section 19 already says this. Keep it, and do not attach the sample numbers to it.
- The sentences “ensure service startup completes in under 800ms” and “no span in these four critical user journeys exceeds two seconds” are prompts the authors say become tractable after that legibility exists. They are not reported results, not percentiles, and not transferable. Flag both numbers as anecdotes.
- Also anecdotes, not bars: on the order of a million lines, about 1,500 merged pull requests, 3.5 pull requests per engineer per day, single runs of upwards of six hours, and an estimate of one tenth the time of writing the code by hand. The post says the low-friction loop depends on this repository’s structure and should not be assumed to generalize yet.
- What does transfer as a mechanical check, already in the dry run: structured logging, validation at the boundary, and custom lint failures that tell the agent how to comply. Those are invariants. They are not the latency numbers.

### 9. GitHub secret push protection, and GitLab secret push protection

- Org: GitHub Docs and GitLab Docs
- Date: current product docs as of 27 October 2026. GitLab’s secret push protection is documented as generally available in GitLab 17.5.
- URLs: https://docs.github.com/en/code-security/concepts/secret-security/push-protection and https://docs.gitlab.com/user/application_security/secret_detection/secret_push_protection/
- Why primary: the forge’s own fail-closed rule for a secret in the diff. Better than a blog checklist. The product requirement behind it is ASVS `v5.0.0-13.3.1`. These pages are how the push fails.

Bars and checks:

- A push that matches a known secret pattern is blocked, and the error names the commit, the file, and the pattern type. The slice does not merge by deleting the scanner’s complaint from the log. A bypass is an explicit, audited exception (GitHub records the bypass reason and alerts; GitLab’s skip is an audit event, including `git push -o secret_push_protection.skip_all` and `[skip secret push protection]` in a commit message).
- This is not proof that no secret exists. Both products scan high-confidence patterns. GitLab states that personal access tokens with a custom prefix are not in the default push-protection set, that binaries are skipped, that a file or diff patch larger than 1 MiB is skipped, and that a push changing more than 3,150 paths or 350,000 lines skips protection. A scan timeout on GitLab accepts the push. Agent-sized diffs are exactly the pushes that fall through. Pair push protection with pipeline secret detection on the commit that landed.
- The check runs on every push, not only on a trust-boundary slice. A clean secret scan of a small slice does not clear a later huge push that the forge skipped.

### 10. SLSA 1.2

- Org: SLSA community
- Date: 24 November 2025 (v1.2 approved)
- URL: https://slsa.dev/spec/v1.2/ and the announcement https://slsa.dev/blog/2025/11/announce-slsa-v1.2
- Why primary: the consensus levels for build provenance. This is the change-safety check that the rehearsed artifact is the artifact you promote. It is not a test of authorization, latency, or privacy. v1.0 and v1.1 pages are marked retired. Cite v1.2.

Bars and checks:

- Build L1: provenance exists, identifies the artifact by cryptographic digest, and describes how it was built. The producer distributes it. L1 can be forged. It catches mistakes, such as promoting a commit that was not the one rehearsed.
- Build L2: a hosted build platform generates that provenance and signs it. Verification checks the signature. This is the M7 bar worth adopting: promote only a digest whose signed provenance names the commit the evidence bundle names, produced on the hosted builder. A developer workstation build does not satisfy L2.
- Build L3 hardens the platform so the signing key is unavailable to the build steps and runs cannot influence each other. That is a platform property, not a slice check.
- Do not treat “SLSA compliant” as a pass on the section 10 security set. A vulnerable allow decision can ship inside a perfectly provenanced artifact.

### 11. 4× Velocity, 10× Vulnerabilities: AI Coding Assistants Are Shipping More Risks

- Org: Apiiro. Itay Nussbaum.
- Date: 4 September 2025
- URL: https://apiiro.com/blog/4x-velocity-10x-vulnerabilities-ai-coding-assistants-are-shipping-more-risks/
- Why primary: this is Apiiro’s own post, which is the source behind the secondary citations. It is a vendor analysis of its customers, ending in a pitch for Apiiro’s product. It is not a peer-reviewed paper. Use it for the shape of the miss. Do not encode any of its multipliers.

What the post actually claims, all of it observational:

- Method, as stated: their Deep Code Analysis engine on code from tens of thousands of repositories and several thousand developers in Fortune 50 enterprises, across a variety of coding assistants. It does not publish a sampling frame, a confidence interval, or a precise rule for labeling a developer or a line as AI-assisted.
- AI-assisted developers produced 3–4× more commits, packaged into fewer and larger pull requests. Pull-request volume fell by nearly a third. By June 2025 the studied repositories showed more than 10,000 new security findings a month attributed to AI-generated code, described as a 10× rise since December 2024.
- Syntax errors in AI-written code dropped 76%. Logic bugs fell by more than 60%. Privilege-escalation paths rose 322%. Architectural design flaws rose 153%. Azure service principals and storage access keys were exposed nearly twice as often as by non-AI peers.
- The authorization-header story (“in one case, a single AI-driven PR…”) is a single anecdote inside the post.

Check this supports: keep slices small enough that a reviewer can see an allow decision; treat a green syntax or unit run as silent on architectural authorization; run the secret and dependency checks even when the slice looks like a refactor. It does not support a numeric finding-rate gate, and it does not support “security review on every trust-boundary slice” as the remedy. The post’s own point is that the miss sits in large, multi-file, design-level changes, which scanners under-detect.

### 12. DORA State of AI-assisted Software Development 2025, with the 2024 estimates beside it

- Org: DORA / Google Cloud. 2025 announcement by Nathen Harvey and Derek DeBellis.
- Date: 2025 report announced 23 September 2025. Survey window in the report text: 13 June through 21 July 2025. The 2024 Accelerate State of DevOps report is the source of the older percentages.
- URLs: https://dora.dev/research/2025/dora-report/ and https://cloud.google.com/blog/products/ai-machine-learning/announcing-the-2025-dora-report and the 2024 PDF https://dora.dev/research/2024/dora-report/2024-dora-accelerate-state-of-devops-report.pdf
- Why primary: the long-running industry survey on delivery throughput and stability, and the primary statement of the AI-adoption result the dry run alludes to in section 18. It measures organizations, not a code trace. It sets no latency number and no vulnerability density.

Bars and checks:

- 2025, from the Google Cloud announcement by the DORA lead: nearly 5,000 technology professionals. AI adoption has a positive relationship with software delivery throughput and with product performance, and it still has a negative relationship with software delivery stability. The announcement’s mechanism sentence: without strong automated testing, mature version control, and fast feedback, more change volume leads to instability. Teams in loosely coupled architectures with fast feedback see gains.
- The report text (v. 2025.2): 90% of respondents use AI at work, more than 80% believe it increased productivity, and 30% report little or no trust in AI-generated code. Instability still hurts product performance and burnout; the report says it found no evidence that higher throughput cancels that harm.
- 2024 PDF, figure on the impact of AI adoption: an estimated 1.5% reduction in delivery throughput and an estimated 7.2% reduction in delivery stability for every 25% increase in AI adoption. The report presents these as model estimates and hypothesizes larger change lists. The 2025 report reverses the throughput direction and keeps the stability finding. Do not cite 1.5% or 7.2% as a current effect, and do not put either number in a gate.
- Check this supports: a slice stays small; the core journey is an automated fail, not a quarterly sample; capacity and the full ASVS level run at M6 and promote, where the cost is paid once per candidate. A quality gate that makes every slice wait on a full load test or a full security replay buys the batch size DORA is warning about.

## Numbers that are not bars

| Figure | Where it appears | How to treat it |
| --- | --- | --- |
| Startup under 800 ms; no critical-journey span over 2 s | OpenAI, 11 Feb 2026 | Example prompts. Not a measured SLO, not a percentile. |
| 99% of Get RPCs under 100 ms; 90/99/99.9% at 1/10/100 ms; average 100 ms hiding a 5 s tail; “one second” as a policy error; queue at 50% of the pool; 60 retries per minute; 5 s RPC deadline | Google SRE book, 2016 | Worked examples of shape. The binary rules (proportion, window, reject when full, deadline, capped retry) are the bar. |
| 10 ms typical, 1 s at the 99th, 100-way fan-out, 63% of requests slow; one service’s 10 ms vs 140 ms at the 99th | Tail at Scale, 2013 | First set is a hypothetical. Second is one Google service. The bar is “SLO on the joined request, and a bound on fan-out.” |
| Within 100 ms feels fluid; Bigtable hedge from 1,800 ms to 74 ms | Tail at Scale, 2013 | UX citation and one benchmark. |
| 3–4× commits, about one third fewer PRs, 10× findings, 10,000 findings in June 2025, syntax −76%, logic bugs −60%, privilege escalation +322%, design flaws +153%, cloud keys nearly 2× | Apiiro, 4 Sep 2025 | Vendor telemetry on Fortune 50 estates. No interval, no published labeling rule. |
| −1.5% throughput and −7.2% stability per +25% AI adoption | DORA 2024 | Survey-model estimates. Throughput direction reversed in the 2025 report. |
| 90% use AI, more than 80% report a productivity gain, 30% little or no trust, about 5,000 respondents | DORA 2025 | Survey description. Not a code gate. |
| A01 average incidence 3.73% or 3.74%; “100% of the applications tested” | OWASP Top 10:2025 | Incidence is the usable column and is still not a slice probability. The 100% sentence conflicts with that column and matches the maximum coverage figure. |
| CVSS ≥ 9.0 | AISVS `AC.4.3` | Their definition of critical, or the organization’s equivalent. |
| API4 bill from about $13 to $8k | OWASP API4 scenario | Illustration of a missing spending limit. |
| On the order of 1,000,000 lines; about 1,500 pull requests; 3.5 PRs per engineer per day | OpenAI, 11 Feb 2026 | One team’s throughput anecdote. |

## Read and not ranked

- OWASP Top 10 for LLM Applications and the OWASP cheat sheet “Secure Coding with AI.” They are about the coding agent and the model: prompt injection, tool abuse, secrets in the prompt. Appendix C already points at them. They are not the pass/fail list for the application’s allow decision, page size, or tail latency. The cheat sheet’s do and don’t list is the less testable cousin of AISVS Appendix C.
- ISO/IEC 25010. It names product characteristics, including performance efficiency (time behaviour, resource use, capacity), reliability, and security. It does not give a fail condition a harness can run.
- Lab studies of autocomplete, including the user study titled “Do Users Write More Insecure Code with AI Assistants?” They argue that the person accepting a suggestion is not the assurance boundary. They were not re-read for this note, and none of their rates is used. They do not set a product bar.
- Vendor “percentage of AI code that fails a scan” reports other than the Apiiro post above. Not used. Another vendor percentage would not become a gate even if the primary were fetched.
- The Augment Code harness article. It cites Apiiro and DORA at second hand. The primaries are the entries above.
