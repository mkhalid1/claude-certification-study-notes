<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Exam overview: Associate, Foundations](README.md) · Next: [Domain 2: Output Evaluation and Quality Control](02-output-evaluation-and-quality-control.md)
<!-- /nav:top -->

# Domain 1: Task Definition and Prompting

Weight: 15% of scored items, about 9 items.

## What this domain measures

This domain is about turning a business goal into a request Claude can do well the first time, and fixing the request when it falls short. The guide groups it into three skills:

- **Prompt construction.** Building a prompt around the business objective, the audience, the constraints and the source material. That means stating a role, the context, the task and the output format, and above all supplying the context Claude does not have by default: your company, your audience, your data, your standards.
- **Task decomposition and sequencing.** Breaking a deliverable that is too large or has too many parts for one request into stages. This covers staged prompts, follow-ups, Project workflows, using a different prompting approach for different task types (analysis, research, drafting, brainstorming), and placing validation checkpoints between steps.
- **Prompt diagnosis and iteration.** Looking at a weak output and tracing it to a specific gap in the request: missing context, an ambiguous instruction, an absent constraint, or the wrong strategy for the task type. Then judging whether to iterate, restart, or change approach.

Why it matters: most disappointing Claude output in real work traces back to the request, not the model, so the person who can name the gap fixes it in one turn instead of ten.

## How questions look

A typical stem describes a professional who asked Claude for something and got an output that was generic, too long, off-target or wrong in a specific way. You are asked for the most likely cause, or the single change that would most improve the next response. Other stems describe a large deliverable and ask how to structure the work.

The options usually include one change that addresses the actual gap, and several that feel like effort but miss it: making the prompt longer or more emphatic, switching to a more capable model, regenerating, or asking Claude to "be more accurate". Some items ask you to choose two additions to a prompt.

## A method for these questions

1. Find the business objective and the audience in the stem. Write them down in a few words.
2. Compare what the person asked for with what they actually needed. The difference is where the answer lives.
3. Ask what Claude could not have known. Internal facts, audience details, house format, and source documents are never available by default.
4. Name the gap type: missing context, ambiguous instruction, absent constraint, or wrong strategy for the task type.
5. Pick the option that closes that exact gap. Reject options that change the model or repeat the request when the problem is in the specification.
6. For large deliverables, prefer staged work with a check between stages, especially where a later step depends on the accuracy of an earlier one.
7. If a long conversation keeps drifting or repeating old mistakes, consider a fresh start with a consolidated request instead of more corrections.

## Traps that cost points

- **Reaching for a better model first.** If the output is generic because Claude lacked your context, a more capable model will produce a more polished generic answer. Fix the specification.
- **Adding adjectives instead of information.** "More detailed", "more professional" or "be accurate" gives Claude nothing new to work with. Supplying facts, audience and format does.
- **Asking Claude to fill gaps with "typical" practice.** For anything specific to your organization, this invites invented content. Give it the real source or tell it to flag what is missing.
- **One giant prompt for a multi-part deliverable.** Long single requests tend to produce shallow coverage of each part. Staging with checkpoints gives you depth and a chance to catch errors early.
- **Checking only the final output.** When step 3 depends on numbers from step 1, an unchecked error in step 1 flows into everything. Put a checkpoint where errors would propagate.
- **Using the same strategy for every task type.** Brainstorming benefits from asking for many varied options. Analysis benefits from exact computation and explicit criteria. Drafting benefits from audience, tone and format.
- **Endless iteration in a drifting chat.** After many corrections, a conversation can keep pulling back toward earlier versions. A clean restart with one consolidated request is often faster.
- **Rewording when the information does not exist.** If Claude cannot have the data (internal figures, private plans), no wording will produce it. Supply the data.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, which is about 1.7 minutes per item. Domain 1 stems are usually short and the answer turns on spotting the gap, so aim for about 1.5 minutes here and bank the time for heavier Domain 2 and Domain 4 items. If two options both look like improvements, pick the one that supplies something Claude could not have known.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Prompt structure | Role, context, task and output format, built around objective, audience, constraints and source material |
| Context Claude lacks by default | Your organization, your audience, your internal data, your standards and your documents |
| Missing context | Output is generic or plausible but not specific to your situation |
| Ambiguous instruction | Claude made a reasonable reading of words that could mean several things |
| Absent constraint | Output is the wrong length, count, scope or format because no limit was stated |
| Wrong strategy for task type | For example, asking for "the best" single answer when the task is brainstorming |
| Decomposition | Split large deliverables into staged prompts or a Project workflow |
| Validation checkpoints | Check outputs between stages, especially before later steps build on them |
| Iterate, restart or change approach | Iterate for small gaps, restart when a chat drifts, change approach when the strategy is wrong |
| Missing information | If Claude cannot know it, supply it; rewording will not help |

## Practice items

These 4 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

An operations lead at a logistics company asks Claude to "write a welcome email for new warehouse staff." The draft is friendly but generic: it mentions "our values" and "your manager" without naming anything real. The new hires start Monday at one specific site that has its own parking rules, shift start times and safety briefing. Which change to the prompt would most improve the next draft?

- A. Ask Claude to make the email more detailed and more professional in tone.
- B. State the audience and purpose, and give the site's parking rules, shift times, safety briefing details and the format required.
- C. Switch to a more capable model and regenerate the same request.
- D. Regenerate the draft three times and combine the best sentences from each version.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The draft is generic because Claude has no way to know the site's details. Supplying the audience, the purpose and the specific facts closes the missing-context gap that caused the problem.

- **A** is wrong because adjectives add no information; Claude still does not know the parking rules or shift times.
- **C** is wrong because a more capable model cannot know site facts it was never given.
- **D** is wrong because every regeneration lacks the same facts, so combining them still gives a generic email.

</details>

### Item 2

A communications manager wants a name for a new internal recognition program. She asks Claude, "What is the best name for our employee recognition program?" Claude returns one safe, forgettable name with a short justification. She wants options her committee can debate next week. What change would best fit this task?

- A. Add the company's brand guidelines and ask again for the single best name.
- B. Tell Claude to be much more creative and repeat the same request.
- C. Start a new chat and ask the same question so earlier context does not interfere.
- D. Ask for a broad set of varied candidates with a one-line rationale each, then narrow them in a follow-up.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** This is a brainstorming task, and asking for "the best" single answer is the wrong strategy for it. Asking for many varied options and narrowing later matches the task type and gives the committee real choices.

- **A** is wrong because brand guidelines help, but the request still produces one name.
- **B** is wrong because "be creative" does not change the request for a single answer.
- **C** is wrong because there was no context problem; the same question produces the same kind of answer.

</details>

### Item 3

A business analyst uses Claude in three stages to build a market-sizing estimate. Stage one gathers population and adoption figures from sources, stage two calculates the market size, and stage three drafts a recommendation for the leadership team. Every later stage uses the figures from stage one. Where is a validation checkpoint most important?

- A. Only after stage three, because the recommendation is what leadership will read.
- B. Only on the formatting of the final document, because the figures came from Claude.
- C. Nowhere in particular, because staging the work already prevents errors.
- D. After stage one, by checking the gathered figures against their sources before calculating.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Every later stage builds on the stage-one figures, so an error there spreads into the calculation and the recommendation. Checking the inputs before calculating stops the error at its source.

- **A** is wrong because by stage three a bad input is buried inside a calculation and harder to spot.
- **B** is wrong because formatting checks do not catch wrong figures.
- **C** is wrong because staging creates places to check; it does not check anything by itself.

</details>

### Item 4

A department head asks Claude to produce next year's internal headcount plan by team. Claude returns specific numbers for each team. She rewords the request four times, adding "be accurate" and "use real figures," and each time gets different numbers. The real figures are in the finance team's approved budget file, which she has not shared. What should she do?

- A. Stop rewording and give Claude the approved budget file to work from.
- B. Add a stronger instruction that the numbers must be accurate.
- C. Switch to the most capable model and the highest effort level.
- D. Ask Claude to cite where each number came from.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Claude has no access to the company's internal budget, so any numbers it produces are invented. No wording can produce real internal figures; the fix is to supply the source.

- **B** is wrong because she has already tried that, and accuracy instructions cannot create data Claude does not have.
- **C** is wrong because no model can know an internal budget it was never given.
- **D** is wrong because there is no real source for Claude to cite, so the request invites fabricated citations.

</details>

Want more? The free timed Associate, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-associate).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-associate) to practise pacing across the whole exam.

---

---

---

<!-- nav:bottom -->
← Previous: [Exam overview: Associate, Foundations](README.md) | Next: [Domain 2: Output Evaluation and Quality Control](02-output-evaluation-and-quality-control.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
