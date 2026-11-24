<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 3: Claude Code Configuration & Workflows](03-claude-code-configuration-and-workflows.md) · Next: [Domain 5: Context Management & Reliability](05-context-management-and-reliability.md)<!-- /nav:top -->

# Domain 4: Prompt Engineering & Structured Output

Weight: 20% of scored items, about 12 items.

## What this domain measures

This domain is about getting output that is precise, consistent and machine-safe, at production volume.

- **Explicit criteria.** Precise categories beat vague instructions. "Flag a comment only when what it claims contradicts what the code does" works better than "check comments are accurate". Instructions like "be conservative" or "only report high-confidence findings" do not improve precision much. Define what to report and what to skip, give severity levels concrete code examples, and if one category produces mostly false positives, switch it off temporarily while you fix its prompt, because noisy categories make developers distrust the accurate ones too.
- **Few-shot examples.** When detailed instructions still give inconsistent results, 2 to 4 targeted examples are the most effective fix. Use them to show the exact output format (location, issue, severity, suggested fix), to show how to handle ambiguous cases and why one action beats a plausible alternative, to separate acceptable patterns from real issues, and to show extraction from varied document layouts. Good examples help Claude generalize to cases you did not list, and they reduce hallucinated or empty extractions.
- **Structured output with tool use.** Defining an extraction tool with a JSON schema and reading the `tool_use` response is the most reliable way to get schema-compliant output. It removes JSON syntax errors but not semantic errors (line items that do not sum to the total, values in the wrong field). `tool_choice` `"auto"` lets Claude answer in text, `"any"` forces some tool call (useful when several extraction schemas exist and the document type is unknown), and forced selection names the tool that must run (for example a metadata extraction before enrichment). Make fields optional or nullable when the source may not contain them, so the model does not invent values. Add enum values such as `"unclear"`, and an `"other"` value plus a detail string for extensible categories. Put format normalization rules in the prompt alongside the schema.
- **Validation and retry.** On a validation failure, send a follow-up containing the original document, the failed extraction and the specific errors. Retries fix format and structure problems. They cannot fix missing information, for example when the value lives in a document you did not provide. Add a `detected_pattern` field to findings so you can analyse what developers dismiss. Build self-checks into the schema, such as `calculated_total` beside `stated_total` and a `conflict_detected` boolean.
- **Batch processing.** The Message Batches API gives 50% cost savings with a processing window of up to 24 hours and no latency guarantee. It suits latency-tolerant work (overnight reports, weekly audits, nightly test generation) and not blocking work (pre-merge checks). It does not support multi-turn tool calling inside one request. `custom_id` correlates requests with results. Work out submission frequency from your SLA, resubmit only failed items by `custom_id` with fixes such as chunking, and refine the prompt on a sample before sending a large batch.
- **Multi-instance and multi-pass review.** A model reviewing its own output in the same session keeps its earlier reasoning and is less likely to question it. An independent instance catches more than self-review instructions or extended thinking. Split large reviews into per-file passes plus a cross-file integration pass. Ask for a confidence value per finding to route review attention.

Why it matters: in production, output is read by code before it is read by people. A prompt that is right 90% of the time and a schema that accepts invented values both turn into data quality incidents.

## How questions look

Stems describe a pipeline that mostly works: a review bot developers have started ignoring, an extractor that fills fields with plausible but invented values, valid JSON whose numbers do not add up, retries that never converge, a batch job that misses its deadline. Options pit a specific technique (explicit criteria, targeted examples, nullable fields, forced tool choice, retry with error feedback) against vaguer instructions, heavier infrastructure, or a technique aimed at a different failure. Multiple-response items often ask for two schema or validation changes that together solve the problem.

## A method for these questions

1. Name the failure precisely: wrong format, wrong judgement on ambiguous cases, invented values, syntax errors, semantic errors, latency, or false positives.
2. Map it to the matching tool. Format and ambiguity point to few-shot examples. False positives point to explicit criteria. Invented values point to nullable fields. Syntax errors point to tool use with a schema. Semantic errors point to validation plus retry.
3. Before choosing a retry answer, ask whether the information is actually in the input. If it is not, no retry will help.
4. For `tool_choice`, ask whether you need "some tool" (`"any"`) or "this tool" (forced).
5. For batch questions, check whether anyone is waiting on the result, and do the SLA arithmetic: wait before submission plus up to 24 hours of processing must fit inside the SLA.
6. For review quality, check whether the reviewer shares the generator's context. If it does, an independent instance is usually the answer.

## Traps that cost points

- **"Only report high-confidence issues."** Precision improves when you give categorical criteria for what to report. Asking for "high confidence" leaves it unchanged.
- **Assuming a strict schema means correct data.** Tool use removes syntax errors only. Totals can still be wrong and values can still land in the wrong field.
- **Making every field required.** If the source lacks a value, a required field invites the model to make one up.
- **Retrying when the data is not there.** A retry with error feedback cannot extract a date that is only in a document you never sent.
- **Using `"any"` to force order.** It guarantees that some tool is called, and leaves the choice of tool to the model.
- **Batching blocking work.** No latency SLA means a pre-merge check could wait up to a day.
- **Resubmitting the whole batch after partial failure.** Resubmit only the failed `custom_id`s, fixed (for example, chunked).
- **Asking the generator to review itself harder.** Its own reasoning is in context. Use an independent instance.

## Timing

Guidance only: the exam gives 120 minutes for 60 items, so about 2 minutes per item. Batch SLA items involve a small calculation. Write the arithmetic down on the scratch material the proctor provides (if any) rather than holding it in your head. Most other items in this domain turn on naming the failure type, which you can usually do from the stem's last two sentences.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Explicit criteria | Define what to report and skip. Vague confidence instructions do not raise precision. |
| False positive control | Temporarily disable high false-positive categories while fixing their prompts. |
| Severity | Give each severity level concrete code examples for consistent classification. |
| Few-shot examples | 2 to 4 targeted examples for ambiguity, format, acceptable vs real issues, and varied document layouts. |
| Tool use with JSON schema | Most reliable schema compliance. Read the result from the `tool_use` block. |
| Syntax vs semantic errors | Schemas remove syntax errors. Semantic errors need validation logic. |
| `tool_choice` `"any"` | Must call some tool. Good when several schemas exist and the document type is unknown. |
| Forced tool | `{"type": "tool", "name": "extract_metadata"}` makes that tool run first. |
| Nullable fields | Prevent fabricated values when the source may not contain the data. |
| `"unclear"` and `"other"` + detail | Enum patterns for ambiguous and extensible categories. |
| Retry with error feedback | Send the document, the failed extraction and the specific validation errors. |
| Limits of retry | Useless when the information is absent from the input. |
| `detected_pattern` | Lets you analyse which constructs trigger dismissed findings. |
| Self-check fields | `calculated_total` beside `stated_total`, `conflict_detected` for inconsistent sources. |
| Message Batches API | 50% cost savings, up to 24 hours, no latency SLA, no multi-turn tool calling, `custom_id` correlation. |
| SLA arithmetic | Submission interval plus 24 hours must fit the SLA. |
| Independent review | A separate instance without generator context beats self-review. |
| Multi-pass review | Per-file local passes plus a cross-file integration pass. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A SaaS company's PR review bot flags 40 to 60 items per pull request. Most are naming preferences and formatting nits that the team's style allows, and developers now merge without reading the bot's comments. The review prompt currently ends with "Only report issues you are highly confident about." What change will most improve precision?

A. Add "Be very conservative and avoid minor issues" to the prompt.
B. Ask the bot to rate its confidence from 1 to 10 and hide anything below 8.
C. Replace the confidence instruction with explicit criteria: report bugs, security issues and broken error handling, and skip style, naming and patterns consistent with nearby code.
D. Limit the bot to the five findings it ranks as most important.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Specific categorical criteria for what to report and what to skip improve precision far more than confidence-based instructions. They tell the model exactly where the line is.

- **A** is wrong because "be conservative" is the kind of general instruction that does little for precision.
- **B** is wrong because self-rated confidence is another form of confidence filtering, and the model's own ratings are not a reliable gate.
- **D** is wrong because an arbitrary cap may drop real bugs while keeping nits, and it does not define what counts as an issue.

</details>

### Item 2

A code review system has five finding categories. Developers dismiss about 80% of the "outdated comment" findings, and the team notices they have started dismissing the security findings too, which are usually correct. The team wants to restore trust now and fix the noisy category properly over the next few weeks. Which two actions fit best? (Choose two.)

A. Temporarily disable the "outdated comment" category while its prompt is improved.
B. Add "be more careful" to the instructions for every category.
C. Add a `detected_pattern` field to each finding so dismissals can be analysed by the code construct that triggered them.
D. Raise the confidence threshold for all five categories.
E. Require a human to approve every finding before it is posted.

<details>
<summary>Answer and explanation</summary>

**Answer: A and C.** A high false-positive category undermines trust in the accurate ones, so switching it off temporarily restores trust quickly. Recording `detected_pattern` on findings shows which constructs cause dismissals, which is the data needed to fix the category's prompt.

- **B** is wrong because general caution instructions do not meaningfully improve precision.
- **D** is wrong because confidence filtering is not a reliable precision fix and would also suppress good security findings.
- **E** is wrong because reviewing every finding by hand does not scale and does not fix the noisy category.

</details>

### Item 3

A platform team uses Claude to generate database migration scripts. After generating each script, the same session is asked to "review the migration critically and list any problems." The review almost never finds anything, yet production incidents keep tracing back to subtle ordering bugs in these scripts. What change is most likely to catch more issues?

A. Turn on extended thinking for the review step in the same session.
B. Have a second, independent Claude instance review each script without the generating session's reasoning in context.
C. Add "be extremely critical, assume there are bugs" to the review instruction.
D. Ask the generating session to review the script twice.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** A model reviewing its own output in the same session keeps the reasoning that led to its choices and is less likely to question them. An independent instance without that context is more effective at catching subtle issues than self-review prompts or extended thinking.

- **A** is wrong because extended thinking in the same session still carries the generator's reasoning.
- **C** is wrong because a stronger instruction does not remove the shared context that blinds the reviewer.
- **D** is wrong because repeating a biased review repeats the bias.

</details>

### Item 4

An IT helpdesk agent chooses between `reset_password`, `unlock_account` and `escalate_access_request`. The system prompt already explains each tool in detail. Ambiguous requests like "I can't get into the finance portal since this morning" are still routed inconsistently: sometimes a reset, sometimes an unlock, sometimes an escalation. What is the most effective next step?

A. Add another paragraph of rules describing the difference between the three tools.
B. Merge the three tools into one `fix_access` tool.
C. Move tool selection into a keyword-based router in code.
D. Add 2 to 4 few-shot examples of ambiguous requests that show which tool was chosen and why it beat the plausible alternatives.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** When detailed instructions still give inconsistent results on ambiguous cases, a few targeted examples that show the reasoning behind each choice are the most effective technique. They also help the model generalize to ambiguous requests the examples did not cover.

- **A** is wrong because more instruction text is what is already failing to produce consistent choices.
- **B** is wrong because merging the tools hides the decision inside one vague contract instead of improving it.
- **C** is wrong because keyword routing handles ambiguous phrasing poorly and discards the model's understanding.

</details>

### Item 5

A procurement platform extracts supplier certificates into a schema. Every field is required, and the `certificate_type` field is an enum of six values. Two problems appear: when a certificate has no expiry date, the model fills in a plausible one, and new certificate types that are not in the enum get forced into the closest wrong value. Which two schema changes address these problems? (Choose two.)

A. Keep `expiry_date` required and add a prompt line saying "never guess dates".
B. Make `expiry_date` optional and nullable so the model can return null when the certificate has no expiry.
C. Turn `certificate_type` into a free-text string with no enum.
D. Add the twenty most likely future certificate types to the enum now.
E. Add an `"other"` value to the `certificate_type` enum, paired with a detail string field describing the actual type.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** A nullable field lets the model report that information is absent instead of fabricating a value to satisfy a required field. An `"other"` enum value with a detail string keeps the category structured while handling types the enum does not list.

- **A** is wrong because a required field still pressures the model to produce a value, and the prompt line is only guidance against that pressure.
- **C** is wrong because free text loses the consistent categories downstream systems rely on.
- **D** is wrong because no list anticipates every new type, so the same problem returns with the next one.

</details>

### Item 6

An accounts payable inbox receives invoices, credit notes and purchase orders, mixed together, with no reliable label on which is which. The team defined one extraction tool per document type. With `tool_choice` set to `"auto"`, about 5% of responses come back as a prose description of the document instead of a tool call, and the pipeline fails on them. What setting fits best?

A. Set `tool_choice` to `"any"` so Claude must call one of the extraction tools and can still choose which.
B. Force `tool_choice` to the invoice extraction tool, since invoices are the most common type.
C. Keep `"auto"` and add "always respond with a tool call" to the system prompt.
D. Collapse the three tools into one schema where every field from every document type is optional.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** `"any"` guarantees that Claude calls a tool rather than answering in text, while leaving it free to pick the schema that matches the document. That fits an inbox where the type is unknown in advance.

- **B** is wrong because forcing one tool would push credit notes and purchase orders into the invoice schema.
- **C** is wrong because `"auto"` still permits text replies. The instruction only lowers the odds.
- **D** is wrong because a single all-optional schema loses the clear per-type contracts and makes missing-field errors harder to catch.

</details>

### Item 7

An expense platform extracts receipts using a tool with a strict JSON schema. The output always parses, but finance finds receipts where the line items do not add up to the stated total, usually because a discount line was dropped or a value landed in the wrong field. The team wants to catch and correct these automatically. Which two changes fit best? (Choose two.)

A. Have the schema capture a `calculated_total` (sum of extracted line items) alongside the `stated_total`, and flag records where they differ.
B. Switch from tool use to asking for raw JSON in the text response.
C. Make the schema stricter by tightening every field's type.
D. When validation fails, send a follow-up request containing the original receipt, the failed extraction and the specific validation error.
E. Resend the identical request whenever totals disagree, and keep the result that matches.

<details>
<summary>Answer and explanation</summary>

**Answer: A and D.** Strict schemas remove syntax errors but not semantic ones like totals that do not sum. A `calculated_total` beside `stated_total` detects the discrepancy, and retrying with the document, the failed extraction and the specific error gives the model what it needs to correct itself.

- **B** is wrong because leaving tool use brings back syntax errors and does nothing for semantic ones.
- **C** is wrong because tighter types still allow numbers that are valid individually but do not add up.
- **E** is wrong because an identical retry gives the model no information about what went wrong.

</details>

### Item 8

A legal operations team extracts the renewal date from each customer contract. For about 10% of contracts, validation keeps failing even after three retries with specific error feedback. Reviewing them, the team finds that these contracts say "renewal terms as set out in Amendment 2", and the amendments are stored in a separate system that the pipeline does not query. What should the team conclude?

A. Increase the retry limit to six, since some will succeed eventually.
B. Add few-shot examples of renewal date extraction to the prompt.
C. Retries cannot help because the date is not in the provided document. Supply the referenced amendment or route these contracts for human handling.
D. Make the renewal date required so the model must produce a value.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Retry with error feedback fixes format and structure problems. It cannot produce information that is absent from the input. The fix is to supply the missing amendment, or route the case to a person.

- **A** is wrong because more retries on missing information only cost more and may encourage a fabricated date.
- **B** is wrong because examples improve extraction of information that is present. They do not supply what is missing.
- **D** is wrong because forcing a value when none exists in the input invites fabrication.

</details>

### Item 9

A compliance team processes regulatory filings with the Message Batches API to save cost. Each filing must have results within 28 hours of arriving. Batches can take up to 24 hours to process. Filings arrive steadily all day. How often should the team submit a batch of newly arrived filings to guarantee the 28-hour SLA?

A. Once a day.
B. Every 12 hours.
C. Every 6 hours.
D. Every 3 hours.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** In the worst case, a filing arrives just after a submission, waits a full interval, then takes up to 24 hours to process. The interval plus 24 hours must be at most 28 hours, so the interval can be no more than 4 hours. Of the options, only every 3 hours meets that.

- **A** is wrong because the worst case is 24 hours of waiting plus 24 hours of processing, 48 hours in total.
- **B** is wrong because the worst case is 12 plus 24, 36 hours.
- **C** is wrong because the worst case is 6 plus 24, 30 hours, which still misses the 28-hour SLA.

</details>

### Item 10

A research firm submits a nightly Message Batches job of 5,000 document summaries. Last night, 5,000 results came back, and 300 of them were errors because the documents exceeded the context limit. The other 4,700 succeeded. What is the best way to handle the failures?

A. Resubmit the full batch of 5,000 with a shorter prompt.
B. Identify the failed requests by `custom_id`, split those documents into chunks, and resubmit only them.
C. Switch the whole job to the synchronous API so failures can be handled one by one.
D. Drop the 300 documents from the report and note them as unavailable.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** `custom_id` correlates each request with its result, so you can resubmit only what failed. Chunking addresses the actual cause, documents too large for the context.

- **A** is wrong because it repeats 4,700 successful requests and a shorter prompt does not shrink the oversized documents.
- **C** is wrong because a nightly, latency-tolerant job is a good fit for batches. Switching gives up the savings without fixing the size problem.
- **D** is wrong because the failures are recoverable. Silently dropping them leaves the report incomplete for no reason.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect), which reports percent correct by domain.

---

---

---

<!-- nav:bottom -->← Previous: [Domain 3: Claude Code Configuration & Workflows](03-claude-code-configuration-and-workflows.md) | Next: [Domain 5: Context Management & Reliability](05-context-management-and-reliability.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
