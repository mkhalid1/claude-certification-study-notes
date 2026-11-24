<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 3: Integration](03-integration.md) · Next: [Domain 5: Governance, Safety, and Risk Management](05-governance-safety-and-risk-management.md)<!-- /nav:top -->

# Domain 4: Evaluation, Testing, and Optimization

Weight: 17% of scored items, about 10 items.

## What this domain measures

This domain tests whether you can prove a Claude system works, find out why it does not, and make it cheaper or faster without breaking it. The guide lists seven skills:

- **Coverage gaps.** After a failure reached production, find which metric dimension (accuracy, groundedness, latency, cost, safety, security) or grading target the suite lacked, and name the metric that would have caught it.
- **Evaluations from business processes.** Decide which part of a business process is a valid evaluation for a given decision (model change, prompt change, release gate), derive the success criterion from the business outcome, and choose a test mix that covers the real production distribution.
- **LLM-as-judge design.** When a judge disagrees with human review, identify the bias responsible (position, verbosity, self-preference, judge drift) and choose the correction.
- **Experimental design.** Within a sample and cost budget, set up a comparison of two configurations that can be interpreted, including how many trials per case are needed to separate the real effect from run-to-run variance.
- **Root-cause isolation.** From logs or traces, tell apart prompt failure, retrieval failure, hallucination, tool or subagent error, and model mismatch, and name the fix.
- **Cost and latency optimisation.** Read telemetry, including cache hit rates, to find where spend or latency concentrates, and reduce it without going below the quality floor.
- **Model version migration.** From a deprecation timeline and regression results, choose whether to pin, re-tune prompts, re-baseline evaluations or defer.

In real work this is what lets you change anything safely. Without trustworthy evaluations, every prompt or model change is a guess.

## How questions look

Expect artifacts: a description of an evaluation suite and the incident it missed, judge scores next to human scores, a sample budget and observed variance, an excerpt from a trace, a spend breakdown by workload with cache hit rates, or a deprecation date with a regression table.

The options are usually four actions that all sound like good engineering. The right one targets the specific gap or bias shown in the evidence. Wrong ones often add more of what the suite already measures, swap in a bigger model, or skip the evidence-gathering step.

## A method for these questions

1. Restate the evidence in one line: what failed, what the suite or judge said, and what humans or production said.
2. For coverage items, ask which dimension the failure belongs to. A fabricated fact is groundedness. A slow response is latency. A leaked secret is security. Then check whether the suite measures that dimension at all.
3. For business-derived items, start from the decision the evaluation supports and the outcome the business cares about. The label should come from what the business actually decided.
4. For judge items, compare the pattern of disagreement with each bias: order effects, length effects, favouring its own outputs, or change over time.
5. For experiment items, count the budget, check whether runs are paired on the same cases, and confirm there are repeated trials per case when outputs vary.
6. For trace items, walk the pipeline in order: input, retrieval, tool and subagent calls, model output. The root cause is the first stage where the trace goes wrong.
7. For migration items, put the deprecation date next to the size and stakes of the regressions before choosing.

## Traps that cost points

- **Adding more accuracy cases when the gap is a different dimension.** If the failure was ungrounded content or a latency spike, more accuracy cases still miss it.
- **Using engineer-written easy cases as a release gate.** They do not match the production distribution and miss the rare, high-stakes cases.
- **Fixing judge bias by switching to a bigger judge.** Position and verbosity effects need a design change such as order swapping or a calibrated rubric.
- **Running each configuration once on different cases.** Unpaired single runs cannot separate the configuration effect from run-to-run noise.
- **Blaming the model first.** Traces often show a retrieval miss or a tool error that was reported as success. Fix the first broken stage.
- **Optimising the visible workload instead of the expensive one.** Telemetry shows where spend concentrates. Act there.
- **Migrating to an alias that always points to the newest model.** Behaviour can change without warning. Pin a tested version.
- **Deferring past a deprecation date.** Deferral is only valid when the timeline allows it.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Trace and telemetry items carry the most text. Read the question first so you know whether you are hunting for a root cause, a missing metric, or a cost hotspot, and then scan the artifact for that one thing. Experimental design items often need simple arithmetic: check the budget against each option before reasoning about anything else.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Metric dimensions | Accuracy, groundedness, latency, cost, safety, security. A gap in any one can let a failure through. |
| Groundedness | Every claim in the output is supported by the provided source or context. |
| Business-derived evaluation | Labels come from real business decisions. Success criteria come from the outcome the business cares about. |
| Deployed distribution | The test mix should match production, including rare and high-stakes cases. |
| Position bias | The judge favours an answer because of where it appears. Fix by evaluating both orders. |
| Verbosity bias | The judge rewards length. Fix with a rubric on specific criteria, calibrated against human labels. |
| Self-preference | The judge favours outputs from its own model. Fix by using a different judge model and checking against humans. |
| Judge drift | Judge scores change over time with no change in quality. Fix by re-checking against a fixed human-labelled set. |
| Paired comparison | Run both configurations on the same cases so differences come from the configuration. |
| Trials per case | When outputs vary, repeat each case several times to measure variance. |
| Root-cause order | Input, retrieval, tool or subagent, generation. The first stage that is wrong is the cause. |
| Batch processing | For work that is not latency-sensitive, asynchronous batch processing lowers cost. |
| Version pinning | Pin an exact model version in production. Re-baseline and re-tune before moving. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A hospital group uses Claude to summarise discharge notes for primary care doctors. Before release, the evaluation suite compared each summary with a reference summary written by a clinician and scored overall similarity, and the system passed comfortably. In production, a doctor found a summary that listed a medication the patient was never prescribed. The summary was otherwise close to the reference, so similarity scoring would have rated it well. Which metric would have detected this failure before release?

- A. A groundedness check that verifies each stated fact, such as each medication, appears in the source discharge note.
- B. A latency measurement at p95 for each summary request.
- C. More reference summaries in the similarity test set.
- D. Average cost per summary across the test set.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The failure is a fabricated fact, which is a groundedness problem. Similarity to a reference does not test whether each claim is supported by the source, so a groundedness check against the note is the missing dimension.

- **B** is wrong because latency has nothing to do with fabricated content.
- **C** is wrong because more similarity cases measure the same dimension and still reward a summary that is mostly similar.
- **D** is wrong because cost does not measure content correctness.

</details>

### Item 2

An insurer's underwriting assistant drafts a risk note and suggests a risk tier for each commercial policy application. The team wants a release gate for prompt changes. What the business cares about is that underwriters assign the correct tier and can accept notes with only light edits. Production traffic is mostly small businesses, but a small share of large, complex applications drives most premium value. Which evaluation design fits best?

- A. Score each new note's word overlap with the previous prompt's output on the same applications.
- B. Have engineers write twenty clear-cut example applications and require all twenty to pass.
- C. Use historical applications labelled with the underwriter's final tier, measure tier agreement and edit effort, and sample the set to match production mix while including enough large, complex cases.
- D. Launch the change and survey underwriters after a month.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The labels come from the real business decision, the criteria match the outcome the business cares about, and the sample reflects the production mix while making sure the high-value cases are present. That makes it a valid gate for prompt changes.

- **A** is wrong because similarity to the old output measures change, not correctness, and would penalise improvements.
- **B** is wrong because a small, easy, engineer-written set does not reflect the deployed distribution.
- **D** is wrong because a post-launch survey is not a release gate.

</details>

### Item 3

A team uses Claude as a pairwise judge to compare answers from two prompt versions. The judge prefers whichever answer is shown first in about 70% of comparisons. Human reviewers given the same pairs, in random order, split close to evenly. Which correction addresses the responsible bias?

- A. Replace the judge with the largest available model tier.
- B. Run each comparison in both orders and count a preference only when the judge prefers the same answer both times.
- C. Add an instruction telling the judge to be fair and unbiased.
- D. Shorten both answers before judging so they are easier to compare.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** A preference that tracks display order is position bias. Evaluating both orders and keeping only consistent preferences cancels the order effect by design.

- **A** is wrong because larger judges can still show position bias, and the change does not test for it.
- **C** is wrong because an instruction does not reliably remove a systematic order effect.
- **D** is wrong because length is not the bias shown in the evidence.

</details>

### Item 4

An analytics vendor compares two configurations using a rubric-based judge. Configuration A uses the same model as the judge. Configuration B uses a different model. The judge scores A clearly higher, and it also gives higher scores to longer answers. Human reviewers prefer B, and they prefer shorter answers that still cover the rubric. Tests showed that swapping answer order has no effect. Which two corrections address the biases responsible? (Choose two.)

- A. Run the judge at a higher temperature to vary its scores.
- B. Randomise the order in which the two answers appear.
- C. Rewrite the rubric to score specific criteria, give no credit for length beyond what the criteria need, and calibrate it against human labels.
- D. Use a judge model that is different from the models that produced the answers, and check its agreement with human reviewers.
- E. Stop human review and rely on the judge, since it is more consistent.

<details>
<summary>Answer and explanation</summary>

**Answer: C and D.** The evidence shows verbosity bias (higher scores for length) and self-preference (higher scores for the judge's own model). A criteria-based rubric calibrated against humans addresses length (C), and an independent judge model addresses self-preference (D).

- **A** is wrong because more randomness adds noise without removing either bias.
- **B** is wrong because the stem says order has no effect, so position bias is not the problem.
- **E** is wrong because human review is the reference that reveals the biases. Removing it hides them.

</details>

### Item 5

A team must choose between two system prompts for a support assistant. Its evaluation budget is 600 model runs. It has a 200-case evaluation set, and earlier testing showed that about 15% of cases flip between pass and fail when the same configuration is run twice. The team wants a comparison that shows whether any difference comes from the prompt rather than run-to-run variance. Which design fits the budget and the goal?

- A. Run prompt A on 300 cases and prompt B on a different 300 cases, once each.
- B. Run prompt A 300 times and prompt B 300 times on the single hardest case.
- C. Run both prompts once on all 200 cases and compare the pass rates.
- D. Select 100 representative cases and run each prompt three times on every case, comparing per-case pass rates between prompts.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** One hundred cases, three trials and two prompts is exactly 600 runs. Pairing both prompts on the same cases removes case differences, and repeated trials measure variance per case so the prompt effect can be separated from noise.

- **A** is wrong because the set has only 200 cases, and unpaired single runs cannot separate prompt effect from variance.
- **B** is wrong because one case says nothing about the distribution the assistant will see.
- **C** is wrong because single runs give no way to tell a real difference from the 15% flip rate.

</details>

### Item 6

A customer asked an e-commerce assistant about the return window for orders shipped to Germany. The trace shows the retrieval step returned the correct EU policy passage, which states a 14-day window, and that passage is in the model's context. No tool calls failed. The final answer told the customer the window was 45 days, a figure that appears nowhere in the context or the system prompt. What is the root cause, and what is the corrective action?

- A. Hallucination. Require answers to cite the retrieved passage for each policy figure and add a groundedness check before responses are sent.
- B. Retrieval failure. Increase the number of retrieved passages.
- C. Tool error. Add retries to the retrieval tool.
- D. Model mismatch. Move the assistant to the largest tier.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Retrieval worked and no tool failed, but the answer states a figure found nowhere in the context. That is an ungrounded generation. Requiring citations and checking groundedness before sending targets that stage directly.

- **B** is wrong because the correct passage was already retrieved.
- **C** is wrong because the trace shows no tool errors.
- **D** is wrong because nothing shows the task is beyond the current tier, and a larger model does not guarantee grounded figures.

</details>

### Item 7

A publisher's telemetry shows that 60% of Claude spend comes from a nightly job that re-summarises every article updated that day. Results are needed by the next morning. The remaining 40% comes from an interactive reader chat, which only meets its quality floor on the current tier. Each nightly request is a different article with a short 500-token instruction block, and its cache hit rate is near zero. Which optimisation reduces spend most without breaching the quality floor?

- A. Move the interactive chat to a smaller model tier.
- B. Run the nightly job through asynchronous batch processing instead of real-time requests.
- C. Cut the maximum output length for the interactive chat in half.
- D. Add a cache breakpoint after the nightly job's 500-token instruction block.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Spend is concentrated in a job that is not latency-sensitive and only needs results by morning. Batch processing is built for that pattern and lowers cost without changing the model or prompts, so quality is unchanged.

- **A** is wrong because the stem says the chat meets its quality floor only on the current tier.
- **C** is wrong because it targets the smaller share of spend and risks truncating useful answers.
- **D** is wrong because the shared prefix is only 500 tokens, so caching saves very little on that workload.

</details>

### Item 8

A bank's document assistant runs on a model version that will be deprecated in 90 days. Regression results for the candidate version are better overall, but two categories regress: structured field extraction now sometimes changes output formatting, and some answers about loan terms are worded differently enough to fail exact-match checks that were tuned to the old model. Which two actions fit the timeline? (Choose two.)

- A. Re-tune the prompts for the two regressed categories and re-run the evaluations on the candidate version.
- B. Defer the migration until after the deprecation date, when more is known.
- C. Pin the current version indefinitely so behaviour never changes.
- D. Point production at an alias that always tracks the newest model, so future migrations happen automatically.
- E. Re-baseline the evaluation suite on the candidate version, then pin that exact version in production once it passes the gates.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** With 90 days to go, the team should fix the specific regressions through prompt changes (A) and refresh its baseline and checks for the new version before pinning it (E). Together, these manage drift inside the timeline.

- **B** is wrong because deferring past the deprecation date leaves production on a model that is going away.
- **C** is wrong because a deprecated version cannot be pinned indefinitely.
- **D** is wrong because a moving alias lets behaviour change without evaluation, which is the drift the team must manage.

</details>

### Item 9

A legal research tool runs on a model version that the deprecation schedule shows will remain available for about twelve more months. A newer candidate version improves general answers, but in regression testing it misstates citation details in a small share of case-law answers, which is the product's highest-stakes category. No current customer has asked for the new version's capabilities. Which response is most appropriate?

- A. Migrate now, since the newer version is better on average.
- B. Migrate now and fix the citation regression after launch based on customer reports.
- C. Keep the current version pinned, defer the migration, and schedule a re-evaluation of later versions well before the deprecation date.
- D. Remove the case-law citation tests from the suite so the candidate version passes.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The timeline leaves plenty of room, the regression is in the highest-stakes category, and there is no business pressure to move. Pinning and deferring with a planned re-evaluation manages risk without wasting the remaining window.

- **A** is wrong because average improvement does not justify a regression in the most important category.
- **B** is wrong because letting customers find citation errors is unacceptable for a legal product.
- **D** is wrong because removing the tests hides the regression instead of managing it.

</details>

### Item 10

A market-intelligence system uses an orchestrator and four subagents. A client's report said "no competitor pricing data is available", although public pricing pages exist. The trace shows that the pricing subagent's web search call timed out, the subagent returned an empty result marked as a success, and the orchestrator wrote the report from what it received. The orchestrator's prompt and the other subagents behaved as expected. What is the root cause, and what is the corrective action?

- A. Prompt failure. Rewrite the orchestrator prompt to ask for more thorough reports.
- B. Retrieval failure. Build a larger index of competitor pages.
- C. Hallucination. Add a groundedness check on the final report.
- D. Tool or subagent error. Make subagents return an explicit error status on tool failure, and have the orchestrator retry or flag the gap in the report.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The first broken stage is the tool call that timed out and was reported as a successful empty result. Explicit error status lets the orchestrator recover or tell the client what is missing, instead of presenting a failure as a finding.

- **A** is wrong because the orchestrator acted correctly on the data it was given.
- **B** is wrong because the system uses live web search, and the search failed by timing out, not by missing content.
- **C** is wrong because the report faithfully reflected its inputs. The input itself was wrong.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 3: Integration](03-integration.md) | Next: [Domain 5: Governance, Safety, and Risk Management](05-governance-safety-and-risk-management.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
