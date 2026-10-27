# Evolvability of the harness

Source dossier. Research date: 27 October 2026. Read against [§19](../Abstract-boilerplate.md) of `Abstract-boilerplate.md`.

The question was how to keep the harness easy to change as models change, including two leads: a sentence remembered as Fabien Potencier’s, that a prompt from a few months ago is worth revisiting because newer models are smarter and a more detailed prompt can constrain them; and a person, remembered as possibly ex-Google, who said that for the best models the strategy is not to harness at all and to let the model figure out what to do.

Pages below were fetched in full unless a claim is marked as a snippet or a description. “Primary” means the person said it on a page this note read, or a named interviewer quotes them in a transcript. A schedule abstract and a company blog that quotes its own executive are labeled as such.

## 1. Ranked sources

Ranked by how directly they say what to delete from a harness when the model changes, then by what they measure. Dates are the dates printed on the pages. A docs page with no date is dated by the day it was read.

### 1. Boris Cherny, Y Combinator Startup School, 27 July 2026

- **Who:** Boris Cherny, creator of Claude Code, interviewed by Diana Hu.
- **Date:** 27 July 2026. The session is the day after Opus 5 shipped, as the host frames it.
- **URLs:** Transcript read here: <https://www.ycrootaccess.com/p/boris-cherny-building-claude-code>. YC library entry, which did not render the transcript: <https://www.ycombinator.com/library/UN-boris-cherny-we-cut-80-of-claude-code-s-prompt>. YouTube: <https://www.youtube.com/watch?v=qyPCVqFUyDo>. The 80% passage in the YouTube auto-transcript matches the Root Access transcript. The rest of this item is the Root Access transcript.
- **Why primary:** He runs the harness the sentence is about. This is the page that matches the remembered wording. It is not Potencier.

**What he says:**

- Claude Code’s harness is always adding and deleting. Every new model, they delete and change part of the system prompt, the tool set, and the tool prompts, because every model is different. Something done for one model about three months earlier may not transfer.
- For Opus 5 they deleted 80% of the system prompt. Those lines were correcting behavior the model should already have had. “Now Opus 5 just does it.”
- An undocumented simple mode strips the system prompts, including tool prompts. They use it as an ablation. He says the model is a little more intelligent without those prompts. A product still keeps some prompts, because they make the product and the model behave the way a person wants.
- Ablation, as he uses the research word: delete the entire system prompt, then bring it back line by line and see the effect of each line. Do the same for tools. Unship tools. Delete harness code. He says almost all of the harness code that remains is safety, permissions, and static analysis, plus UI. He says they do not delete the entire codebase.
- For someone using Claude Code, every six months: delete the instruction file, the skills, and the hooks, and see what the model does. The transcript renders the filename as “quantum D,” which is the recognizer’s `CLAUDE.md`. For Opus 5 he recommends trying that deletion, because instructions written for past models may be unnecessary.
- How to rebuild: delete, then use it. Do not guess which instruction the model needs. Add a line back only when the same stumble repeats, and not early. The model reads the line on every use.
- Evals outlive the harness a little, and not by much. An eval might last one to three model generations, then it saturates and is replaced. He would not treat evals as the permanent layer.
- Overly specific step order is the wrong shape for current models. “I want you to do it in this way… You must do one, then two, then three, then four.” He says to state the task, the guardrails, and the exit criteria, and let the model proceed. He says that would not have worked six months earlier.
- The skill he names is no longer prompt wording. It is giving a task that is a little too hard, and giving the model a way to verify its work. Verification is “probably the single most important thing that people do not get right.”
- “You don’t even need to use scaffolding” is about a long run of Opus 5 with Auto Mode: no `/goal`, and later no `/go` and no `/loop`, which he says still help. The two-week Electron-to-Swift rewrite is his example. The prompt still contained the verification method: run one app, screenshot, compare pixel by pixel, do not stop until done. The Bun rewrite from Zig to Rust ran 11 days on a test suite. He calls both steering, not the absence of a check.
- A common failure he names in engineers of long experience is overspecifying the path so the model does the task the way the engineer would have done it.

Companion primary, earlier and quoted directly, 23 September 2025: Gergely Orosz, “How Claude Code is built,” <https://newsletter.pragmaticengineer.com/p/how-claude-code-is-built>. Cherny: they want people to feel the model as raw as possible; features meant to help end up hobbling the model; every new model release they delete a bunch of code; with the 4.0 models they deleted around half the system prompt; they minimize prompting and the number of tools, and they delete tools. The same interview says the most complex remaining part is the permissions system, and that a session should not change the machine without permission unless the person has opted out. The half-prompt figure is this interview. The 80% figure is the July 2026 talk. They are different model generations.

### 2. OpenAI, “Rethinking skills and prompts for GPT-6 Astra”

- **URL:** <https://developers.openai.com/blog/rethinking-skills-and-prompts-for-gpt-6-astra>
- **Date:** None printed on the page. Read 27 October 2026.
- **Why primary:** OpenAI’s own migration note for agent instructions. This is the other page whose wording matches the remembered sentence (“best practices are changing,” “worth revisiting”).

**Claims:**

- Best practices are changing fast. What used to require handholding and scaffolding no longer does. Instructions accumulated over the last year, in skills, `AGENTS.md`, and task prompts, are worth revisiting at each release, and more so for GPT-6 Astra.
- Skill descriptions that are long, contradictory, or eager cause the model to load instructions that do not help. A skill with several workflows should be a short router into other files, not an itinerary. “Models have gotten much better at understanding nuance and ambiguity, so overly specific guidance can now hinder results where it previously helped.”
- Repository skills are read by other people’s agents, which may be different models. Guidance that helps a smaller model can overconstrain Astra.
- Revisit each `AGENTS.md` line. Requiring a stack of docs before every edit is excessive for a typo fix. Astra can decide what to read. Pointing at a doc still helps when the pointer says when the doc applies. An instruction to run tests, written because older models would not, makes Astra test more than the task needs. A hard boundary written so an older model would ask first can make Astra, which the page calls their most aligned model, stop where the person would have wanted it to continue.
- Define completion before starting, if the model stops at a first implementation. A requirement to stop for review after the first pass pulls it toward an earlier stop.

Companion, same organization, GPT-5.5 prompting guide, <https://developers.openai.com/api/docs/guides/latest-model?model=gpt-5.5>, no date printed, read 27 October 2026. Treat the new model as a new family. Begin from a fresh baseline. Start with the smallest prompt that preserves the product contract. State the outcome and the success criteria. Reduce or remove step-by-step process guidance unless the product requires that path. “Legacy prompts often over-specify the process because earlier models needed more help staying on track. With GPT-5.5, that can add noise, narrow the model's search space, or lead to overly mechanical answers.”

### 3. Anthropic, “Skill authoring best practices”

- **URL:** <https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices>
- **Date:** None printed. Read 27 October 2026. Docs pages move.
- **Why primary:** Anthropic’s own writing rule for skills, which is the same claim at the grain of one file.

**Claims:**

- The default assumption is that Claude is already very smart. Add only context Claude does not already have. Each paragraph has to justify its tokens, because once the skill is loaded it competes with the rest of the window.
- Freedom matches fragility. An open task (their example is a code review) gets a direction. A fragile command (their example is a database migration that must run as one script, with no extra flags) stays exact.
- Test the skill on every model that will run it. Ask whether Haiku has enough guidance, and whether Opus is being over-explained. What works for Opus can be too thin for Haiku. A skill used across models should be written so each of them can run it.

### 4. Cat Wu, Ars Technica, 15 May 2026

- **Who:** Cat Wu, head of product for Claude Code, interviewed by Samuel Axon. The piece says the interview was edited for length and clarity.
- **URL:** <https://arstechnica.com/ai/2026/05/claude-codes-product-lead-talks-usage-limits-transparency-and-the-lean-harness/>
- **Why primary:** The product lead, on why the harness stays thin. She is not the ex-Google lead.

**Claims:**

- They remove scaffolds. They remove parts of the system prompt and of tool descriptions as models get smarter.
- She can see a later collapse back to a text box, if the model is “just always right,” and she says that for now the tools are still needed to meet people where they are.
- They have no grand plan for the form factor. She names Sutton’s Bitter Lesson as a guiding principle: do not bake in a structure the next model will make obsolete. This note did not re-read Sutton’s essay. The claim used is that she uses it that way.
- Semantic code navigation, in their evals, did not show a measurable change. They ship a leaner harness with fewer opinionated tools, and let a developer add a tool. The core harness she names is a minimal set: plan, to-do, edit, a few others, and clarifying questions.
- Opus 4.5, on her account, is when the tool started to work on legacy codebases for people who had not learned how to coax it. That is a capability claim about one model generation, not a measurement in this note.

### 5. Databricks, “Benchmarking Coding Agents on Databricks’ Multi-Million Line Codebase,” 8 July 2026

- **Who:** Vinay Gaba, Ankit Mathur, Rishabh Singh, Patrick Wendell, Matei Zaharia.
- **URL:** <https://www.databricks.com/blog/benchmarking-coding-agents-databricks-multi-million-line-codebase>
- **Why primary:** A named team, on their own large codebase, comparing harnesses at fixed model and effort. This is the closest published “big repo” measurement. It is not a measurement of a multi-session product build.

**Setup, as the post states it.** Tasks are recent human-written pull requests, confined to a few modules, with a test suite. Bot, service-account, fully AI-generated, and auto-generated changes were filtered out. The prompt states the outcome and drops the solution. Tests are held out and patched in after the agent says it is done. No LLM judge. Git history was sealed after agents were found walking forward to the merged commit. The repo spans many languages, including Scala, Go, Rust, Java, Python. About a quarter of captured internal tasks were tagged low complexity and about 60% medium. The post says the sample is not comprehensive.

**What they report.** Conclusion 4 says the harness “dramatically impacts cost and quality,” and that simple harnesses such as Pi performed best on their workloads. The paired paragraph says the same model at the same thinking effort, through Claude Code or Codex versus Pi, had a cost difference of more than 2× in some cases, “while quality remained the same.” Pi sent about 3× less context per turn. Zaharia’s own shorter account of the same study (<https://www.linkedin.com/posts/mateizaharia_we-benchmarked-coding-agents-on-our-own-internal-activity-7480710350720602112-bCcQ>) says Pi got the same success rate as the vendor harnesses with Opus and GPT 5.5, at 2× less cost, mainly from smaller inputs. The author’s short account matches the paired paragraph. The summary line is stronger than that paragraph. This note uses the paired paragraph for the quality claim and reports the summary line as a conflict inside the post.

**What it does not measure.** The tasks are self-contained pull requests. They are not a constitution, a security boundary, a migration, or a handoff across sessions. Public benchmarks are set aside in the post because solutions leak into training and because the results were not representative of this repo.

### 6. Pan, Yang, Arabzadeh, Chiang, Stoica, and Zaharia, HarnessTax, 16 September 2026

- **URL:** <https://arena.ai/blog/coding-agents-harness-tax>. Citation page: <https://harnesstax.github.io/>. Updated 18 September 2026.
- **Why primary:** A controlled comparison of 21 model–harness pairs. The limit is in the post.

**Setup.** Seven models, three harnesses (Claude Code, Codex CLI, Pi), 30 randomly sampled tasks from SWE-bench Lite and 30 from Terminal-Bench 2.0, three runs each, high effort, 100-turn cap. SWE-bench Lite runs block the network and disable web tools. Costs use one price list dated 1 September 2026. Success uses each benchmark’s evaluator.

**What they report.** Harness choice has little effect on success and a large effect on cost. Average harness effect on success stays within ±2% on SWE-bench Lite and within about ±5% on Terminal-Bench 2.0. The opening summary says the same model can reach similar success at up to 5× the cost. The body gives geometric means: Claude Code costs about 2.0× Pi and 1.6× Codex on SWE-bench Lite, and 1.5× Pi on Terminal-Bench 2.0. Claude Fable 5 on SWE-bench Lite: 97.8% of attempts in Claude Code, 96.7% in Codex, 96.7% in Pi, at about $1.33 versus $0.67. Pi’s tools are read, write, edit, and bash. Claude Code’s mean initial context is over 10× Pi’s. Across six Anthropic and OpenAI models and both benchmarks, an alternative harness had the highest observed success in nine of twelve comparisons. The post says the benchmarks may have been seen in training, and that results may differ on other workloads.

**Their own limit.** “The natural next step is to evaluate harnesses and automate their selection in real development workflows, where requirements evolve, developers provide feedback, and tasks extend across sessions.” They point that sentence at Young’s long-running-agent post. For day-to-day tasks they describe the coding agent as an interface. For problems at the boundary of what the model can do, they say a harness that structures exploration and feedback may still help.

### 7. Logan Kilpatrick, Training Data (Sequoia), episode dated 11 June 2026

- **Who:** Logan Kilpatrick, who leads Google AI Studio and the Gemini API. He was at OpenAI before Google. He is not a former Googler.
- **URL:** <https://sequoiacap.com/podcast/google-deepminds-logan-kilpatrick-why-the-model-eats-the-harness>. The transcript page, read 27 October 2026, did not print a date. The episode listing dates it 11 June 2026.
- **Why primary:** This is the “model eats the harness” conversation. It is the closest public match to the remembered “let the model do it” person, and the affiliation in the memory is reversed.

**What he says:**

- What people call the model is no longer only the weights. Tool calling, hosted tools, search, code execution, and a container with a harness have been absorbed into the thing still called Gemini, GPT, or Claude.
- Scaffolding runs ahead of what is baked into the weights. Then the model eats that scaffolding.
- External scaffolding still has value in some cases. His examples are a choice of search provider, and code execution.
- The agent harness is his example of where people currently think the advantage is. He thinks that will not stay true, “at least in the way that we think of the harness today.” In 12 months, on his estimate from that interview, models will have digested a bunch of it, and the advantage will not be in spinning up your own harness, because the model does it natively.
- A model that cannot use someone else’s harness is not a general model. He asks for something like a Harness Bench: how well each model adapts to each harness.
- In the same interview he describes Google’s products rebasing onto one agent harness, Antigravity. The episode description says that harness came out of the Windsurf team. He is arguing against a proprietary harness as a moat. He is not describing a Google product with no harness.

The 12 months had not elapsed on 27 October 2026. This note does not score the prediction.

### 8. Fabien Potencier, pages that were fetched

The remembered sentence was not on these pages. They are what he does say in public, and they pull in a different direction from “no harness.”

- SymfonyLive Paris 2026, schedule abstract, talk in French, <https://live.symfony.com/2026-paris/schedule/building-a-coding-agent-in-php-inside-the-harness>. The abstract says the model is only half of the solution, and that the harness — context, tool orchestration, prompt construction, and feedback loops — is what determines the quality of the result. This is the abstract, not a transcript of the talk. The recording was not fetched.
- Upsun, “The AI trust dial,” <https://upsun.com/blog/trust-dial-ai-agents-autonomy/>, no date printed, read 27 October 2026. The article quotes him. Trust is a dial. Gates come off after the work has been done and the runs are consistently good. The retrospective after each run tweaks prompts and skills, and anything that can be made deterministic moves into scripts. Once it is encoded, you are trusting the process, not asking the agent to figure that step out again. He calls unsupervised default “yolo mode,” and he calls the person who approves every step a human checklist. The article is Upsun writing, with quotation marks around his sentences. It is not a post signed by him.
- LinkedIn, “A coding agent has to earn your trust,” <https://www.linkedin.com/posts/fabienpotencier_a-coding-agent-has-to-earn-your-trust-a-activity-7480946358187765761-C2yY>. His Symfony release skill replaced a confirmation rule with an autonomy rule: he validates the plan, the agent runs, a second model watches for anything unusual. Each run is a chance to sharpen the prompt and tighten the skill. No date printed on the fetch.
- LinkedIn, “Reviewing agent code line by line is a trap,” <https://www.linkedin.com/posts/fabienpotencier_reviewing-agent-code-line-by-line-is-a-trap-activity-7490006061089902592-I2vU>. His review questions move up to outcome, tests, abstractions, and “Will I be able to evolve this next year?” Comments on the fetch are dated August 2026. The post date was not printed.
- DevelopersTV, “LLMs as constrained generators,” 14 November 2025, <https://developerstv.com/video/qvzdsgrbvp2ungc>. Description only. The video was not transcribed. The title is about bounding generation so the output is deterministic. That is a different claim from “a detailed prompt constrains a smarter model,” and it is the earlier of the two. A later autonomy post does not retract output checks. It moves confirmation off the steps that have become scripts.

### 9. mini-SWE-agent docs, read 27 October 2026

- **Who:** The Princeton and Stanford group behind SWE-bench and SWE-agent. John Yang and Ofir Press are the names on the project.
- **URL:** <https://mini-swe-agent.com/latest/>
- **Why it is here:** The same group that, in 2024, argued for a custom agent-computer interface (SWE-agent, arXiv:2405.15793) later wrote that a year on, much of that interface is not needed to build a useful agent. The docs describe a bash-only loop, a linear history, and a claim of above 74% on SWE-bench Verified. That percentage is a live docs number. It is not installed as a stable result. The historical claim used in the digest is the authors’ own retraction of the need for the special interface, not the leaderboard figure.

SWE-bench’s bash-only leaderboard uses this agent so models can be compared without a special scaffold. <https://www.swebench.com/verified.html>, read 27 October 2026. It is a comparison instrument. It is not a product harness.

## 2. Conflicts

**Delete the prompt, or the harness determines the quality.** Cherny, Wu, and the OpenAI guides say a detailed prompt written for an older model now gets in the way, and they delete it. Potencier’s SymfonyLive abstract says the harness determines the quality. Both can be true if “harness” is split. Cherny says the code that remains is safety, permissions, and static analysis, and that verification is the part people miss. Potencier’s trust-dial quotations say a gate comes off when the step has become a script, which is a deletion of supervision, not a deletion of the check. The digest splits a line into a compensation, a fragile command, and an invariant. A judgement in the reviewer file stays with the invariant. That split is the digest’s. No page in this set measures it on a product constitution.

**“No harness” is three different sentences.** Kilpatrick: within about 12 months the advantage will not be in a proprietary generic loop, because the model absorbs it; search and code execution stay outside; Google ships Antigravity. Cherny: you do not need `/goal` for a long Opus 5 run; you do need a way to verify; the model is a little more intelligent with the prompts stripped, and the product still wants some of them. Wu: a text box is imaginable later, and the tools are still needed. None of the three says a multi-session product has no outer policy.

**A thin harness matches on pass rate.** Databricks’ paired paragraph and HarnessTax agree, on issue-shaped tasks, that a four-tool harness keeps up with Claude Code and Codex on success and spends less. Databricks’ summary line also says quality changes. HarnessTax’s own last section says the result may not survive contact with evolving requirements and cross-session work. OpenAI’s February 2026 harness post, already in `01-harness-definition.md`, says their million-line run depended on repository structure and tooling that took months, and should not be assumed to generalize yet. Young’s November 2025 post, already in that dossier, is the harness for many fresh windows. Those two are why the digest does not treat the issue-task result as a license to drop the constitution.

**One text for every model.** The skill guide says a line that is right for Opus can be too thin for Haiku, and that a shared skill should work for each model that loads it. The Astra guide says a line that helps a smaller model can overconstrain Astra. The digest’s constitution names one instruction-model. A second model is a second test, not a second copy of the old prompt pasted in.

**Evals are not the constitution.** Cherny says a capability eval saturates in one to three model generations and is then thrown away. The gates in the digest are spec checks and invariant checks. They do not saturate because a coding benchmark saturated. Keeping them across model changes is the digest’s distinction, not his sentence.

**Gloaguen is already measured.** `04-artifacts.md` records that extra always-loaded requirements lowered resolution and raised cost on issue tasks, and that a human-written file should state only what the agent cannot infer. That is the same direction as Cherny’s deletion, on a different object (the context file, not the vendor system prompt). It is not a new result here.

## 3. Checked and not used as a rule

- Imran Khan, “You Don’t Need Prompt Engineering Anymore: The Prompting Inversion,” arXiv:2510.22251, HTML at <https://arxiv.org/html/2510.22251v1>, which prints 24 August 2026. On GSM8K, his constrained “Sculpting” prompt beat standard chain-of-thought on a 100-problem gpt-4o sample (97% against 93%) and lost on the full gpt-5 set (94.00% against 96.36% for chain-of-thought). The figure caption says the gpt-5 bars shown are also a 100-problem subset, with the full set in a later table. One author, one prompt family, grade-school math, not a repository. The direction matches Cherny and OpenAI. The percentages are not used.
- Richard Sutton, “The Bitter Lesson,” 13 March 2019. Not re-read. Wu cites it. The digest cites Wu.
- <https://tmustier.github.io/harness-benchmarks/>, captured 10 September 2026 on that page. A compilation of public comparisons. It reports spreads, including 12.5 to 27.4 points on one benchmark, and says the count is not 27 independent replications. The underlying papers were not re-read for this note. The spreads are not claims in the digest. HarnessTax and the Databricks post were read themselves.
- <https://agentnativeengineering.com/field-notes/2026-06-16-model-is-absorbing-the-harness/>. A retelling of Kilpatrick. Not used. The Sequoia transcript is the source.
- <https://pejmanjohn.com/agent-debt-don-t-trap-tomorrow-s-model-in-today-s-prompt>. A practitioner essay that splits a feature contract from a model-specific prompt. The same split is already in the OpenAI GPT-5.5 guide (“the product contract” versus the old prompt stack). The essay is not an additional source.
- Context Jamming, “FounderFiles” on Potencier, <https://www.contextjamming.com/founder-files/FabienPotencier>. Secondary. Not used for his words.
- Steve Yegge, “The Shape of Things to Come,” <https://yegge.ai/essays/the-shape-of-things-to-come/>. Ex-Google, and he builds heavy bespoke harnesses. He is the opposite of the remembered “no harness” sentence. Not used.
