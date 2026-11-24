<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md) · Next: [Domain 6: Prompt and Context Engineering](06-prompt-and-context-engineering.md)<!-- /nav:top -->

# Domain 5: Model Selection and Optimization

Weight: 12% of scored items, about 7 items.

## What this domain measures

This domain tests whether you can explain how generation behaves, pick the right model for a job, and bring cost and latency down without hurting quality. The guide groups it into three skills:

- **Generation mechanics and model options.** Using what you know about tokens, context windows, sampling and non-determinism, and model options such as adaptive thinking and effort levels to predict, explain, and control what you observe.
- **Model tier selection and release planning.** Weighing model tiers on quality, latency, and cost against the task and its binding constraint, justifying the choice, and planning for behaviour changes across model releases with regression evaluations.
- **Cost diagnosis and optimization.** Reading usage or cost data to find the biggest cost driver, choosing the optimization that targets it (prompt caching with single, layered, and rolling breakpoints and careful checkpoint placement, batch processing, or token budgets), and estimating the effect.

Why it matters: the same feature can cost many times more than it needs to, or miss its latency target, because of one misplaced cache breakpoint or the wrong tier. Fixing the dominant driver beats ten small tweaks.

## How questions look

Expect a usage report or a description of traffic: input and output token counts, cache read and write figures, request timing, latency targets, and a quality bar. The question asks which change gives the biggest saving without changing behaviour, or which tier and setting fit a constraint. Distractors are often real optimizations aimed at the wrong driver, or changes that save money by quietly lowering quality.

## A method for these questions

1. Find the binding constraint: a latency target, a quality bar, a budget, or "no behaviour change".
2. For cost items, find the dominant driver in the data. Is it uncached input, output tokens, request volume, or the tier price?
3. Match the lever to the driver. Repeated input prefixes point to prompt caching. Work nobody is waiting on points to batch. Long outputs point to output constraints and effort. A tier that is more capable than the task needs points to a smaller tier, if the eval agrees.
4. For caching, check the order and stability of the prefix. Anything that changes per request must come after the last breakpoint, and the prefix must be byte-identical between requests.
5. For tier and release choices, insist on evidence from a regression eval, not intuition.
6. Reject options that change what the model sees or does when the stem says behaviour must not change.

## Traps that cost points

- **Putting a timestamp or request id at the top of a cached prompt.** Caching matches an exact prefix. One changing byte early on means every request misses.
- **Caching only the system prompt in a long agent loop.** In a growing conversation, the history is most of the input. A rolling breakpoint at the end of the latest turn lets each request reuse the previous prefix.
- **Picking a longer cache lifetime for entries that never match.** Lifetime helps only when the prefix is identical and requests return after the default lifetime has passed.
- **Downgrading the tier to cut cost when the stem says quality must hold.** A smaller tier is a valid choice only when the regression eval shows it meets the bar.
- **Expecting identical output from identical input.** Generation samples tokens, so wording varies. If you need the same answer every time, store and reuse it.
- **Upgrading to a new model version without a regression run.** New releases can change behaviour. Test against the golden set before promotion.
- **Raising effort everywhere.** Higher effort costs more tokens and time. Raise it on the routes where measurement shows it helps.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 5 is about 7 items, so budget roughly 12 minutes. Cost items can look number-heavy. You rarely need to calculate exactly: find which line in the usage data is biggest and which option attacks that line.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Tokens | Input and output are billed separately, and output usually costs more per token. Count tokens with the token-counting endpoint, not by guessing. |
| Context window | The maximum tokens one request can hold, input plus output. Exceeding it is a request error. |
| Non-determinism | Output is sampled, so repeated runs vary. Do not rely on identical output. Store results that must not change. |
| Adaptive thinking | The model decides when and how much to think. On current models it is the way to use thinking. |
| Effort | `output_config.effort` from `low` to `max` trades thoroughness against tokens and latency. Tune it per route by measurement. |
| Tier choice | Choose the smallest tier that passes your eval within the latency and cost limits. Justify it with data. |
| Release planning | Pin the model version, run the regression eval on the new release, review differences, then promote. |
| Prompt caching | Exact prefix match in the order tools, then system, then messages. Up to 4 breakpoints per request. Below a model-specific minimum length, nothing is cached. |
| Breakpoint patterns | Single: after the stable prefix. Layered: one breakpoint per block that changes on a different schedule. Rolling: on the latest turn, so a growing conversation reuses its earlier prefix. |
| Cache lifetime | Default is 5 minutes, refreshed on each read. A 1-hour option exists at a higher write cost, for prefixes reused with longer gaps. |
| Cache usage fields | `cache_creation_input_tokens` (written), `cache_read_input_tokens` (reused), `input_tokens` (uncached). Zero reads across repeated requests means the prefix is changing. |
| Batch processing | Asynchronous and cheaper than realtime, for work nobody is waiting on. Can be combined with caching. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A coding agent runs long sessions of 60 to 100 turns. Its request has a large, fixed system prompt with a cache breakpoint at the end of it, followed by the growing conversation. Usage data shows cache reads covering the system prompt on every request, but uncached input tokens grow with every turn and now dominate the bill late in sessions. The team wants to cut cost without changing what the model sees. Which change fits best?

A. Remove the breakpoint from the system prompt and put a single breakpoint on the first user message instead.

B. Shorten the system prompt by half so that each request carries fewer tokens.

C. Add a rolling breakpoint on the latest turn (or use automatic caching), so each request reuses the conversation prefix cached by the previous request.

D. Switch to a smaller model tier, since the conversation history is billed at the same rate whichever tier reads it.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** In a long session, the conversation history is most of the input, and it is re-sent each turn. A breakpoint that moves to the end of the latest turn caches that growing prefix, so the next request reads it from cache and pays full price only for the new turn. The system prompt breakpoint can stay as well.

- **A** is wrong because it drops the reliable system prompt cache and still leaves the growing history uncached.
- **B** is wrong because the system prompt is already cached, so shortening it saves little and may change behaviour.
- **D** is wrong because a different tier changes quality and behaviour, which the team ruled out, and it ignores the actual driver.

</details>

### Item 2

A support summarisation service caches a long system prompt with tool definitions. Usage logs show `cache_creation_input_tokens` high on every request and `cache_read_input_tokens` at zero, even for requests seconds apart. Reviewing the code, the team finds that the first line of the system prompt is "Current time: " followed by the exact time of the request, which the model uses to judge ticket age. What should they change?

A. Move the current time out of the cached prefix, placing it after the last cache breakpoint, for example in the user turn, so the cached content stays identical.

B. Switch the cache to the 1-hour lifetime so that entries survive longer between requests.

C. Add three more cache breakpoints inside the system prompt so that at least one of them matches.

D. Remove prompt caching, since it cannot work for prompts that include time information.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Caching matches an exact prefix, and the timestamp makes the very start of the prompt different on every request. Moving that value after the last breakpoint keeps the expensive prefix identical, so it can be read from cache. The model still gets the time.

- **B** is wrong because a longer lifetime cannot help entries that are never matched.
- **C** is wrong because every breakpoint comes after the changing first line, so none of them would match either.
- **D** is wrong because caching works fine once volatile content is moved after the stable part.

</details>

### Item 3

An email client adds an inline "suggest a reply" feature. Suggestions must appear within about a second at the 95th percentile, traffic is millions of requests a day, and each request is short and simple. A regression eval on 1,000 real emails shows that the smallest current model tier, at low effort, meets the product's quality bar. Larger tiers score slightly higher but miss the latency target. Choose two.

A. Use the most capable tier at maximum effort, because the higher quality score justifies the extra cost and time.

B. Choose the smallest tier that passes the eval, since it meets the quality bar and the latency target at the lowest cost.

C. Send the suggestions through the Message Batches API to reduce cost on the high volume.

D. Set a low effort level for this route, because the eval showed quality holds there and it keeps responses fast and cheap.

E. Turn on the longest available reasoning for every suggestion so that the replies are as considered as possible.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** The binding constraint is latency, and the eval shows the smallest tier at low effort meets the quality bar. Choosing that tier and that effort meets every requirement at the lowest cost. The choice is justified by measurement, not by preference.

- **A** is wrong because the larger tiers miss the latency target, which is binding.
- **C** is wrong because batch results can take hours, and these users need a suggestion in about a second.
- **E** is wrong because more reasoning adds time and tokens to a simple task that already meets its bar.

</details>

### Item 4

A healthcare documentation team runs a summarisation feature on a pinned model version. A new model version is released, and a teammate proposes switching production to it today, since it scores better on public benchmarks. The team's golden set of 300 labelled clinical notes has never been run against the new version, and the summaries feed a regulated workflow. What is the right plan?

A. Switch production today and watch user complaints for a week before deciding whether to keep it.

B. Switch to the model alias that always points to the newest version, so that future upgrades happen automatically.

C. Stay on the current version indefinitely, because any new model might change behaviour.

D. Run the golden set against the new version in staging, compare results and review any changed behaviour, adjust prompts if needed, then promote by updating the pinned version.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** New releases can change behaviour in ways benchmarks do not show. For a regulated workflow, the team's own golden set is the evidence that counts. Running it in staging, reviewing differences, and promoting by changing the pinned version keeps the upgrade controlled and reversible.

- **A** is wrong because using production users as the test puts a regulated workflow at risk.
- **B** is wrong because an auto-moving alias removes the control the team needs.
- **C** is wrong because refusing all upgrades gives up improvements. The answer is to test, not freeze.

</details>

### Item 5

A marketing team generates product descriptions and notices that the same input produces differently worded text each time. Legal has approved one description per product and requires that the approved text be shown every time a product page loads. A developer suggests passing a fixed random seed in each request so the model always generates the same words. What is the right approach?

A. Pass a fixed seed in the request, as the developer suggests, so that generation becomes repeatable.

B. Generate once, store the approved description, and serve the stored text on each page load rather than generating it again.

C. Raise the effort level so that the model settles on one best wording.

D. Shorten the prompt so that there is less room for variation in the output.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Generation is sampled, so you should not expect identical wording from repeated calls. When a specific approved text must appear every time, the reliable design is to store it and serve it. That also saves the cost of generating it again.

- **A** is wrong because the Messages API does not offer a seed parameter that makes output repeatable.
- **C** is wrong because effort changes how thoroughly the model works, not whether its output repeats word for word.
- **D** is wrong because a shorter prompt does not remove sampling variation.

</details>

### Item 6

A research assistant's monthly cost has doubled. The usage breakdown shows input tokens are modest and mostly cached, but output tokens make up about 80% of spend. Reviewing outputs, the team sees long preambles, repeated restatements of the question, and multi-page answers where users asked for a short list. The quality team says users prefer shorter answers. Which change targets the dominant cost driver?

A. Constrain output: specify the expected length and format in the prompt, for example a list of at most five items with one line each, set `max_tokens` to fit, and test whether a lower effort level holds quality.

B. Add more cache breakpoints to the system prompt so that more input is read from cache.

C. Move the workload to the Message Batches API, since output tokens are not billed in batches.

D. Remove the conversation history from each request so that the model has less to respond to.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The data says output tokens are the driver, and users want shorter answers anyway. Telling the model the length and format you want, and sizing `max_tokens` to that, cuts output directly. Lower effort can trim further, but only if the eval shows quality holds.

- **B** is wrong because input is already mostly cached and is not the driver.
- **C** is wrong because batches lower the price of a request but output is still billed. Batch also does not fix verbosity.
- **D** is wrong because removing history damages answers and targets input, which is not the problem.

</details>

### Item 7

A financial-analysis product has three routes: a quick "define this term" lookup, a news summary, and a multi-step valuation that reasons over several tables. All three run at low effort to save cost. The regression eval shows the first two meet their quality bars, but the valuation route fails a quarter of its cases, with reasoning errors in the intermediate steps. What should the team change?

A. Raise effort on all three routes to the maximum so that every answer gets the deepest reasoning.

B. Leave effort low everywhere and add a sentence asking the model to think harder on valuations.

C. Raise the effort level on the valuation route only, then re-run its eval to confirm the gain is worth the extra tokens.

D. Split each valuation into three separate requests at low effort so that each step is smaller.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Effort is a per-route setting that trades tokens and time for thoroughness. Only the multi-step route is failing, and reasoning-heavy work responds to higher effort. Raising it there and measuring keeps the other routes cheap.

- **A** is wrong because it raises cost and latency on routes that already pass.
- **B** is wrong because effort is the control built for this. A prompt request is a weaker substitute.
- **D** is wrong because splitting without more reasoning may not fix the intermediate errors, and it adds orchestration the team would need to build and test.

</details>

### Item 8

A compliance team runs a nightly job that reviews about 15,000 new transactions. Each request starts with the same 6,000-token block of review rules, followed by one transaction. Results are read the next morning. The cost report shows this job is now the biggest line on the bill, with nearly all of it uncached input tokens on realtime requests. The team wants the largest saving without changing the model's output. Choose two.

A. Run the job through the Message Batches API, since nobody needs the results until morning.

B. Switch the job to a larger model tier so that each review is more thorough.

C. Split each transaction into several smaller requests so that each request uses fewer tokens.

D. Raise `max_tokens` so that each review has room to cover every rule.

E. Put a cache breakpoint after the shared review rules so the repeated block is read from cache instead of billed in full each time.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** The two drivers are realtime pricing for work nobody waits on, and a large identical block sent 15,000 times. Batch processing addresses the first. Caching the shared rules addresses the second. Neither changes what the model sees, so output stays the same.

- **B** is wrong because a larger tier raises cost and changes behaviour.
- **C** is wrong because splitting repeats the rules in even more requests.
- **D** is wrong because `max_tokens` caps output and does nothing about the input cost.

</details>

### Item 9

A team is deciding whether to invest a sprint in prompt caching for a document Q&A service. Before starting, the engineering manager asks for an estimate of the likely saving, based on data the team can pull from existing logs. Which measurement best supports that estimate?

A. The average number of output tokens per response over the last month.

B. The number of distinct users who sent a question last month.

C. The total number of tool calls per request.

D. The share of input tokens that form a stable prefix shared across requests, and how often requests sharing that prefix arrive within the cache lifetime of each other.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Caching saves money on input tokens that repeat as an identical prefix and are reused before the entry expires. Knowing how large that stable share is, and how often it is reused in time, tells you how many tokens move from full price to the cheaper cache-read price.

- **A** is wrong because caching does not reduce output tokens.
- **B** is wrong because user count does not say whether prompts share a prefix or how often.
- **C** is wrong because tool call counts do not measure the repeated input that caching saves.

</details>

### Item 10

An internal knowledge assistant uses a large, stable system prompt with a cache breakpoint after it. Usage is bursty: people ask a question, read the answer, and ask the next one about 15 to 25 minutes later. Logs show that nearly every request writes the cache again instead of reading it, even though the prompt is byte-identical between requests. What change fits best?

A. Remove the cache breakpoint, since bursty traffic cannot use caching.

B. Use the 1-hour cache lifetime on that breakpoint so the entry survives the 15 to 25 minute gaps, accepting its higher write cost.

C. Add a timestamp to the system prompt so that each request is tracked separately in the cache.

D. Move the system prompt into the user turn so that it is cached with the question.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The prefix is stable, so matching is not the problem. Timing is. The default lifetime is about 5 minutes, so with 15 to 25 minute gaps each entry expires before the next request. The 1-hour lifetime costs more per write but keeps the entry alive across the gaps, so most requests become cache reads.

- **A** is wrong because caching works for bursty traffic once the lifetime matches the gaps.
- **C** is wrong because a changing timestamp guarantees misses.
- **D** is wrong because putting the stable prompt in with a changing question breaks the shared prefix.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md) | Next: [Domain 6: Prompt and Context Engineering](06-prompt-and-context-engineering.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
