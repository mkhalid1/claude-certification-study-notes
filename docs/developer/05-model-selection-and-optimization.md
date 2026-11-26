<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md) · Next: [Domain 6: Prompt and Context Engineering](06-prompt-and-context-engineering.md)
<!-- /nav:top -->

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

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

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

### Item 2

A marketing team generates product descriptions and notices that the same input produces differently worded text each time. Legal has approved one description per product and requires that the approved text be shown every time a product page loads. A developer suggests passing a fixed random seed in each request so the model always generates the same words. What is the right approach?

A. Pass a fixed seed in the request, as the developer suggests, so that generation becomes repeatable.

B. Generate once, store the approved description, and serve the stored text on each page load rather than generating it again.

C. Raise the effort level so that the model settles on one best wording.

D. Shorten the prompt so that there is less room for variation in the output.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Generation is sampled, so you should not expect identical wording from repeated calls. When a specific approved text must appear every time, the reliable design is to store it and serve it. That also saves the cost of generating it again.

- **A** is wrong because regenerating the text on each page load cannot guarantee the exact wording legal approved. The Messages API also offers no seed parameter for this.
- **C** is wrong because effort changes how thoroughly the model works, not whether its output repeats word for word.
- **D** is wrong because a shorter prompt does not remove sampling variation.

</details>

### Item 3

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

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md) | Next: [Domain 6: Prompt and Context Engineering](06-prompt-and-context-engineering.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
