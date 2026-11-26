<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 3: Capability, Model, and Context Selection](03-capability-model-and-context-selection.md) · Next: [Domain 5: Configuration and Knowledge Management](05-configuration-and-knowledge-management.md)
<!-- /nav:top -->

# Domain 4: Workflow Design and Integration

Weight: 18% of scored items, about 11 items.

## What this domain measures

This domain moves from single requests to whole workflows: how Claude fits into real work from start to finish, who does which step, how to make it repeatable, and how to explain it to people who are new to AI. The guide lists four skills:

- **End-to-end deliverable workflows.** Using Claude across the life of a research, analysis or planning deliverable: analyzing the requirements, researching with cited sources, analyzing data through code execution, and turning it all into an actionable plan.
- **Delegation and workflow redesign.** Looking at an existing workflow through Delegation criteria (how reversible a step is, its stakes, whether it needs human judgment or empathy, and who is accountable) and assigning each step as AI-executed, human-retained or human-reviewed. It also covers spotting over-delegation.
- **Repeatable workflow design.** Turning a recurring task that is done by hand or with loose prompting into a repeatable workflow using Projects, Skills, connectors and code-executed steps. The aim is consistent results across runs and across users, including workflows that a colleague will own after you hand them over.
- **Capability communication.** Explaining to stakeholders with little AI experience what Claude can and cannot reliably do for a proposed use, what human oversight stays in place, and what outcomes to realistically expect, without overstating or understating.

Why it matters: the value of Claude in a team comes from workflows that run reliably for everyone, with people in the right places, and from stakeholders who trust it for the right reasons.

## How questions look

Stems describe a team process, a recurring task or a stakeholder conversation. You are asked which step comes first, how to assign a step, which setup makes results consistent, what to include in a handover, or how to answer a manager's question about Claude. "Choose two" items often ask for signs of over-delegation or for the factors that justify human review.

Distractors include automating a step that needs human judgment, keeping a person on a step that is safe to delegate, relying on individual prompts for team consistency, and statements to stakeholders that promise too much or too little.

## A method for these questions

1. For a lifecycle question, place the stem in the sequence: requirements, research with sources, data analysis, synthesis into a plan. The first missing stage is often the answer.
2. For a delegation question, test the step against the four criteria: Can it be undone? What happens if it is wrong? Does it need human judgment or empathy? Who is accountable?
3. Assign the step. Low stakes and reversible: AI-executed. Consequential but checkable: human-reviewed. Judgment, empathy or accountability that must sit with a person: human-retained, with Claude preparing material if useful.
4. For a repeatability question, look for what varies between runs or users and fix it with shared setup: a Project, an approved Skill, a connector to the live source, or a code-executed step.
5. For a handover, include everything the new owner needs to run and fix it without you.
6. For a stakeholder question, choose the answer that is specific about what Claude does, what people still do, and what results to expect, with no invented numbers.

## Traps that cost points

- **Automating the decision instead of the preparation.** Claude can gather, summarize and draft for a consequential decision. The decision itself often belongs to an accountable person.
- **Treating "it has clear rules" as permission to automate high-stakes steps.** Rules do not remove the stakes or the need for accountability.
- **Keeping every step human out of caution.** Low-stakes, reversible, repetitive steps are good candidates for AI execution, and over-caution wastes the workflow's value.
- **Fixing team inconsistency with tips.** Shared prompts in a document do not make outputs consistent. A shared Project, approved Skill and common sources do.
- **Handing over a workflow as a chat link.** The next owner needs the setup, the sources, the review points and the known failure modes.
- **Letting computations vary between runs.** In a repeatable workflow, exact figures should come from a code-executed step.
- **Overstating to win support.** Promising that Claude will replace a role or quoting made-up time savings sets the project up to fail.
- **Understating to seem careful.** Telling staff Claude cannot do something it does well stops useful adoption just as surely.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, which is about 1.7 minutes per item. Workflow stems can describe several steps, so allow about 2 minutes. When a stem lists many steps, find the single step the question asks about before you read the options.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Deliverable lifecycle | Requirements, research with cited sources, data analysis by code execution, synthesis into an actionable plan |
| Delegation criteria | Reversibility, stakes, need for human judgment or empathy, accountability |
| AI-executed | Low stakes and reversible; Claude does the step |
| Human-reviewed | Claude does the step and a person checks before it takes effect |
| Human-retained | A person does the step; Claude may prepare inputs |
| Over-delegation | Consequential, irreversible or empathy-heavy steps given to Claude without a person |
| Repeatable workflow | Shared Project, approved Skills, connectors and code-executed steps for consistency across runs and users |
| Handover | Documented setup, sources, review checkpoints and known failure points, ideally with a walkthrough |
| Capability communication | What Claude can and cannot reliably do, the oversight kept, realistic outcomes |
| Balance | Neither overstate nor understate capability |

## Practice items

These 4 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

An operations analyst has been asked to recommend whether the company should consolidate its four office-supply vendors into one. She has a spending export, access to Claude's research and code-executed analysis, and two weeks. Nobody has yet agreed what "better" means for the decision: lowest cost, fewest suppliers, or best delivery reliability. What should she do first?

- A. Start research on the leading national office-supply vendors and their terms.
- B. Ask Claude for a final recommendation now, then refine it as data comes in.
- C. Upload the spending export and have Claude compute totals by vendor.
- D. Agree the decision and its criteria with stakeholders, using Claude to help structure the requirements.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The lifecycle starts with requirements, and here the criteria for a good outcome are not yet agreed. Without them, research and analysis cannot be aimed at the right question.

- **A** is wrong because research without criteria may gather the wrong information.
- **B** is wrong because a recommendation before requirements, research and analysis has nothing reliable behind it.
- **C** is wrong because the analysis cannot be designed until it is clear which measures matter.

</details>

### Item 2

Five event coordinators each produce a weekly event report with Claude, each using their own prompts. Managers complain that the reports use different sections, different metrics and different tone. The coordinators all draw on the same booking data. What change would make the reports consistent?

- A. Share a document of prompting tips that each coordinator can follow.
- B. Ask each coordinator to save their best prompt and reuse it every week.
- C. Set up a shared Project with common instructions, the booking data source and an approved report Skill.
- D. Have one coordinator write all five reports so the style matches.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Consistency across users needs shared configuration. A shared Project with the same instructions, sources and report Skill means every coordinator starts from the same setup.

- **A** is wrong because tips leave each person writing different prompts.
- **B** is wrong because five personal prompts still produce five different reports.
- **D** is wrong because it removes the workload sharing instead of fixing the workflow.

</details>

### Item 3

A retail operations team runs a weekly inventory variance check. Each week they paste the stock counts into a chat and ask Claude for the variance by store. Running the same data twice has produced slightly different totals, and the team now distrusts the numbers. What should they change?

- A. Add an instruction asking Claude to be especially careful with the totals.
- B. Make variance calculation a code-executed analysis step on the uploaded data.
- C. Switch to a more capable model for this one task.
- D. Run the calculation three times each week and use the average.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The figures must be exact and reproducible. A code-executed step computes from the data the same way every run, which removes the variation.

- **A** is wrong because an instruction to be careful does not change how the totals are produced.
- **C** is wrong because exact, repeatable computation comes from running code, not from a different tier.
- **D** is wrong because averaging inconsistent answers does not produce the correct one.

</details>

### Item 4

A project manager has finished the research and analysis phases of a plan to reduce new-hire attrition. She has verified findings and a few assumptions that could not be confirmed. Leadership has asked for something they can act on at next week's meeting. What should the final deliverable look like?

- A. A long narrative summary of all the research so leadership can see everything that was found.
- B. A list of every source Claude cited during the research phase.
- C. The answer to a fresh prompt asking Claude for "recommendations on attrition," with no other context.
- D. Actions with owners and dates, each tied to a verified finding, with open assumptions flagged.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Synthesis into an actionable plan means turning findings into decisions people can carry out. Linking actions to verified findings and flagging the unconfirmed assumptions keeps the plan honest.

- **A** is wrong because a narrative of everything found is not a plan anyone can act on.
- **B** is wrong because a source list supports a plan but is not one.
- **C** is wrong because it throws away the verified work and invites generic advice.

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
← Previous: [Domain 3: Capability, Model, and Context Selection](03-capability-model-and-context-selection.md) | Next: [Domain 5: Configuration and Knowledge Management](05-configuration-and-knowledge-management.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
