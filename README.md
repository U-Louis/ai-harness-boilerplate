# ai-harness-boilerplate

A foundation for building an AI harness. An abstract approach, independent of any particular technology or domain specification, meant to be adapted to any real project.

## Abstract boilerplate

### A harness is an empty bag that cannot stand upright
The first assumption is that a harness cannot be effective unless it knows what it is supposed to harness: a domain, with its specifications and use cases; an implementation, with its technologies, constraints, and physical and financial limits; and, finally, a confrontation with real developers, real data, and ultimately real users. An empty bag cannot stand upright. Starting from that, what can we still find that remains relevant to any project? How can we determine the outlines of this bag? This folder is an attempt to bring together the state of the art on that question.

### The main variable is the human
The second assumption is that the paradigm has shifted. We moved from a human workflow into which we injected more or less AI, to an AI workflow into which we inject more or less of the human. So, first: how much of the human do we want to inject? And then: where do we want it? For this folder, we will pick a side. Let us treat AI-written code as a new step of abstraction, comparable to the move from assembly to higher-level programming languages (Uncle Bob's take). In that case, we developers — code writers? engineers? — are left with guiding the AI and making sure it does the job well. That is it. Well, actually, that is still a lot, right? The main variable is the human.

Choosing where to put the human is very important. For example: should our code lean toward low cyclomatic complexity, so an AI can read it easily, or toward low cognitive complexity, so a human can? Both are valid choices. If we decide there will be no more human review, the answer is low cyclomatic complexity. But since both will work, why not ask the AI to write code a human can read effortlessly, just in case? Old habits die hard. So, in this abstract boilerplate, we will leave room for these choices whenever we can. There are so many choices to make, even before stepping a single foot in the actual project.

### Harness is code
The third assumption is that LLM have come to a point where they are capable to do without a harness, or that it will do better without it. Ok, then this boilerplate would come more as a decision making process than actual guidelines for efficiency. Plus, maybe when not using the best models (why? money.), this boilerplate might still find itself relevant. Still, we have to be aware that some prompts will be counter effective, that therefore, they need, by design, maintenance. So let's treat the harness like any other organic code : it needs to be thought, debated, structured, reviewed, rewritten, etc. Harness is code.