<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 1: Agents and Workflows](01-agents-and-workflows.md) · Next: [Domain 3: Claude Code](03-claude-code.md)
<!-- /nav:top -->

# Domain 2: Applications and Integration

Weight: 20% of scored items, about 12 items.

## What this domain measures

This is the largest domain on the December 7 version. It tests whether you can wire Claude into real application code correctly and keep it working when things go wrong. The guide groups it into four skills:

- **Messages API mechanics.** Building requests and reading responses: message structure and roles, tool use, streaming, vision, thinking, prompt caching, structured output, and stop reasons. It also covers choosing realtime requests or batch processing for a workload.
- **Application boundary and surface behaviour.** Judging how Claude reads instructions on different surfaces, what data actually leaves your application and reaches the model, and whether your schema and session design meet the stated requirements.
- **Configuration and SDLC integration.** Working out the effective configuration from the artifacts in front of you: CLAUDE.md files, settings.json, model version pinning, prompt versioning, plugin dependencies, and CI. You also need to predict how one change affects each environment.
- **Integration resilience.** Reading failing code and its error output, finding the defect, and fixing it in line with the documented API error semantics. The guide names three defect families: streaming events and terminal-event handling, retry and fallback logic that treats all errors alike, and broken multi-format ingestion through the Files API.

Why it matters: most production incidents with LLM features are integration bugs, not model bugs. A truncated answer shown as complete or a retry storm on a bad request hurts users more than a slightly weaker prompt.

## How questions look

Expect a short block of handler code, a request body, an error payload, or a config snippet, plus a symptom ("users see half answers", "the job never finishes", "staging behaves differently from production"). The options are usually four plausible engineering changes. One fixes the defect the evidence points to. The others work around the symptom: raise a limit, switch off a feature, retry harder, or add a new component. Some items ask you to pick two changes that together meet a requirement.

## A method for these questions

1. Find the evidence first. Read the error type, status code, event name, or stop reason in the stem before reading any option.
2. Name the contract that was broken. Examples: every `tool_use` needs a matching `tool_result`; the API is stateless; `message_stop` ends a stream; a 400 is not transient.
3. Decide whether the failure is deterministic or transient. Deterministic failures need a code or request fix. Transient ones (rate limits, overload, server errors) need backoff and retry.
4. Prefer the option that fixes the contract at its source over one that hides the symptom.
5. Check the option against the stated constraint (latency, cost, privacy, audit). Reject options that break it, even if they would "work".
6. For "Choose two" items, check that the two picks are independent and that together they fully satisfy the requirement.

## Traps that cost points

- **Retrying everything.** A 400 `invalid_request_error` will fail the same way every time. Retrying it burns quota and hides the bug. Retry rate limits, overload, and server errors; fix invalid requests.
- **Raising `max_tokens` to "fix" truncation.** A larger limit makes truncation rarer but does not detect it. The fix is to read the stop reason and treat `max_tokens` as incomplete.
- **Assuming the API remembers the conversation.** The Messages API is stateless. If you do not send the earlier turns, Claude does not see them.
- **Dropping or reordering tool results.** Each `tool_use` block in the assistant turn needs a `tool_result` with its id in the next user message. If Claude made several calls, return all the results together in one user message.
- **Treating an HTTP 200 stream as success.** A streamed response can still carry an error event after it starts. Handle stream error events as failures.
- **Choosing realtime for work nobody is waiting on.** Large offline jobs fit batch processing, which is asynchronous and cheaper. Realtime calls fit work a user is waiting for.
- **Sending more data than the task needs.** Whatever you put in the request crosses the application boundary. Minimise and redact before sending, not after.
- **Floating model aliases in production.** An alias can move to a new model version. Pin an explicit model identifier per environment and promote changes through a regression eval.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 2 is about 12 items, so budget roughly 20 minutes. Code-reading items take longer. Read the symptom and the error output first, then scan the code for the one line that breaks the contract. If an item needs more than about 3 minutes, mark it and move on.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Stateless API | Every request carries the full conversation you want Claude to see. Session memory is your application's job. |
| Stream event order | `message_start`, then per block `content_block_start`, `content_block_delta` events, `content_block_stop`, then `message_delta` (carries `stop_reason` and usage), then `message_stop`. `content_block_stop` ends one block, not the message. |
| Stop reasons | `end_turn` finished naturally; `max_tokens` hit the output limit; `stop_sequence` hit one of your stop sequences; `tool_use` wants a tool run; `pause_turn` paused a long server-side turn that you can continue; `refusal` declined. Check it before trusting content. |
| Error classes | 400 `invalid_request_error`, 401 `authentication_error`, 403 `permission_error`, 404 `not_found_error`, 413 `request_too_large`, 429 `rate_limit_error`, 500 `api_error`, 529 `overloaded_error`. |
| What to retry | 429, 500, and 529, with exponential backoff and jitter, honouring the `retry-after` header on rate limits. Do not retry 400, 401, 403, or 404 without changing something. |
| Tool use round trip | `stop_reason: "tool_use"`, run the tools, send a user message containing a `tool_result` for every `tool_use` id. Use `is_error: true` for a failed tool instead of dropping the result. |
| Thinking with tools | Pass the assistant content back unchanged, thinking blocks included, when you continue a tool loop. |
| Files API | Upload once, then reference by `file_id`. The content block type must match the file: `document` for PDF or plain text, `image` for images. |
| Batch processing | Message Batches API: asynchronous, lower cost, results may take up to 24 hours and arrive in any order, so match them by `custom_id`. |
| Structured outputs | Constrain the response to a JSON schema, and use strict tool definitions when tool inputs must match the schema exactly. Still check the stop reason. |
| Versioning | Pin model identifiers and keep prompts as versioned artifacts, so every environment's behaviour is traceable and reversible. |

## Practice items

These 4 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A claims-processing service wraps every Messages API call in a retry helper that retries any non-2xx response five times with a fixed one-second delay. During an incident review, logs show thousands of retries for requests that returned HTTP 400 with `invalid_request_error`, caused by a malformed `messages` array after a refactor. The same logs show that genuine 429 responses also exhausted their retries quickly during traffic peaks. Which change best fixes the retry logic?

A. Increase the retry count to ten and the delay to five seconds for all error responses, so that both failure types have more time to clear.

B. Catch every API exception in a single handler and return a friendly fallback message to the user without retrying anything.

C. Classify errors by type: fail fast on 400-class request errors and surface them, and retry 429, 500, and 529 with exponential backoff and jitter, honouring `retry-after` where present.

D. Switch the service to the Message Batches API so that failed requests are retried by the batch system automatically.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** An `invalid_request_error` is deterministic: the same malformed request fails every time, so retrying only wastes quota and delays the alert. Rate limits, server errors, and overload are transient and should be retried with growing, jittered delays that respect `retry-after`. Treating the two classes differently fixes both symptoms in the logs.

- **A** is wrong because more retries make the 400 storm worse and still do not separate deterministic from transient failures.
- **B** is wrong because it gives up on transient errors that would have succeeded and hides the malformed-request bug.
- **D** is wrong because batch processing changes the delivery model, not the defect. A malformed request fails in a batch too, and claims processing may need realtime answers.

</details>

### Item 2

A platform team runs a Claude-powered contract summariser in development, staging, and production. Last month a production incident was traced to a prompt that someone edited directly in the production config, and to a model alias that moved to a newer version without anyone testing it. The team now wants every behaviour change to be traceable to a commit and easy to roll back, with staging always proving a change before production gets it. Choose two.

A. Let the on-call engineer edit the production prompt in place during incidents, and copy the change back into the repository afterwards.

B. Store prompts as versioned files in the repository and have each environment's config reference a specific prompt version.

C. Point every environment at the newest model alias so that all three always run the same model.

D. Paste the prompt text inline in each service that calls Claude so that every service carries its own copy.

E. Pin an explicit model identifier in each environment's config and promote a new model to production only after it passes the regression eval in staging.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** Versioned prompt files tie every prompt change to a commit and make rollback a config change. Pinned model identifiers stop a model from changing underneath you, and promoting through staging with a regression eval proves a change before production sees it. Together they cover both causes of the incident.

- **A** is wrong because editing production in place is exactly what caused the untraceable change.
- **C** is wrong because a moving alias lets the model change without a test, which was the other cause of the incident.
- **D** is wrong because inline copies drift apart and are harder to version, review, and roll back.

</details>

### Item 3

A developer builds a recipe assistant on the Messages API. Each time the user types something, the app sends a request containing the system prompt and only the user's newest message. Users complain that when they say "make it vegetarian", the assistant asks which recipe they mean, even though it suggested one a moment ago. Logs show no errors. What is the defect?

A. The system prompt is too short, so Claude cannot keep track of earlier recipes without more detailed instructions.

B. The model's context window is too small for a multi-turn conversation, so the app should move to a larger model.

C. The API is stateless, so the app must send the earlier user and assistant turns with each request for Claude to see them.

D. The app should enable prompt caching, which stores the conversation on the server between requests.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The Messages API does not keep conversation state between requests. Claude sees only what is in the request, and this app sends only the latest message, so the earlier recipe is simply missing. The fix is to keep the history in the app and send it each turn, managing its length as it grows.

- **A** is wrong because no system prompt can recover a turn that was never sent.
- **B** is wrong because a two-turn conversation is nowhere near the context limit, and the earlier turn is not being sent at all.
- **D** is wrong because prompt caching reuses a prefix you send. It does not store a conversation you leave out.

</details>

### Item 4

An internal research agent uses adaptive thinking and calls a `query_warehouse` tool. To save tokens, the developer's loop keeps only the `text` and `tool_use` blocks from each assistant turn, discarding the thinking blocks, before appending the turn to history and sending the `tool_result`. Since that change, the API rejects some continuation requests and the agent's multi-step reasoning has become noticeably worse. Which change is correct?

A. Turn off thinking for every request that includes tools, so that there are no thinking blocks to manage.

B. Append the assistant turn's content unchanged, thinking blocks included, when sending the tool results back.

C. Rewrite the thinking blocks as a short text summary and add it to the system prompt before each continuation.

D. Move the tool results into the system prompt so that the assistant turn no longer needs to be sent back.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** When thinking is used with tools, the assistant turn must be passed back as it was returned, including its thinking blocks, so the model can continue its reasoning across the tool call. Stripping or editing those blocks breaks the continuation, which matches both symptoms in the stem.

- **A** is wrong because it gives up the reasoning the agent needs. On current models, thinking cannot always be switched off.
- **C** is wrong because a rewritten summary is not the original block, and moving it to the system prompt still leaves the assistant turn altered.
- **D** is wrong because tool results belong in `tool_result` blocks answering specific `tool_use` ids. The system prompt is not part of the tool-use protocol.

</details>

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 1: Agents and Workflows](01-agents-and-workflows.md) | Next: [Domain 3: Claude Code](03-claude-code.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
