<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 3: Claude Code](03-claude-code.md) · Next: [Domain 5: Model Selection and Optimization](05-model-selection-and-optimization.md)
<!-- /nav:top -->

# Domain 4: Eval, Testing, and Debugging

Weight: 10% of scored items, about 6 items.

## What this domain measures

This domain grew from a small slice of the old exam to a tenth of the new one. It tests whether you can prove that a Claude feature works and find out why it does not. The guide groups it into two skills:

- **Evaluation design.** Building an evaluation that measures a stated quality goal. That includes LLM-as-judge harnesses calibrated against human-labelled data, golden sets, regression tests, and behavioural checks, plus choosing metrics you can defend.
- **Failure-mode diagnosis.** Reading a trace excerpt or a reproduction and naming the failure: hallucination, refusal, under-acting, context loss, format drift, or inconsistent output. Then working out whether it starts in the integration layer or in the model's output, and choosing a fix for the cause, not the symptom.

Why it matters: without an evaluation, every prompt or model change is a guess, and every fix can quietly break something else. Without diagnosis, teams patch prompts for bugs that live in their own code.

## How questions look

Evaluation items describe a team's quality goal and a proposed eval, and ask what is wrong with it or what to add. Diagnosis items show a few lines of a trace (a retrieval result, a tool call, a response) and a symptom. The options usually include a prompt tweak, a model change, a code fix, and a monitoring step. The right answer matches the layer where the trace shows the problem starting.

## A method for these questions

1. Restate the quality goal in one line. An eval is only defensible if its metric measures that goal.
2. For eval design, check three things: the data is representative of real traffic, including hard cases; the labels are trustworthy; and any automated judge has been checked against human labels.
3. For diagnosis, walk the trace in order: input, what the integration sent, what came back from tools or retrieval, and what the model wrote. Find the first point where things went wrong.
4. If the integration sent bad or missing data, fix the code. If the inputs were right and the output was wrong, fix the prompt, schema, or model choice.
5. Prefer fixes that address the cause and add a regression case so it stays fixed.

## Traps that cost points

- **Trusting an uncalibrated judge.** An LLM judge is a measuring instrument. Until you compare its scores with human labels on a sample, you do not know what it measures.
- **Golden sets made of demo examples.** Easy, hand-picked cases pass and tell you nothing. Pull from real traffic and include the known failures and edge cases.
- **Blaming the model for an integration bug.** If retrieval returned nothing or the history was truncated, a prompt change will not fix it. Read the trace before touching the prompt.
- **Single-run evals for a non-deterministic system.** One run per case hides inconsistency. Run cases several times when consistency matters and report the spread.
- **Metrics that do not match the goal.** Response length, thumbs-up counts, or an "overall quality" score rarely measure the stated requirement directly.
- **Fixing one case and skipping the full suite.** A prompt change that fixes the reported case can break others. Run the regression set on every change.
- **Treating a refusal as a model fault by default.** Over-broad instructions in your own prompt often cause refusals of legitimate requests.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 4 is about 6 items, so budget roughly 10 minutes. Trace items reward a systematic read: find the first bad step, and the answer is usually the option that fixes that step.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Golden set | A labelled set of representative inputs with known good outputs, including edge cases and past failures. Version it with the code. |
| Regression test | Re-run the golden set on every prompt, model, or code change, and block the change if scores drop. |
| LLM-as-judge | A model grading outputs against a rubric. Calibrate it on a human-labelled sample, and measure agreement before using it as a gate. |
| Behavioural check | A targeted test of one behaviour, for example "says not found when the answer is missing" or "asks for the order id". |
| Defensible metric | Measures the stated goal directly: field-level accuracy for extraction, per-class precision and recall for rare classes, schema validity for parsers. |
| Hallucination | A confident claim not supported by the input. Check first whether the needed data reached the model at all. |
| Refusal | Declining a legitimate request. A decline caused by your own over-broad prompt rule usually arrives as ordinary text. A decline by Anthropic's safety classifiers arrives with `stop_reason: "refusal"`. Check for both. |
| Under-acting | The model describes or promises an action but does not call the tool. Fix tool descriptions and instructions about when to act. |
| Context loss | An earlier fact or constraint is missing from what the model sees. Often caused by truncation in the integration. |
| Format drift | Output gradually stops matching the expected shape. Fix with schema-constrained output and validation, not more parsing hacks. |
| Inconsistent output | The same input gives different results across runs. Measure it with repeated runs, then tighten criteria and examples. |

## Practice items

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A team built a golden set for their customer-support bot from 50 questions the product manager wrote for a sales demo. The bot scores 96% on it, yet support escalations about wrong answers keep rising in production. Reviewing escalations shows multi-part questions, typos, and questions about discontinued products, none of which appear in the golden set. What is the best fix?

A. Raise the pass threshold on the existing 50 questions from 90% to 98%.

B. Rebuild the golden set from a labelled sample of real production questions, including the escalated cases and edge cases like typos and discontinued products.

C. Have the bot generate 1,000 new questions similar to the demo set and add them to the golden set.

D. Replace the golden set with a weekly count of thumbs-down ratings from users.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The eval passes because its data does not look like production. A golden set drawn from real traffic, with the known failures included, measures what users actually ask. Then the score can track the escalation problem.

- **A** is wrong because a stricter threshold on unrepresentative data still misses the real failures.
- **C** is wrong because questions generated to resemble the demo set share its blind spots.
- **D** is wrong because thumbs-down counts are sparse, noisy, and arrive only after users have been harmed. They do not replace a labelled set.

</details>

### Item 2

A pipeline asks Claude to return a JSON object and parses it with a strict JSON parser. For weeks it worked, but parse failures have crept up to about 3% of requests. Logs show the failing responses wrap the JSON in Markdown code fences or add a sentence before it. The developer's first idea is to add more regular expressions to strip fences and leading text. What fix addresses the cause?

A. Use structured outputs with the JSON schema so the response is constrained to that shape, and keep validating the parsed result before use.

B. Add regular expressions for each new wrapper pattern found in the logs.

C. Retry every failed parse up to three times until a response parses.

D. Ask for the JSON in a longer, more detailed system prompt that describes the format three times.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** This is format drift: free-text output that is meant to be JSON slides into other shapes. Structured outputs constrain the response to a schema at generation time, which removes the cause. Validation stays as a safety net for cases like a truncated response.

- **B** is wrong because each new pattern needs another rule, and the parser stays fragile.
- **C** is wrong because retries pay for extra calls and still fail some of the time.
- **D** is wrong because repeating the instruction may reduce drift but does not guarantee the shape.

</details>

### Item 3

A pharmacy app answers questions using the product labels it supplies in the request. After someone added "Never provide medical advice of any kind" to the system prompt, the app began refusing to answer "What is the maximum daily dose on this label?", which the business wants answered from the supplied label. The model and the retrieval code have not changed. What is the best fix?

A. Switch to a different model tier that refuses less often.

B. Retry each refused request until the model answers.

C. Rewrite the instruction to define the scope precisely, for example: answer factual questions using the supplied label text and cite it, and decline to recommend treatments beyond the label. Then add both kinds of question to the regression set.

D. Remove the system prompt entirely so that no instruction can cause a refusal.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The refusals started with a prompt change, so the cause is the over-broad instruction, not the model. A precise scope lets the app answer what the business wants and still decline what it should not. Regression cases on both sides keep the boundary in place.

- **A** is wrong because the refusals began with the prompt edit while the model stayed the same, so changing the tier targets the wrong cause.
- **B** is wrong because retries keep paying for refusals and do not change the instruction causing them.
- **D** is wrong because removing all instructions throws away the scope the app needs.

</details>

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 3: Claude Code](03-claude-code.md) | Next: [Domain 5: Model Selection and Optimization](05-model-selection-and-optimization.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
