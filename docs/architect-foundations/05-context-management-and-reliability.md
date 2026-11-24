<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 4: Prompt Engineering & Structured Output](04-prompt-engineering-and-structured-output.md) · Next: [Exam overview: Architect, Professional](../architect-professional/README.md)<!-- /nav:top -->

# Domain 5: Context Management & Reliability

Weight: 15% of scored items, about 9 items.

## What this domain measures

This is the smallest domain by weight, but its ideas appear in four of the six scenarios. It checks whether your systems keep the right facts, admit what they do not know, and hand over to people at the right moments.

- **Keeping critical information in long interactions.** Progressive summarization blurs exact numbers, dates, percentages and what the customer was promised. Models handle the start and end of long inputs better than the middle (the "lost in the middle" effect). Tool results pile up and use tokens out of proportion to their value. Fixes: keep a persistent "case facts" block of transactional details outside the summarized history, keep structured issue data in its own layer for multi-issue sessions, trim tool output to the fields that matter before it accumulates, put a key findings summary at the top of aggregated input with clear section headers, and have upstream agents return structured facts with metadata instead of long prose. Pass the complete conversation history in each API request to keep the conversation coherent.
- **Escalation and ambiguity.** Escalate when the customer asks for a human, when policy has an exception or a gap, or when the agent cannot make progress. Honor an explicit request for a human straight away. If a customer is frustrated but the issue is within the agent's ability, acknowledge it and offer to resolve, and escalate if they repeat the request. Sentiment and self-reported confidence are poor proxies for case complexity. When a lookup returns several possible customers, ask for another identifier instead of guessing. Explicit escalation criteria with few-shot examples in the system prompt calibrate the agent.
- **Error propagation in multi-agent systems.** Subagents should recover from transient failures locally and pass up only what they cannot fix, with the failure type, what was attempted, partial results and possible alternatives. Generic statuses hide that context. Returning empty results as success and aborting the whole workflow on one failure are both anti-patterns. Synthesis output should mark which findings are well supported and where coverage has gaps because a source was unavailable.
- **Large codebase exploration.** In long sessions, answers drift toward "typical patterns" instead of the specific classes found earlier. Counter it with scratchpad files of key findings, subagents for specific questions (so verbose exploration stays out of the main context), summaries of each phase injected into the next phase's subagents, structured state exports and a manifest for crash recovery, and `/compact` when context fills with discovery output.
- **Human review and confidence calibration.** A high overall accuracy figure can hide poor results on one document type or field. Check accuracy by type and field before reducing human review. Use stratified random sampling of high-confidence extractions to keep measuring error rates and spot new error patterns. Have the model output field-level confidence, calibrate thresholds on a labelled validation set, and send low-confidence or contradictory cases to reviewers first.
- **Provenance and uncertainty.** Summarization drops the link between claims and sources unless subagents output structured claim-source mappings that synthesis must keep. When credible sources disagree, report both values with attribution instead of picking one. Require publication or collection dates so differences over time are not mistaken for contradictions. Separate well-established findings from contested ones, and render each content type sensibly (tables for financial data, prose for news, lists for technical findings).

Why it matters: these are the failures users notice. A forgotten refund amount, a confident "no records found" during an outage, or a statistic with no source does more damage to trust than a slow answer.

## How questions look

Stems describe a long or multi-step interaction that degraded: a summary lost a figure the customer cares about, an agent escalated the wrong cases or picked one of several matching accounts, a coordinator could not recover from a subagent failure, a report merged two conflicting numbers, a plan to cut human review rests on one overall accuracy figure. Options contrast a structural fix (a facts block, structured errors, stratified analysis, claim-source mappings) with tempting shortcuts: bigger context, sentiment triggers, confidence self-reports, or silently picking a value. Multiple-response items often ask for the two correct agent behaviours in one situation.

## A method for these questions

1. Ask what information was lost or distorted, and where in the flow it happened: summarization, tool output, handoff, synthesis.
2. Prefer fixes that make the critical information structured and persistent over fixes that add more raw context.
3. For escalation, check the trigger list: explicit request, policy gap or exception, no progress. Treat sentiment and confidence scores as distractors.
4. For ambiguity in data (several matching customers, conflicting sources), the answer asks for more information or annotates the conflict. It never guesses.
5. For errors, check that the reply distinguishes "failed" from "found nothing" and carries what was tried and what partial results exist.
6. For human review, look past the overall number. Ask whether accuracy was measured per document type and per field.

## Traps that cost points

- **Trusting the running summary.** Progressive summaries tend to round off amounts and dates, so keep transactional facts such as order numbers and refund amounts in their own persistent block.
- **Fixing context overflow with a bigger window.** The usual cause is irrelevant fields and important facts buried in the middle of long input, so trim tool results to what matters and put key facts where they are easy to find.
- **Escalating on negative sentiment.** Frustration does not equal complexity, and many angry customers have simple problems.
- **Investigating first when the customer asked for a human.** An explicit request is honored immediately.
- **Picking the most likely customer record.** With several matches, ask for another identifier.
- **Choosing one of two conflicting statistics.** Report both with sources and dates and let the coordinator or reader see the conflict.
- **Reducing review because overall accuracy is 97%.** One document type may be far worse. Segment first.
- **Killing the workflow on one subagent failure, or hiding the failure.** Recover locally, then propagate structured context and continue with partial results where possible.

## Timing

Guidance only: the exam gives 120 minutes for 60 items, so about 2 minutes per item. These items often look like they belong to another domain, because they sit inside support, research or extraction scenarios. When you notice yourself weighing a "bigger model" or "more context" option, pause: in this domain the answer is almost always to make the important information structured, persistent or explicitly flagged.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Case facts block | Amounts, dates, order numbers and statuses kept in every prompt, outside summarized history. |
| Lost in the middle | Put a key findings summary first and use clear section headers. |
| Trim tool output | Keep only relevant fields before results accumulate. |
| Structured upstream output | Key facts, citations and relevance scores instead of long reasoning, when downstream context is tight. |
| Escalation triggers | Explicit request for a human, policy exception or gap, inability to progress. |
| Explicit request | Escalate immediately, without investigating first. |
| Frustrated but solvable | Acknowledge, offer to resolve, escalate if the customer repeats the request. |
| Unreliable proxies | Sentiment and self-reported confidence do not measure case complexity. |
| Multiple matches | Ask for another identifier. Do not guess. |
| Structured handoff | Customer ID, root cause, amounts, recommended action for humans who cannot see the transcript. |
| Error context | Failure type, what was attempted, partial results, alternatives. |
| Anti-patterns | Silent empty success, and aborting everything on one failure. |
| Coverage annotations | Mark well-supported findings and gaps caused by unavailable sources. |
| Scratchpad files | Persist key findings across context boundaries in long explorations. |
| Crash recovery | Agents export state to a known location; the coordinator loads a manifest on resume. |
| `/compact` | Reduce context during long exploration sessions. |
| Segmented accuracy | Check by document type and field before reducing human review. |
| Stratified sampling | Ongoing error measurement on high-confidence output and detection of new patterns. |
| Claim-source mappings | URLs, document names and excerpts that synthesis must preserve. |
| Conflicts and dates | Annotate conflicting values with sources. Require publication or collection dates. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

An airline's support agent handles long rebooking conversations. To save tokens, older turns are replaced with a running summary. In a 40-turn conversation, the customer agreed early on to a refund of a specific amount plus a voucher by a stated date. By the end, the agent's summary says only "customer was offered compensation", and its final confirmation gets the amount wrong. What is the best fix?

A. Stop summarizing and keep the full transcript for every conversation regardless of length.
B. Extract transactional facts (amounts, dates, booking references, agreed actions) into a persistent case facts block included in every prompt, outside the summarized history.
C. Ask the summarizer to "keep important details" when it condenses older turns.
D. Add a final step that asks the customer to restate what was agreed.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Progressive summarization tends to blur exact figures and commitments. Keeping those facts in a structured block that travels with every prompt protects them, while the rest of the history can still be summarized.

- **A** is wrong because it gives up the token savings and leaves the facts buried mid-context, where they are easy to miss.
- **C** is wrong because a vague instruction does not stop summaries from condensing numbers and dates.
- **D** is wrong because it pushes the agent's job onto the customer and undermines confidence in the service.

</details>

### Item 2

An electronics retailer's warranty agent calls `get_order` several times per conversation. Each call returns over 50 fields, including warehouse codes, tax breakdowns and marketing flags. Only about six fields matter for warranty decisions. In long conversations, the agent starts missing details the customer gave earlier. What change best addresses the cause?

A. Trim each `get_order` result to the warranty-relevant fields before it is added to the conversation.
B. Move the agent to a model with a larger context window.
C. Ask the agent to ignore irrelevant fields when reading tool results.
D. Call `get_order` only once per conversation and tell the agent to remember the result.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Verbose tool results accumulate and consume context out of proportion to their relevance. Trimming them to the needed fields before they enter the conversation keeps the important details visible.

- **B** is wrong because a bigger window delays the problem and does not stop irrelevant fields from crowding out what matters.
- **C** is wrong because the fields are still in context using tokens. Asking the model to ignore them does not remove them.
- **D** is wrong because order status can change during the conversation, and the single bloated result still sits in context.

</details>

### Item 3

A research coordinator combines 25 subagent reports into one long input for a synthesis step. Reviewers notice that findings from reports placed in the middle of the input rarely appear in the final synthesis, while the first and last few reports are always well represented. What change most directly addresses this?

A. Shuffle the order of the reports on every run so each one appears near the start sometimes.
B. Ask the synthesis step to give every report equal weight.
C. Drop the middle reports to keep the input shorter.
D. Put a summary of the key findings from all reports at the start of the input, and organize the detailed reports under explicit section headers.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** This is the "lost in the middle" effect. A key findings summary at the beginning ensures every report's main points sit where the model attends reliably, and section headers make the detail easy to locate.

- **A** is wrong because random order just moves the blind spot around and makes output inconsistent between runs.
- **B** is wrong because an instruction does not change how the model processes position in long inputs.
- **C** is wrong because it throws away valid findings.

</details>

### Item 4

A bank's support agent is chatting with a customer whose debit card PIN is locked, a routine fix the agent can do in one step. The customer writes, "I don't want to do this with a bot. Put me through to a person, please." The agent is designed to maximize first-contact resolution. What should it do?

A. Unlock the PIN first since it only takes a moment, then offer to connect the customer to a person.
B. Acknowledge the frustration and offer to fix the PIN now, escalating only if the customer asks again.
C. Escalate to a human agent right away, honoring the request.
D. Check the customer's sentiment score and escalate only if it is below the threshold.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** An explicit request for a human is an escalation trigger to honor immediately, without first investigating or resolving. The customer has said clearly what they want.

- **A** is wrong because it overrides an explicit request in favour of the agent's metrics.
- **B** is wrong because "acknowledge and offer to resolve" is the pattern for a frustrated customer who has not asked for a human. This customer has asked directly.
- **D** is wrong because sentiment is not a reliable escalation signal, and the explicit request already settles it.

</details>

### Item 5

A streaming service's support agent meets two situations in one afternoon. In the first, `get_customer` returns three accounts named "Sam Lee" in the same city. In the second, a customer asks to transfer their annual subscription to a family member. The policy covers pausing and cancelling subscriptions but says nothing about transfers. Which two behaviours are correct? (Choose two.)

A. Pick the "Sam Lee" account with the most recent activity, since that is most likely the one contacting support.
B. Ask the customer for another identifier, such as the account email or the last digits of the payment card, before continuing.
C. Handle the transfer by cancelling the subscription and asking the family member to sign up, since that achieves the same result.
D. Escalate the transfer request to a human, because the policy is silent on it.
E. Escalate both situations only if the customer's tone turns negative.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Multiple matches call for clarification with an additional identifier, not heuristic selection. A request the policy does not address is a policy gap, which is an escalation trigger even when the agent could improvise something.

- **A** is wrong because guessing between matching accounts risks exposing or changing the wrong customer's data.
- **C** is wrong because it invents policy. Cancelling may forfeit value the customer paid for, and nobody approved that path.
- **E** is wrong because sentiment does not measure whether the agent has the authority or the right identity to act.

</details>

### Item 6

A utilities company's agent escalates complex billing disputes through `escalate_to_human`. Human agents work in a separate console and cannot see the chat transcript. Today the agent passes a one-line note such as "Customer disputes bill, please help." Human agents report spending most of each case re-asking the customer for details. What should the escalation include?

A. The full raw chat transcript pasted into the note field.
B. A note telling the human agent to read the customer's account history before calling.
C. The customer's sentiment score and the agent's confidence rating.
D. A structured handoff summary: customer ID, the disputed amounts, the root cause the agent identified, what was already checked, and the recommended action.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Human agents who lack the transcript need a structured summary that lets them act straight away. Customer identity, amounts, root cause and a recommended action cover what they would otherwise have to re-ask.

- **A** is wrong because a raw transcript is long and unstructured, and the human still has to dig out the key facts, the root cause and the next step before acting.
- **B** is wrong because account history does not contain what was learned in the conversation.
- **C** is wrong because sentiment and self-reported confidence say little about the case and nothing about what to do.

</details>

### Item 7

In a multi-agent market research system, the document retrieval subagent sometimes hits brief timeouts from a publisher's API. Currently, any timeout is reported to the coordinator immediately as "retrieval failed", and the coordinator stops the whole research run. Most of these timeouts would succeed on a second try. What is the best design?

A. Have the subagent catch timeouts and return an empty document list marked as successful.
B. Have the subagent retry transient timeouts itself, and propagate to the coordinator only failures it cannot resolve, with the failure type, the query attempted, any partial results and possible alternatives.
C. Keep reporting every timeout immediately, but have the coordinator retry the whole run from the beginning.
D. Remove the timeout limit on the publisher API so calls never time out.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Subagents should handle transient failures locally and escalate only what they cannot fix, with enough structured context for the coordinator to decide: retry differently, use another source, or continue with partial results. That also avoids stopping the whole run for one failure.

- **A** is wrong because reporting a failure as an empty success hides it, and the final report will silently miss sources.
- **C** is wrong because restarting everything for one transient failure wastes all completed work, and the generic status still gives the coordinator nothing to reason with.
- **D** is wrong because calls that hang forever are worse than timeouts, and it avoids designing proper error handling.

</details>

### Item 8

An investment research pipeline asks a document analysis subagent to report the size of a regional battery storage market. One credible industry report gives a figure for 2022, and a government statistics release gives a much higher figure for 2025. The synthesis report currently shows only the higher number, with no source, and reviewers think the sources simply disagree. What change best fixes this?

A. Instruct the subagent to always pick the more recent figure and drop the other.
B. Average the two figures and report the result.
C. Require the subagent to output each figure as a structured claim with its source, excerpt and data collection date, keep both in the analysis, and flag any genuine conflict for the coordinator to reconcile.
D. Instruct the synthesis step to leave out any statistic that differs between sources.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Structured claim-source mappings with dates keep attribution through synthesis and show that the figures describe different years, which may not be a contradiction at all. Genuine conflicts are annotated and left for the coordinator to reconcile, instead of being resolved arbitrarily.

- **A** is wrong because it selects a value arbitrarily, drops attribution, and loses the trend the two dates reveal.
- **B** is wrong because averaging figures from different years and methods produces a number neither source supports.
- **D** is wrong because removing contested data hides useful information instead of presenting it honestly.

</details>

### Item 9

An engineer uses an Agent SDK assistant to understand a large legacy insurance platform over a long session. After a couple of hours, the assistant's answers start describing "typical repository patterns" instead of the specific `PolicyLedger` and `ClaimRouter` classes it found earlier, and its answers contradict each other. Which two practices best counter this? (Choose two.)

A. Have the assistant maintain a scratchpad file of key findings (classes, flows, decisions) and consult it for later questions.
B. Ask the assistant to re-read the entire repository before each new question.
C. Raise `max_tokens` so the assistant can write longer answers.
D. Paste the full source of every module into the conversation at the start of the session.
E. Delegate specific investigations, such as tracing the claims routing flow, to subagents that return summaries while the main agent keeps the high-level picture.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** Scratchpad files persist key findings beyond the point where context degrades, so the assistant can refer back to specific facts. Subagents keep verbose exploration out of the main context and return only what the main agent needs.

- **B** is wrong because re-reading everything floods the context again and makes degradation worse.
- **C** is wrong because `max_tokens` limits response length. It does nothing about context degradation.
- **D** is wrong because loading everything up front exhausts the context early, the opposite of incremental exploration.

</details>

### Item 10

An insurance company's claims extraction pipeline reports 97% field-level accuracy overall, and leadership wants to stop human review of extractions the model marks as high confidence. Typed claim forms make up 85% of volume. Handwritten forms and scanned faxes make up the rest. Nobody has looked at accuracy by document type. What should the team do before reducing review?

A. Reduce review now, since 97% exceeds the target, and investigate only if complaints rise.
B. Ask the model to give an overall confidence score for each document and trust it for routing.
C. Measure accuracy by document type and by field, and set up stratified random sampling of high-confidence extractions to keep tracking error rates and detect new error patterns.
D. Increase the confidence threshold for automation from high to very high.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** An overall figure can hide poor performance on smaller segments such as handwritten forms. Segmenting by document type and field shows whether performance is consistent, and stratified sampling keeps measuring error rates on high-confidence output after review is reduced.

- **A** is wrong because the aggregate number may mask a segment with far lower accuracy, and complaints are a slow, costly detector.
- **B** is wrong because uncalibrated, document-level confidence is not a reliable basis for routing. Confidence should be field-level and calibrated on a labelled set.
- **D** is wrong because a stricter threshold without per-segment measurement still does not reveal whether high-confidence extractions are accurate for every document type.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 4: Prompt Engineering & Structured Output](04-prompt-engineering-and-structured-output.md) | Next: [Exam overview: Architect, Professional](../architect-professional/README.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
