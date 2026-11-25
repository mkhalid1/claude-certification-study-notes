<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 5: Model Selection and Optimization](05-model-selection-and-optimization.md) · Next: [Domain 7: Security and Safety](07-security-and-safety.md)
<!-- /nav:top -->

# Domain 6: Prompt and Context Engineering

Weight: 15% of scored items, about 9 items.

## What this domain measures

This domain tests whether you can control what Claude does through the words and data you give it, and keep that control as sessions grow and inputs get messy. The guide groups it into four skills:

- **Context management for long-running sessions.** Spotting context bloat or drift in a long session or agent, and choosing the technique that restores the intended behaviour: compaction, pruning tool output, isolating work in subagents, or deciding when to start a fresh session.
- **Prompt revision.** Changing a prompt to close a gap that regression evaluations have exposed between the output you want and the output you get. The tools are clearer instructions, putting content in the system or user turn as fits, zero-, one-, or multi-shot examples, output constraints, and sanitising inputs. The goal is to close the gap and keep it closed.
- **Structured output and defensive parsing.** Using structured outputs with strict schema validation, validating responses, and parsing free-text fallbacks defensively, so downstream code is protected from malformed or unexpected output.
- **Grounding and source citation.** Building a request that answers only from the supplied documents, cites them, and says "not found" when they do not contain the answer. That includes deciding, from corpus size, cost, and freshness, whether to put the whole document set in context or retrieve only the relevant passages.

Why it matters: most quality problems in Claude applications are solved here, without changing the model. And most production crashes downstream of Claude come from code that trusted output it should have validated.

## How questions look

Prompt items show a prompt, a few failing outputs from a regression run, and four revisions. Context items describe a session that degrades over time. Parsing items show a crash or bad record downstream and the response that caused it. Grounding items describe a corpus and a requirement such as "cite the page" or "never guess". The right answer is usually the most targeted change that fixes the shown failure and can be checked by the eval.

## A method for these questions

1. Name the gap precisely: wrong format, wrong scope, ignored rule, made-up content, lost instruction.
2. Pick the matching tool. Unclear or conflicting rules need clearer instructions. A format the model gets wrong on edge cases needs varied examples. Length or content limits need explicit output constraints. Untrusted text that steers the model needs separation and delimiting. Machine-read output needs a schema.
3. For long sessions, ask what is filling the context and whether it is still needed. Old tool output can be pruned, long history can be compacted, bulky side work can go to a subagent, and a new task may deserve a fresh session.
4. For parsing, assume any response can be truncated, refused, or malformed. Check the stop reason and validate before use.
5. For grounding, check that the documents are actually in the request, that citations are requested, and that a "not found" path exists. Then choose full context or retrieval from size, cost, and freshness.
6. Prefer the option that comes with a way to keep the fix in place, such as adding the failing cases to the regression set.

## Traps that cost points

- **Mixing instructions and untrusted input in one block.** User or document text then competes with your rules. Keep operator instructions in the system prompt and wrap untrusted input in clearly labelled delimiters.
- **One example for a varied task.** A single example invites copying its surface details. Use a few varied examples that cover the edge cases.
- **Shouting instead of specifying.** Capital letters and "IMPORTANT" are weaker than a clear rule with the reason and the boundary.
- **Trusting structured output blindly.** A response can still stop at `max_tokens` or be a refusal. Check the stop reason and validate against the schema before downstream use.
- **Restating every instruction each turn in a long session.** That adds to the bloat that causes drift. Prune and compact instead.
- **Asking for citations without supplying the sources in a citable form.** Put the documents in the request, ideally as document blocks with citations enabled.
- **Putting a huge, fast-changing corpus in context.** Size, cost, and freshness all point to retrieval. A small, stable set can go in context with caching.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 6 is about 9 items, so budget roughly 15 minutes. Prompt-revision items often include long prompts. Read the failing outputs first, decide what kind of gap it is, then check which revision targets that gap.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| System vs user placement | Stable operator instructions and role go in the system prompt. Per-request data and the user's request go in the user turn. |
| Input sanitising | Delimit untrusted text clearly (for example in labelled XML-style tags), say it is data to analyse, and strip or neutralise content that should never reach the model. |
| Examples | Zero-shot for clear tasks. One or more varied examples when format or edge-case judgement is wrong. Examples should cover the hard cases, not just the easy ones. |
| Output constraints | State length, format, allowed content, and what to do when unsure. Constraints should be testable by the eval. |
| Keeping a fix closed | Add the failing cases to the regression set and run it on every prompt change. |
| Structured outputs | Constrain responses to a JSON schema (`output_config.format`), or use strict tool definitions for tool inputs. Prefer this to parsing free text. |
| Defensive parsing | Check `stop_reason` first. Validate against the schema. On failure, retry with the error, use a safe default, or route for review. Never pass unvalidated output downstream. |
| Citations | Supply sources as `document` blocks with citations enabled, and the response carries citations pointing to the cited text. |
| "Not found" path | Tell Claude to answer only from the documents and to say it cannot find the answer when they do not contain it. Test that path. |
| Full context vs retrieval | Small, stable corpus: full context, cached. Large, costly, or fast-changing corpus: retrieve relevant passages per question. |
| Compaction | Summarise older history into a shorter form that keeps decisions, constraints, and open tasks. |
| Tool-output pruning | Clear old tool results that are no longer needed, and shape new ones to be small. |
| Fresh session | Start over with a short handover summary when the task changes or the context is full of irrelevant history. |

## Practice items

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A ticket classifier sorts messages into five queues with a zero-shot prompt. A regression run shows it handles clear messages well but misroutes messages that mention two topics, and messages that are mainly complaints but contain a billing question. The team has labelled examples of each tricky pattern. Which revision is most likely to close the gap?

A. Add one example showing a simple billing message, and tell the model to copy its format exactly.

B. Remove the queue descriptions so that the model relies on its own understanding of each queue.

C. Add a few varied examples that cover the tricky patterns, such as two-topic messages and complaints containing billing questions, each with the correct queue and a one-line reason.

D. Ask for a longer answer that explains every queue before choosing one.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The model fails on specific edge patterns, and the team has labelled examples of them. A few varied examples that show those patterns and the reasoning behind the choice teach the decision boundary directly. Varied examples also avoid the copying problem a single example causes.

- **A** is wrong because one easy example does not cover the edge patterns and invites surface copying.
- **B** is wrong because removing the queue descriptions takes away guidance the model needs.
- **D** is wrong because longer answers add cost and do not teach the boundary between queues.

</details>

### Item 2

A legacy integration cannot use structured outputs, so Claude returns free text that is supposed to contain a JSON object, and the code calls a JSON parser on the whole response. When parsing fails, the code logs the error and passes an empty object downstream, where a billing step treats the empty fields as zero. Finance has found invoices issued for zero. What is the best defensive design?

A. Keep passing empty objects but add a note in the logs so someone can fix the invoices later.

B. Extract the JSON object from the response, validate it against the expected schema, and on failure retry once with the validation error in the request, then send it for review if it still fails. Never send unvalidated data downstream.

C. Ask the model to return the JSON twice in each response, and parse whichever copy succeeds.

D. Increase the size of the prompt with five more format reminders and keep the current error handling.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Defensive parsing means pulling out the expected object, validating it, and having a safe failure path. Retrying with the specific validation error often fixes the output, and review catches the rest. The crucial change is that a failed parse never turns into a fake valid record downstream.

- **A** is wrong because it keeps issuing bad invoices and only records the damage.
- **C** is wrong because two copies double the output and do not validate either one.
- **D** is wrong because reminders may reduce failures but leave the dangerous fallback in place.

</details>

### Item 3

A developer used one long session to build and debug an authentication module. The module is finished and merged. He now wants the assistant to design an unrelated reporting dashboard. The current context is full of authentication code, stack traces, and debugging notes, and in a quick test the assistant suggested reusing auth helpers that have nothing to do with reporting. What should he do?

A. Keep working in the same session and add a note that authentication is finished.

B. Start a fresh session, carrying over only a short note of what is relevant, such as project conventions, and leave the authentication history behind.

C. Compact the session so the authentication history is summarised, then continue with the dashboard in the same context.

D. Ask the assistant to ignore everything above the current message.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The task has changed completely, and nearly all of the context is irrelevant to it. That is a clear signal to start fresh. A short handover note keeps the few things that carry over without the noise that is already steering the suggestions.

- **A** is wrong because the irrelevant history stays in context and keeps influencing the work.
- **C** is wrong because a summary still carries authentication material into an unrelated task. Compaction suits continuing the same task.
- **D** is wrong because the old context is still sent and can still affect answers.

</details>

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 5: Model Selection and Optimization](05-model-selection-and-optimization.md) | Next: [Domain 7: Security and Safety](07-security-and-safety.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
