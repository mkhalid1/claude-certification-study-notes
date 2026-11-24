<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 1: Task Definition and Prompting](01-task-definition-and-prompting.md) · Next: [Domain 3: Capability, Model, and Context Selection](03-capability-model-and-context-selection.md)<!-- /nav:top -->

# Domain 2: Output Evaluation and Quality Control

Weight: 20% of scored items, about 12 items.

## What this domain measures

This is the heaviest domain on the exam. It tests whether you judge Claude's output properly before you use it, and whether you shape it for where it is going. The guide lists four skills:

- **Output evaluation and fitness for purpose.** Checking an output against its requirements, its source material or professional standards for accuracy, completeness, relevance and fitness for purpose. Then classifying it: ready to use, needs revision, or needs correction by a person.
- **Hallucination detection and verification.** Spotting unsupported content: fabricated citations, confident invented specifics, internal inconsistencies and bias. Then verifying with source checks, citation review and independent confirmation before the output is used.
- **Review depth and escalation.** Matching the depth of review to the risk. That means weighing the consequence of an error, the difference between an internal draft and an external deliverable, and deciding what must be independently verified, corrected or escalated to a person.
- **Output adaptation and format selection.** Editing and adapting raw output for its audience or channel, and choosing the right format: an inline response, an artifact, structured data, code-executed results when a computation must be exact, or a document produced by a Skill.

Why it matters: you stay accountable for anything you send, and a polished, wrong output is more dangerous than an obviously rough one.

## How questions look

Most stems describe a Claude output that is about to be used: a research summary, a draft for a client, a set of figures, a document for a specific channel. Something about it is off, or its stakes are high, and you choose the next action. Some stems ask you to classify an output, to choose a review depth, or to pick a format.

Distractors tend to fall into a few families: trusting the output because it looks authoritative, asking Claude to confirm its own work, overreacting by discarding everything, and spending heavy review effort on low-stakes work. "Choose two" items often ask for two verification steps or two edits.

## A method for these questions

1. Identify where the output is going and who will rely on it: internal draft, team discussion, client, regulator, the public.
2. Estimate the consequence of an error and whether it can be undone after delivery. This sets the review depth.
3. Compare the output against its requirements. Note anything missing as well as anything wrong.
4. Look for hallucination signals: precise numbers, named sources, quotations, dates and article numbers that you have not seen in the source.
5. Choose verification that is independent of Claude: the source document, the cited source itself, or a second authoritative source.
6. Classify: ready, revise, or human correction. Revise when the gap is fixable by another pass; escalate when the judgment or the accountability belongs to a qualified person.
7. If the question is about format, ask what the destination needs: exact figures, a downloadable document, a house template, a status page, a machine-readable file.

## Traps that cost points

- **Treating citations as proof.** A named source, a specific figure or a quotation is exactly where invented detail tends to appear. You must check the source says it.
- **Asking Claude to verify itself.** Asking the same system whether it is sure is not independent confirmation.
- **Regenerating to check consistency.** Getting the same number twice does not prove it is true. Check it against a source.
- **Over-reviewing low-stakes work.** A list of raw internal ideas for discussion does not need line-by-line fact-checking. Review depth should match the consequence of an error.
- **Under-reviewing external or hard-to-reverse work.** Anything going to a client, a regulator or the public needs a higher standard, and sometimes the accountable expert.
- **Discarding everything when one part is wrong.** Removing all specifics or starting over throws away verified value. Separate what is supported from what is not.
- **Accepting computed figures from text.** When a total must be exact, use code-executed analysis on the data and check the result.
- **Missing bias because the content is fictional or "just a draft".** Unsupported assumptions about people still shape decisions and need to be corrected against real data.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, which is about 1.7 minutes per item. Domain 2 stems are often longer because they describe an output in detail. Allow about 2 minutes for these, and recover the time on shorter Domain 1 and Domain 3 items. Read the last sentence of the stem first so you know whether you are classifying, verifying or formatting.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Fitness for purpose | Judge against the requirements, the sources and professional standards, not against how good it reads |
| Three classifications | Ready to use, requires revision, requires human correction |
| Hallucination signals | Fabricated citations, confident invented specifics, inconsistencies, bias |
| Independent verification | Source checking, citation review, confirmation from a second authoritative source |
| Self-confirmation | Asking Claude whether it is right is not independent verification |
| Review depth | Scales with consequence of error, reversibility and audience |
| Internal versus external | External deliverables need a higher standard than internal drafts |
| Escalation | Send it to a qualified person when the judgment or accountability is theirs |
| Exact computation | Use code-executed analysis when a figure must be exact |
| Format selection | Inline, artifact, structured data, code-executed result or Skill-produced document, chosen for the destination |

## Practice items

These 5 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A procurement lead asked Claude to compare three shortlisted software vendors on cost, implementation timeline and delivery risk, using the vendors' proposals she uploaded. The output compares cost and timeline clearly and accurately against the proposals. It does not discuss delivery risk at all. The comparison goes to the steering group on Friday. How should she classify this output?

- A. Ready to use, because everything it says matches the proposals.
- B. Requires human correction by a subject matter expert, because it contains errors.
- C. Requires revision: ask for the missing risk comparison, then check the new section against the proposals.
- D. Unusable, so she should discard it and write the comparison herself.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The output is accurate but incomplete against its requirements, which makes it a revision case. Another pass can add the missing dimension, and the new section still needs checking against the proposals.

- **A** is wrong because accuracy is not enough; the output misses one of three required dimensions.
- **B** is wrong because nothing is incorrect, so there is nothing for an expert to correct.
- **D** is wrong because two thirds of the work is accurate and usable.

</details>

### Item 2

A strategy analyst uses Claude's research capability to prepare background for a board paper on supply-chain resilience. The output summarizes findings from eight cited web sources, including several specific statistics. The board will rely on the figures when it decides on investment. Which two steps are the best verification before the figures go into the paper? (Choose two.)

- A. Count the citations, since eight sources show the summary is well supported.
- B. Open each cited source and confirm it actually says what the summary attributes to it.
- C. Ask Claude to rate its confidence in each statistic from one to ten.
- D. Confirm the key statistics in a second authoritative source that was not in the original list.
- E. Check that each citation URL looks like a real, reputable domain.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Citation review means checking that each source supports the specific claim, and independent confirmation means finding the key figures in a separate authoritative source. Together they separate verified claims from unverified ones for a high-stakes decision.

- **A** is wrong because the number of citations says nothing about whether they support the claims.
- **C** is wrong because self-rated confidence is not verification.
- **E** is wrong because a real domain can still not contain the claimed statistic.

</details>

### Item 3

A product marketer asks Claude to create buyer personas from a set of customer interview notes. Every persona for the "decision maker" role is described as a man over 45, although the interview notes record a mix of genders and ages in that role. The personas will guide the next campaign's targeting. What should the marketer do?

- A. Use them as they are, because personas are fictional and do not describe real people.
- B. Delete the personas and stop using Claude for persona work.
- C. Flag the demographic assumptions as unsupported and revise them against the interview data.
- D. Ask Claude to add a disclaimer that the personas may contain assumptions.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The personas contain an assumption that the source material does not support, which is a form of bias that would skew real targeting decisions. Revising against the actual interview data corrects it.

- **A** is wrong because fictional personas still drive real decisions, and these contradict the data.
- **B** is wrong because the rest of the work may be useful; the fix is to correct the unsupported part.
- **D** is wrong because a disclaimer leaves the biased content in place.

</details>

### Item 4

An IT service manager has a detailed Claude-drafted incident analysis. It includes internal system names, the names of engineers on call, a confirmed customer impact window, and some unconfirmed theories about root cause. She needs a version for the public customer status page, and the incident owner signs off public updates. Which two actions are most appropriate? (Choose two.)

- A. Remove internal system names, staff names and unconfirmed theories, keeping only confirmed facts and customer impact.
- B. Publish the full analysis on the status page to show full transparency.
- C. Keep the root-cause theories in the update to show the team is investigating thoroughly.
- D. Convert the analysis into a structured data file so the status page can display it.
- E. Rewrite it in plain language at the length the status page expects, and get the incident owner's approval.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** Adapting for a public channel means removing internal and unconfirmed content and keeping verified facts. It also means matching the audience's language and the channel's length, with the accountable owner approving.

- **B** is wrong because the full analysis exposes internal details and unconfirmed claims.
- **C** is wrong because unconfirmed theories should not be published as part of an official update.
- **D** is wrong because the status page needs readable text, and a format change does not remove the internal content.

</details>

### Item 5

A team lead asks Claude for 30 theme ideas for the department's annual offsite. The list will be read aloud at a planning meeting tomorrow, where the team will vote on a few to explore. Nothing will be shared outside the team or committed to without further discussion. How much review does the list need before the meeting?

- A. A full fact-check of every idea by a second reviewer before it is shared.
- B. Review by the legal team, since it is AI-generated content.
- C. A quick read for anything inappropriate or off-topic, then share it labelled as raw ideas.
- D. None, because the team should not use AI-generated ideas at all.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The stakes are low, the use is internal and nothing is committed without discussion. A quick screen for anything inappropriate is proportionate, and labelling the list as raw ideas sets the right expectation.

- **A** is wrong because a full fact-check is far more review than a list of brainstorm ideas needs.
- **B** is wrong because AI involvement alone does not require legal review; the risk here is minimal.
- **D** is wrong because brainstorming is a reasonable, low-risk use.

</details>

Want more? The free timed Associate, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate) to practise pacing across the whole exam.

---

---

---

<!-- nav:bottom -->← Previous: [Domain 1: Task Definition and Prompting](01-task-definition-and-prompting.md) | Next: [Domain 3: Capability, Model, and Context Selection](03-capability-model-and-context-selection.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
