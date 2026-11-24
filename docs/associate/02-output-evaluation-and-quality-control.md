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

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

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

A compliance coordinator uploads the full text of a new industry regulation and asks Claude for a one-page summary. The summary cites a specific section number for the record-keeping rule and gives an exact date when the rule takes effect. The coordinator cannot remember seeing that date in the document. The summary will be circulated to department heads as guidance. What should she do before circulating it?

- A. Find each section number and date in the uploaded regulation, and remove or flag anything she cannot find there.
- B. Ask Claude whether it is confident that the section number and date are correct.
- C. Circulate it, because Claude was working directly from the full regulation text.
- D. Regenerate the summary and check whether the same date appears again.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Precise section numbers and dates are typical places for confident invented specifics, even when a source was supplied. Checking each one against the source document is independent verification.

- **B** is wrong because Claude confirming its own output is not independent.
- **C** is wrong because having a source available does not guarantee every detail came from it.
- **D** is wrong because getting the same answer twice is consistency, not verification.

</details>

### Item 3

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

### Item 4

An operations manager at a utility company asks Claude to draft the company's response to a formal information request from its industry regulator. The draft uses internal documents she uploaded and reads well. The response will be filed on the record, and errors could lead to penalties. What review is appropriate before it is sent?

- A. A quick proofread for tone and spelling, as she would do for an internal email.
- B. Ask Claude to review its own draft for factual errors and fix any it finds.
- C. Send it, since the content was drawn from the company's own documents.
- D. Have the accountable compliance or legal owner verify each factual statement and approve it.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A regulatory filing is external, on the record and hard to reverse, with real consequences for errors. That calls for verification of every factual claim and approval by the person accountable for it.

- **A** is wrong because an internal-draft standard is far too light for a regulatory filing.
- **B** is wrong because self-review by Claude is not independent and does not provide accountable approval.
- **C** is wrong because drawing on internal documents does not guarantee every statement is accurate.

</details>

### Item 5

A finance operations specialist must reconcile 3,000 invoice lines in an export, including currency conversions at given rates, and report one exact total to the controller. A difference of even a small amount will hold up the month-end close. She wants Claude to help. Which output approach fits this need?

- A. Code-executed analysis on the uploaded export, then a check of the method and a sample of lines.
- B. An inline chat answer in which Claude reads the export and states the total.
- C. An artifact containing a formatted summary table that Claude writes from the data.
- D. An estimate from Claude, rounded to the nearest thousand to allow for small errors.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The figure must be exact, which is the case for code-executed results. Running the computation on the data, then checking the method and some lines, gives a result she can stand behind.

- **B** is wrong because a total stated in text is not computed and may not be exact.
- **C** is wrong because a formatted table written from reading the data still lacks an exact computation.
- **D** is wrong because the controller needs an exact figure, not a rounded estimate.

</details>

### Item 6

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

### Item 7

A regional manager asks Claude to summarize quarterly performance from a set of reports. The executive summary says revenue grew by 8 percent. A table later in the same output shows revenue falling from the previous quarter. The summary goes to the leadership team on Monday. What should the manager do?

- A. Trust the table, because tables are generally more precise than prose.
- B. Check both statements against the source reports and correct the output before it goes out.
- C. Trust the executive summary, because leadership will mainly read that section.
- D. Ask Claude to reconcile the two statements and accept whichever one it chooses.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** An internal inconsistency is a warning sign that at least one statement is unsupported. Only the source reports can show which is right, so both should be checked before use.

- **A** is wrong because the table could be the wrong one; format does not decide accuracy.
- **C** is wrong because what leadership reads first does not make it correct.
- **D** is wrong because accepting Claude's choice is not independent verification.

</details>

### Item 8

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

### Item 9

An account manager produces a weekly progress report for a client, who opens it in standard office software and expects the company's branded document layout. Her organization has an approved custom Skill that produces reports in that layout. Last week she pasted an inline chat response into a blank document, and the client asked why the format had changed. What should she use this week?

- A. An inline chat response, copied into the client's usual file.
- B. An artifact that shows the report as an interactive page.
- C. A structured data file the client can import.
- D. The approved report Skill, to produce the document in the branded layout.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The destination is a branded document opened in standard office software, and an approved Skill exists to produce exactly that. Using it gives the client the format they expect every week.

- **A** is wrong because that is what caused the format complaint last week.
- **B** is wrong because an interactive page is not the branded document the client expects.
- **C** is wrong because the client needs a readable report, not a data import.

</details>

### Item 10

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

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate) to practise pacing across the whole exam.

---

---

---

<!-- nav:bottom -->← Previous: [Domain 1: Task Definition and Prompting](01-task-definition-and-prompting.md) | Next: [Domain 3: Capability, Model, and Context Selection](03-capability-model-and-context-selection.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
