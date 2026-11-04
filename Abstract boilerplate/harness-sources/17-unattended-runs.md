# Unattended runs on a forge

Source dossier. Research date: 3 November 2026. Read against [§21](../Abstract-boilerplate.md) of `Abstract-boilerplate.md`. That section was written from this note.

`14-matt-pocock.md` stopped on 27 October 2026 and did not read `skills/in-progress`. The factory skill below is later. Where a skill file and a course page disagree, the skill file wins. Where a vendor page and a retelling disagree, the vendor page fetched on this date wins.

“Primary” means the author or the vendor wrote it. A comparison table of cloud agents is not a primary.

## What the pages are describing

An unattended run starts without a person opening a session. Something already on the forge, or a clock, or a chat message, starts it. The agent works on a machine that does not keep the chat. It leaves a branch, a pull request, or a merge request. A human approval stands in front of the default branch on every page except StrongDM.

Pocock calls the whole arrangement a software factory. GitHub says cloud agent, agentic workflow, and automation. GitLab says flow and trigger. Anthropic says interactive mode and automation mode. Stripe says minion and blueprint. Factory.ai uses Pocock’s two words as a product name. This note keeps each page’s word in the claim and uses “unattended run” for the rule.

## Ranked sources

Ranked by how directly the page says who may start a run, who may write, and who may merge.

### 1. GitHub, risks and mitigations for Copilot cloud agent

- **Date:** Fetched 3 November 2026. The kickoff page was fetched the same day.
- **URL:** <https://docs.github.com/en/copilot/concepts/security-governance-and-network-settings/risks-and-mitigations>
- **Also:** <https://docs.github.com/en/copilot/how-tos/copilot-on-github/use-copilot-agents/kick-off-a-task>
- **Why primary:** The forge states the controls in the product.

**Claims**

- Assigning an issue always creates a pull request. Copilot receives the title, the body, and the comments that exist at assignment. Later comments on the issue are not passed in. Follow-ups go on the pull request.
- Only a user with write access can start the agent. Comments from anyone else are not shown to it.
- The agent pushes to one branch: the pull request’s branch when mentioned there, otherwise a new `copilot/` branch. It cannot run `git push`. It uses a limited push.
- The pull request stays a draft. Copilot cannot mark it ready, cannot approve it, and cannot merge it. The person who asked for it cannot approve it.
- A pull request opened under Copilot’s own app identity, on a repository that already requires one approval, requires one further approval. A shared automation is attributed to Copilot and gets that extra approval. A personal automation is attributed to the user who created it, and that user still cannot approve it.
- Commits are authored by Copilot. The person who assigned the issue is the co-author. Commits are signed. The message links to the session log.
- By default, Actions workflows do not run on the agent’s pull request until a user with write access clicks Approve and run workflows. The same default holds when one automation opens an issue or a pull request that would start another. A setting can turn the default off.
- Hidden characters are stripped before user text reaches the agent. An HTML comment is the example they give.
- An automation may run on a schedule or an event with no person starting that run. The author chooses the tools. Write access for a GitHub event can be limited to the triggering issue or pull request. Events from users without write access are ignored unless someone opts in.
- Before the pull request is finished, the agent runs CodeQL, checks new dependencies against the advisory database, and runs secret scanning. It also requests a Copilot code-review pass and tries to fix what that pass finds. Each of those can be disabled.

### 2. GitHub Agentic Workflows

- **Date:** Fetched 3 November 2026. GitHub Next’s Continuous AI page names the category. The product page says the demonstrator is generally available.
- **URL:** <https://docs.github.com/en/copilot/concepts/agent-capabilities/agent-powered-workflows-and-operations/about-github-agentic-workflows>
- **Also:** <https://github.github.com/gh-aw/reference/safe-outputs/>, <https://github.github.com/gh-aw/introduction/architecture/>, <https://githubnext.com/projects/continuous-ai/>
- **Why primary:** This is the other GitHub design. The agent does not hold the write token.

**Claims**

- A workflow is Markdown in `.github/workflows/`. Frontmatter sets the trigger, the permissions, the tools, and the safe outputs. The body is the instruction. `gh aw compile` writes a lockfile that is an ordinary Actions workflow. The engine can be Copilot CLI, Claude Code, Codex, Gemini, or Pi.
- The agent job is read-only unless the author grants more. A write is a safe output: the agent emits a structured request, and a later job with a scoped token applies it. Secrets for that write stay in the later job.
- Threat detection can sit between the agent and the write. A network allowlist can block other outbound traffic. An environment protection rule can require a person to approve the write job.
- The author can grant `contents: write` or add a custom job. Those paths leave the safe-output list. The default is the split.
- Continuous AI, on the Next page, is the category name. The examples are triage, documentation, test improvement, a daily report, and a simplification pull request. They say it sits beside CI/CD and does not replace the build.

### 3. GitLab Duo Agent Platform

- **Date:** Docs fetched 3 November 2026. The pages print GitLab versions. Composite identity is generally available in 18.8 and has no off switch from 18.9. Triggers begin at 18.3 for a mention and run through 19.6 for work-item type filters.
- **URL:** <https://docs.gitlab.com/user/duo_agent_platform/triggers/>
- **Also:** <https://docs.gitlab.com/user/duo_agent_platform/composite_identity/>, <https://docs.gitlab.com/user/duo_agent_platform/agents/external/>, <https://docs.gitlab.com/ci/pipelines/fix_pipeline/>
- **Why primary:** The same arrangement on the other large forge, with the clock split out.

**Claims**

- An event trigger is a mention, an assignment, a reviewer assignment, a pipeline state, a merge-request action, or a work-item action. A scheduled trigger is a clock. No person starts that run.
- An event trigger runs as the service account combined with the human who caused the event. Permissions are the intersection. A scheduled trigger runs as a dedicated service account, with no human in the token. That account’s role is the only limit.
- A merge request a flow creates is attributed to the human who triggered it, although the service account created the commits. The stated reason is segregation of duties under SOC 2, SOX, ISO 27001, and FedRAMP. Prompting counts as authorship.
- An external agent is reached by mentioning, assigning, or requesting a review from the service account. It runs on a CI runner. The documented branch prefix is `duo/fix`, `duo/feature`, `duo/refactor`, or `duo/docs`. `.gitlab/duo/agent-config.yml` is read from the default branch only. It can set a network allowlist and a cache.
- Fix CI/CD Pipeline reads the failed log, the merge-request diff, and the repository. It may apply an inline suggestion, open a new merge request when the fix sits outside the diff, or post a comment and stop. It stops without a patch when the context is thin or the failure is security-sensitive.
- Assigning `@GitLabDuo` as reviewer starts Code Review Flow. A project setting can review every new merge request. The fetched blog says it comments. It does not say it merges.

### 4. Claude Code GitHub Action

- **Date:** Fetched 3 November 2026.
- **URL:** <https://code.claude.com/docs/en/github-actions>
- **Why primary:** This is the workflow file a team copies when Actions is the orchestrator.

**Claims**

- Interactive mode has no `prompt` input. Claude starts when a comment, a review, or a new issue contains `@claude`. Progress is a comment.
- Automation mode has a `prompt` input and runs on the event without a mention. Results go to the workflow log unless the prompt says to post.
- On an issue or a pull request, the triggering user must have write access, unless `allowed_non_write_users` names them. A bot actor is rejected unless listed in `allowed_bots`. The stated reason is to stop a bot from waking Claude. A `schedule` event skips the write check. GitHub still attributes the schedule to a user, and the bot check still applies to that user.
- A plain-text scheduled prompt has no shell and no GitHub API until the workflow grants tools.
- Opening a pull request is not the default. The action’s issue thread says Claude pushes a branch and returns a pre-filled link, so branch protection still applies. Opening the pull request requires an allowed tool and an instruction to use it.

### 5. Stripe Minions, parts 1 and 2

- **Date:** Fetched 3 November 2026. The pages do not print a publication day in the fetched text. Part 2 says the weekly count of minion-produced, human-reviewed pull requests with no human-written code had moved from over 1,000 to over 1,300.
- **URL:** <https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents>
- **Also:** <https://stripe.dev/blog/minions-stripes-one-shot-end-to-end-coding-agents-part-2>
- **Why primary:** A team describing the loop they run, including the cap. Slack starts it. The forge receives the branch.

**Claims**

- A run starts in Slack. The minion pushes a branch, CI runs, and a pull request is prepared from Stripe’s template. The engineer opens the pull request and asks another engineer to review it. The engineer can send the minion back onto the same branch.
- The machine is a devbox, the same EC2 image a human uses, ready in about ten seconds, isolated from production and from the internet. One box per task. Full permissions are allowed because a mistake stays on that box. There is no confirmation prompt.
- A blueprint is a state machine in code. Some nodes are agent loops. Lint and push are ordinary code and never call a model.
- Rule files are per directory, attached as the agent walks the tree. A global file is used sparingly because it would fill the window.
- Tools come from an internal server of about 500. A minion receives a small subset. The devbox is in QA, with no production data and no arbitrary egress.
- Linters run before the push. CI runs once. If a failure has no autofix, the failure goes back for one more local attempt. After the second push and the second CI run, the branch returns to the human. Further rounds, they say, cost tokens and time for a shrinking return.

### 6. Matt Pocock, factory skill and dictionary

- **Date:** Dictionary entries added 24 September 2026 (`813001a`). Skill pull request #1316 merged 31 October 2026 (`4ae2317`). Files fetched 3 November 2026. The course page was fetched 2 November 2026. Lessons were not public.
- **URL:** <https://github.com/mattpocock/skills/blob/main/skills/in-progress/software-factory/SKILL.md>
- **Also:** `EXAMPLE-TASKS.md` in the same directory. Dictionary: <https://github.com/mattpocock/dictionary-of-ai-coding>. Course: <https://www.aihero.dev/cohorts/software-factories-for-real-engineers-xdy1m>. Talk write-up: <https://ai.engineer/talks/LlgiOCmFG_w-fixing-pr-bottleneck>
- **Why primary:** His definitions, and the example tasks. The skill is beta, user-invoked, and excluded from the stable plugin. The course page is a syllabus. The lesson text was not read.

**Claims**

- A software factory is a system of agents that turns tickets, pull requests, and schedules into merged code. A run never waits for a human. It ends. A later human edit starts a new run. The runner is disposable. Memory of earlier runs is the ticket tracker, the git host, and the orchestrator.
- Every trigger is an accelerator. The three brakes are automated checks, a separate review run that reads the diff cold, and a human reading the pull request. Add one trigger at a time, throttled, with a human on every pull request. Cap a loop at a few tries. He publishes no count.
- Example tasks include explore, which may label an easy ticket ready and start an implementer, and decompose, which splits a spec and starts an implementer without showing the breakdown to a person. Rework fixes only what review named. A fix of the factory itself always gets a human. Retro, on one merged pull request in ten, proposes edits to the factory’s instructions.
- The dictionary: taking the human out of starting sessions does not, by itself, take the human out of anything else. A dark factory is a codebase nobody reads. Reading the agent’s summary does not count as review. If a human still reviews the pull requests, the arrangement is not a dark factory.
- The course page says brakes come before accelerators, names mutation testing, ubiquitous language, and deep modules, and says loops plus a dark factory will not be trusted. Those sentences are the syllabus. They are not a procedure.

### 7. StrongDM, as quoted by Simon Willison

- **Date:** Willison, 7 February 2026. He visited the team in October 2025. The team dates itself to July 2025.
- **URL:** <https://simonwillison.net/2026/Feb/7/software-factory/>
- **Also:** <https://factory.strongdm.ai/>
- **Why primary:** The other public use of “software factory,” and the only page that removes the human read and says what replaces it.

**Claims**

- Code must not be written by humans. Code must not be reviewed by humans.
- A scenario is an end-to-end story, often stored outside the codebase, on the model of a holdout set. The agent that writes the product does not see it. Satisfaction is the fraction of observed runs that a judge model scores as having satisfied the user. A green suite is not that verdict.
- The Digital Twin Universe is a set of behavioral clones of the vendors the product calls, so the scenarios can run without those vendors.
- The harness repository they released contains Markdown specs and an instruction to feed them to a coding agent. The product code is not in that repository.
- They print a token spend of a thousand dollars a day per engineer. Willison does not audit it. This note does not either. The satisfaction figure is likewise unused.

### 8. Factory.ai, and one measurement of app approvals

- **Date:** Factory docs fetched 3 November 2026. Vitruvian Software, 9 October 2026.
- **URL:** <https://docs.factory.ai/software-factory/automations>
- **Also:** <https://docs.factory.ai/software-factory/code-review-ci>, <https://vitruviansoftware.dev/blog/2026/10/09/every-ai-agent-gets-its-own-github-identity/>
- **Why last:** The product describes triggers and a review that can approve. The measurement shows that a GitHub App’s approval can satisfy a required review. Copilot’s own ban does not extend to every app.

**Claims**

- A Factory automation has a trigger, an instruction, a place the agent runs, and a visibility setting. Triggers they list: a schedule, Slack, a GitHub event, a webhook.
- Droid review skips drafts, posts inline comments, and submits an approval when it finds no issues.
- On one repository with one required approval, an installation token posting `APPROVE` moved the review decision to approved. `CODEOWNERS` does not accept an app. This is one repository’s measurement.

## What §21 took

- Three starts: a person assigns or mentions, a clock, a failed check. Each clock and each failed-check start keeps a human on the merge, and a secret-bearing workflow waits.
- The model job does not hold the token that writes issues, reviews, or the default branch. A later job writes. Copilot’s push to its own branch, and Stripe’s push, are the published exception. §21 records that exception. It does not make it the default.
- On a branch opened from an issue, a comment, a clock, or another run, workflows that hold secrets do not run until a person who did not write the triggering text allows them. The further rule that a fetched report URL is data, and that private and metadata hosts are refused, is Spec Kit’s bug assess command, retrieved 4 November 2026. It is recorded in `05-outer-workflow.md` source 1a. It is not a claim of the forge pages in this note.
- An agent approval counts only if the ruleset lets it. The ruleset in §14 excludes the agent’s identity. The Vitruvian measurement is why that sentence stays a ruleset and not a property of the forge.
- Stripe’s second CI run is the published count beside this document’s two send-backs. The cap does not move.
- Pocock’s five questions — what starts the run, what it reads and writes, what it starts next, where a human re-enters, what stops two runs — are the checklist for a start this document did not already name. His banned-word list is not.

## Conflicts

| Question | What the pages publish | What §21 kept |
| --- | --- | --- |
| Who may label work ready? | Pocock’s explore task may, and then starts an implementer. Copilot, GitLab, Claude’s action, and Stripe start from a person, a clock, or a failed check. | A second agent’s label is not a start. G2 stays. |
| Who accepts a split of a large spec? | Pocock’s decompose task publishes the tickets and starts the implementer. His stable `to-tickets` skill, already in the digest, shows the breakdown to a person first. | The stable skill. |
| May the reviewer write? | The factory skill: review reports, and rework is a separate run. The talk: the reviewer commits the fixes it is sure of. | Findings, then a send-back. The talk’s committing reviewer is a second writer. |
| Does an approval mean the diff was read? | Copilot, GitLab, and Stripe require a human approval. None define it as reading the diff. Pocock: the summary does not count, and a codebase nobody reads is a dark factory. | G4 stays a posture. The button is the forge’s. The reading is the constitution’s. Metrics still does not read the diff, and that still fails Pocock’s test. The conflict stays written. It does not ban Metrics. |
| Who holds the write token? | Agentic Workflows: the agent job is read-only. Copilot and Stripe: the agent pushes a branch. Pocock’s implementer opens a draft pull request. | Read-only is the default. Pushing the branch is a recorded exception. |
| Where does the proof live if nobody reads? | StrongDM: scenarios outside the repository, which the writing agent does not see. | Not installed. Frozen examples in the spec remain the oracle. The implementer can see them. ImpossibleBench is why that is weaker than a holdout. |
| Who edits the instruction file? | Pocock’s retro, one merge in ten, opens a pull request against the factory’s instructions. | A person. Instruction changes stay in the pipeline risk class. |

## Rejected

- The course lessons, the price, and the office-hour schedule. The lessons were not public on 2 November 2026.
- “Human review when your priorities call for it,” as a replacement for G4.
- One-way and two-way doors as a replacement for the risk list. The talk uses them to allocate attention. The October note already refused them as the list. The risk list stays.
- A repository-wide mutation score. The course names the practice. §11 already binds a survivor on the slice’s decision to the frozen example.
- Explore labeling a ticket ready. Decompose shipping a plan the person has not accepted. A reviewer that commits. Retro editing the constitution on a timer. Self-merge after the brakes feel trustworthy.
- StrongDM’s token line and satisfaction figure.
- Teardown Atlas, and comparison posts that line up Codex, Cursor, Jules, and Devin. The vendor pages above already say how a task becomes a branch.
- The word factory as a required term.
