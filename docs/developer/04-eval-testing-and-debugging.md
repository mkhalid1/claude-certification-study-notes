<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 3: Claude Code](03-claude-code.md) · Next: [Domain 5: Model Selection and Optimization](05-model-selection-and-optimization.md)<!-- /nav:top -->

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

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A legal-tech team wants to use an LLM-as-judge to grade contract summaries for faithfulness, and plans to make the judge's score a release gate. They have written a rubric and run the judge on 500 summaries, and the average score looks reasonable. Two senior lawyers have each labelled 100 of the same summaries as faithful or not. What should the team do before trusting the judge as a gate?

A. Raise the passing threshold on the judge's score so that the gate errs on the side of caution.

B. Use a larger model as the judge, since a stronger model will grade more accurately without further checks.

C. Average the judge's scores across all 500 summaries again to confirm the result is stable.

D. Compare the judge's verdicts with the lawyers' labels on those summaries, measure agreement, and refine the rubric until agreement is acceptable.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A judge has to be calibrated against human judgment before it can stand in for that judgment. The lawyers' labels are the reference, so measuring the judge's agreement with them shows whether the score means what the team thinks. Refining the rubric until agreement holds makes the gate defensible.

- **A** is wrong because a stricter threshold on an unvalidated score is still unvalidated.
- **B** is wrong because a stronger judge still needs checking against human labels, and model strength alone does not prove agreement.
- **C** is wrong because re-averaging shows the score is stable, not that it is correct.

</details>

### Item 2

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

### Item 3

An accounts-payable team uses Claude to extract invoice number, supplier, date, total, and currency from scanned invoices into JSON for an ERP import. The quality goal is "imports succeed and every field matches the invoice". They have 400 invoices with fields labelled by the finance team. The team needs metrics they can defend to the finance director. Choose two.

A. Field-level exact-match accuracy against the labelled values, reported per field.

B. Average length of the JSON response, compared against last month's average.

C. The rate at which responses pass validation against the ERP import schema.

D. A single "overall quality" score from 1 to 10 given by an uncalibrated LLM judge.

E. The number of thumbs-up ratings from the two clerks who use the tool.

<details>
<summary>Answer and explanation</summary>

**Answer: A and C.** The goal has two parts. Per-field exact match against the finance labels measures "every field matches", and it shows which fields fail. Schema validity rate measures "imports succeed". Together they map directly onto the stated goal.

- **B** is wrong because response length says nothing about whether fields are correct or imports work.
- **D** is wrong because an uncalibrated overall score is hard to defend and does not separate the two parts of the goal.
- **E** is wrong because two people's ratings are a tiny, biased sample that does not measure field accuracy.

</details>

### Item 4

An HR assistant answers policy questions from a retrieved set of policy documents. An employee reports that it confidently cited a "remote work stipend policy" that does not exist. The trace shows that the retrieval step returned zero documents for that query, because a recent change to the department filter excluded all documents, and the prompt then asked Claude to answer the question. What is the best fix?

A. Add "never make things up" to the system prompt and move to a larger model.

B. Lower the effort level so that the model writes shorter answers with fewer details to get wrong.

C. Fix the department filter in the retrieval code, and change the request so that when retrieval returns nothing the assistant says it could not find the answer instead of answering.

D. Add a disclaimer under every answer telling employees to check the policy portal.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The trace shows the failure starting in the integration layer: retrieval sent no documents. The filter bug is the cause, and the missing "no results" path let a hallucination through. Fixing both addresses the cause and closes the path for future retrieval misses.

- **A** is wrong because it treats an integration bug as a model problem. No prompt can ground an answer in documents that were never sent.
- **B** is wrong because shorter answers with no supporting documents are still unsupported.
- **D** is wrong because a disclaimer hides the symptom and leaves employees with wrong answers.

</details>

### Item 5

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

### Item 6

A stock-checking agent has a `check_inventory` tool. In about one in eight conversations, the trace shows Claude replying "Let me check the warehouse stock for you" and then ending its turn with `stop_reason: "end_turn"` and no tool call. Customers wait for an answer that never comes. The tool works whenever it is called. Which diagnosis and fix fit best?

A. Hallucination: add an instruction never to invent stock numbers.

B. Context loss: send the full conversation history with every request.

C. Refusal: remove any safety wording from the system prompt so the model is less cautious.

D. Under-acting: make the tool description and instructions say clearly when to call `check_inventory` and that Claude should call it rather than announce it, then add regression cases for these conversations.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The model states an intent to act and then stops without calling the tool. That is under-acting. Clear guidance on when to use the tool, attached to the tool itself and backed by instructions to act directly, addresses it. Regression cases keep it from coming back.

- **A** is wrong because no stock numbers were invented. The model never got that far.
- **B** is wrong because nothing in the trace shows missing history.
- **C** is wrong because the model agreed to help, so this is a failure to act, which is a different failure mode.

</details>

### Item 7

A team tunes the system prompt of a ticket-routing assistant to fix a reported misroute of billing disputes. The new prompt fixes that case, and they ship it after checking only that ticket. Two days later, routing accuracy for technical tickets has dropped sharply. They have a labelled set of 600 past tickets that nobody ran. What process change prevents this?

A. Ask the person who reported each bug to approve the next prompt change.

B. Run the full labelled set as a regression suite on every prompt change and block the change if accuracy drops in any category.

C. Ship prompt changes only on Mondays so that problems are noticed during the working week.

D. Freeze the prompt permanently and route misroutes to a human queue.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** A prompt change can fix one case and break others. Running the whole labelled set on every change, with a check per category, catches that before release. The team already has the data. It just was not used.

- **A** is wrong because the reporter checks only their own case, which is how the regression slipped through.
- **C** is wrong because timing does not stop a regression. It only changes when users meet it.
- **D** is wrong because freezing the prompt gives up improvement to avoid testing.

</details>

### Item 8

A content-moderation service labels user posts into five categories. When the team runs the same 300 posts through the service three times, about 15% of posts get a different label on at least one run, mostly in two neighbouring categories. The eval they use today runs each post once. They want to measure the problem honestly and reduce it. Choose two.

A. Keep running each post once, and when two runs disagree, keep the label that sounds more severe.

B. Run each eval case several times and report a consistency rate alongside accuracy, broken down by category.

C. Use the same model as both the classifier and the judge so that their labels always agree.

D. Rewrite the prompt with explicit criteria that separate the two neighbouring categories, with worked examples of borderline posts.

E. Raise `max_tokens` so that the model has more room to decide.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Repeated runs turn the inconsistency into a number the team can track, and breaking it down by category shows where it is. Clear criteria and borderline examples for the two confused categories attack the cause: the model has no firm rule for those cases.

- **A** is wrong because single runs hide the problem, and picking the more severe label adds a bias without measuring anything.
- **C** is wrong because agreement between a model and itself is not evidence of correctness.
- **E** is wrong because the output limit is not what makes the label vary.

</details>

### Item 9

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

### Item 10

A travel chatbot is told early in a conversation that the user is travelling with a wheelchair. Twenty messages later it recommends a hotel with no step-free access. The trace shows the request for that turn contained only the last 10 messages, because the app trims history to control cost. The wheelchair message was turn 3. Where does the failure start, and what is the fix?

A. In the integration layer: replace blind trimming with history management that keeps key facts, for example by carrying a running summary of constraints or pinning them into the request.

B. In the model output: add an instruction telling Claude to always remember accessibility needs.

C. In the model output: move to a model with a larger context window.

D. In the retrieval layer: add more hotels with step-free access to the hotel database.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The trace shows the constraint was never sent on the failing turn, so the failure is in the integration's history handling. The model could not respect a fact it did not see. Keeping key constraints when trimming fixes the cause and still controls cost.

- **B** is wrong because an instruction to remember cannot recover a message the app removed.
- **C** is wrong because the app trims history itself, so a bigger window would receive the same trimmed request.
- **D** is wrong because suitable hotels already existed in the data. The model was never told about the constraint.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->← Previous: [Domain 3: Claude Code](03-claude-code.md) | Next: [Domain 5: Model Selection and Optimization](05-model-selection-and-optimization.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
