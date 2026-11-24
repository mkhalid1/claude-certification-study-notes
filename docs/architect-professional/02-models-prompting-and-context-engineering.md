<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 1: Solution Design and Architecture](01-solution-design-and-architecture.md) · Next: [Domain 3: Integration](03-integration.md)<!-- /nav:top -->

# Domain 2: Claude Models, Prompting, and Context Engineering

Weight: 13% of scored items, about 8 items.

## What this domain measures

This domain tests whether you can get the right behaviour from Claude at the right cost. The guide lists six skills:

- **Model tier selection.** Pick the tier that meets every binding latency, cost and quality constraint for a workload, and say why each rejected tier fails.
- **System prompt diagnosis.** Find the underspecification behind degraded or inconsistent output, and choose a revision that constrains scope, format and behaviour without blocking legitimate variation.
- **Prompting technique.** Match the technique (zero-shot, few-shot, prompted reasoning versus native thinking, structured output) to an observed failure, without paying for tokens the task does not need.
- **Context composition and token economics.** Look at what goes into each call and how caching behaves at a given call volume, and find where tokens can be cut without losing task-relevant information.
- **Cache breakpoints and reusable packaging.** Order prompts so stable content comes before dynamic content, place breakpoints, and use modular or Skill-based packaging to maximise reuse.
- **Reasoning effort and context lifecycle.** Allocate reasoning effort and choose context editing, the memory tool or compaction so a long-running agent keeps performing within its latency and accuracy requirements.

In practice, this domain decides your unit economics. The same design can cost several times more, or run several times slower, depending on prompt order, model tier and how context is managed.

## How questions look

Stems usually give you a workload profile with numbers: call volume, prompt sizes, latency targets, evaluation scores by tier, or cache hit rates. Some show a short system prompt or describe its structure and the failures it causes. Others describe a long-running agent whose quality drops after some hours.

Options tend to be four reasonable-sounding changes. The wrong ones usually fix a different problem than the one observed (for example, enforcing format when the failure is label confusion), over-correct (rigid rules that block valid answers), or spend more tokens or latency than the constraint allows.

## A method for these questions

1. Name the observed failure in one phrase: wrong label, invalid format, inconsistent scope, too slow, too expensive, degrades over time.
2. List the binding constraints with their numbers. For tier items, put each tier against each constraint and strike any that fails one.
3. Match the failure to the lever. Ambiguous categories call for examples. Format failures call for structured output. Multi-step reasoning errors call for thinking or more effort on that step only. Cost and latency at volume call for caching, trimming and tier changes.
4. For caching items, write out the prompt order and find the first byte that changes between calls. Everything after it misses the cache.
5. For context lifecycle items, separate within-session pressure (old tool results, long transcripts) from cross-session continuity (decisions that must survive a new session).
6. Check the chosen fix does not remove task-relevant information or over-constrain legitimate answers.

## Traps that cost points

- **Reaching for the largest tier when quality is already sufficient.** If a smaller tier meets the quality bar in evaluation, the larger tier fails the cost or latency constraint and is wrong.
- **Using structured output to fix wrong answers.** A schema guarantees a valid shape. It does not make the chosen label correct.
- **Turning on thinking everywhere.** Extra reasoning adds latency and tokens. Apply it to the step that fails, not to routing or formatting steps that already work.
- **Putting a timestamp or user name at the top of a cached prompt.** Any change early in the prefix invalidates the cache for everything after it.
- **Fixing a vague prompt with a wall of NEVER rules.** Long prohibition lists over-constrain and still miss cases. Define scope, out-of-scope handling and format instead.
- **Truncating documents to save tokens.** Cutting the input the task needs is information loss. Trim duplicated instructions, unused tool definitions and stale tool results first.
- **Treating caching as a fix for distraction.** Caching makes repeated content cheaper. It does not stop irrelevant content from confusing the model.
- **Carrying everything forward in long sessions.** Replaying full transcripts fills context and degrades quality. Persist decisions in memory and clear what is no longer needed.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Numeric stems in this domain reward a quick table on scratch paper: tiers down the side, constraints across the top. That takes 30 seconds and usually leaves one survivor. For caching items, sketch the prompt order as a line and mark the first dynamic element.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Tier selection | The right tier is the cheapest and fastest one that meets the quality bar. Every rejected tier fails a named constraint. |
| Zero-shot vs few-shot | Few-shot helps when labels or style are ambiguous. Boundary examples teach more than easy ones. |
| Prompted reasoning vs native thinking | Both trade tokens and latency for accuracy on multi-step problems. Use them where reasoning fails, not by default. |
| Structured output | Enforces shape and parseability. Use it when downstream code needs a guaranteed format. |
| Good system prompt | States scope, what to do out of scope, output format and tone, and leaves room for natural variation. |
| Cache prefix | Stable content first, dynamic content last. The first change in the prefix ends cache reuse. |
| Breakpoint placement | Put the breakpoint at the end of the content that is identical across the call pattern. |
| Skills packaging | Short descriptions load up front, full procedures load only when relevant. Good for many procedures with separate owners. |
| Context editing | Clears stale content such as old tool results during a long session. |
| Memory tool | Stores information outside the context window so it can be read back later, including in new sessions. |
| Compaction | Summarises earlier context so a long conversation can continue within the window. |
| Token economics | Multiply per-call waste by call volume. Unused tool definitions and redundant examples add up fast. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A payments company classifies each incoming support message into one of twelve routing queues. Volume is about two million messages a month, the routing call must return within 800 ms at p95, and the required accuracy is 94%. An offline evaluation shows the Haiku tier at 96% accuracy with p95 latency of 450 ms, the Sonnet tier at 97% with 1,100 ms, and the Opus tier at 98% with 2,300 ms. Which choice satisfies every binding constraint?

- A. The Opus tier, because the highest accuracy minimises misrouted messages.
- B. The Sonnet tier, with prompt caching added to bring latency under the target.
- C. The Haiku tier, because it meets the accuracy floor and is the only tier inside the latency limit, at the lowest cost.
- D. The Sonnet tier for half of the traffic and the Opus tier for the other half, to balance cost and accuracy.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Only Haiku meets the 800 ms latency limit, and its 96% accuracy clears the 94% floor. At two million calls a month, it is also the cheapest option, so nothing is gained by paying for a larger tier.

- **A** is wrong because Opus fails the latency constraint by a wide margin, and the extra accuracy is not required.
- **B** is wrong because caching helps with long repeated prefixes, and nothing in the stem suggests it would close a gap from 1,100 ms to 800 ms on short routing calls.
- **D** is wrong because both tiers in the split fail the latency limit.

</details>

### Item 2

An HR assistant uses this system prompt: "You are a helpful assistant for Northwind HR. Answer employee questions." Users report that it sometimes discusses other employees' pay when asked, its answers range from one line to several pages, and it gives legal opinions on employment disputes. Which revision best fixes the problem without over-constraining it?

- A. State that it answers questions about Northwind's published HR policies using the provided handbook, that it declines questions about other employees or legal disputes and points users to the HR portal, and that answers lead with a short direct answer followed by the policy reference.
- B. Instruct it to answer only by quoting exact sentences from the handbook and to refuse any question that the handbook does not answer word for word.
- C. Add "Be careful, accurate and professional at all times" to the end of the existing prompt.
- D. Add a list of forty topics it must NEVER discuss, written in capital letters, and keep the rest of the prompt unchanged.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The failures come from missing scope, missing out-of-scope handling and missing format guidance. Option A supplies all three while still letting the assistant phrase answers naturally.

- **B** is wrong because it over-constrains. Many legitimate questions need a short explanation, not a verbatim quote.
- **C** is wrong because generic adjectives do not define scope or format, so the same failures continue.
- **D** is wrong because a long prohibition list is brittle, still leaves format unspecified, and misses topics nobody listed.

</details>

### Item 3

A procurement platform extracts invoice fields with Claude and passes the result straight into an accounting system that rejects anything that is not valid JSON matching its schema. Field values are almost always correct, but about one response in two hundred has a trailing comment or a missing bracket, and the import fails. The team wants to remove these failures without making each call noticeably more expensive. Which change addresses the observed failure?

- A. Add ten complete example invoices with their JSON outputs to the prompt.
- B. Enable native extended thinking so the model plans the JSON before writing it.
- C. Ask the model to reason step by step about each field before giving the final JSON.
- D. Use structured output with the accounting schema so responses are constrained to valid, schema-conforming JSON.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The values are right and only the shape fails. Structured output targets exactly that, guaranteeing parseable output that matches the schema, without adding examples or reasoning tokens.

- **A** is wrong because examples make good format more likely but do not guarantee it, and they add thousands of tokens to every call.
- **B** is wrong because thinking adds latency and tokens to solve a reasoning problem the task does not have.
- **C** is wrong because prompted reasoning adds tokens and gives the model more free text that the parser could choke on.

</details>

### Item 4

A contract-review agent makes about 20,000 calls a day. Each call carries a 4,000-token system prompt, definitions for 40 tools totalling 15,000 tokens (traces show only 3 tools are ever used in this workflow), the full conversation history including raw output from file reads in earlier turns that are no longer referenced, and the 10,000-token contract under review. The team wants to cut tokens sent per call without losing task-relevant information. Which two changes should it make? (Choose two.)

- A. Truncate each contract to its first 5,000 tokens, since key terms usually appear early.
- B. Remove the tool definitions this workflow never uses, or load them only on demand.
- C. Remove the output-format section of the system prompt, since the model usually formats correctly anyway.
- D. Clear stale tool results from earlier turns once they are no longer referenced.
- E. Replace the contract with a summary produced by a smaller model before review.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Unused tool definitions and stale tool results are tokens the task never uses, so removing them cuts cost and context pressure with no information loss. At 20,000 calls a day, both savings are large.

- **A** is wrong because clauses later in a contract are task-relevant, so truncation loses information.
- **C** is wrong because the format instructions are relevant to the task, and removing them invites inconsistent output.
- **E** is wrong because a summary can drop the exact wording that contract review depends on.

</details>

### Item 5

A customer-support assistant sends a 20,000-token policy document with every request. The system prompt begins with the current timestamp and the customer's name, followed by the policy document, and the team set a cache breakpoint at the end of the system prompt. Monitoring shows a cache hit rate close to zero even though the policy document never changes during the day. What is the most effective fix?

- A. Extend the cache lifetime so that entries last longer between requests.
- B. Move the timestamp and customer name after the policy document, so the stable policy forms an identical prefix, and keep the breakpoint at the end of the policy.
- C. Add several more cache breakpoints inside the policy document.
- D. Shorten the policy document so that the cache matters less.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The prefix changes at its very first tokens on every call because of the timestamp and name, so nothing after them can be reused. Placing stable content first and dynamic content after it makes the policy an identical prefix that the cache can serve.

- **A** is wrong because a longer lifetime does not help when every prefix is different.
- **C** is wrong because each breakpoint's prefix still begins with the changing timestamp, so none of them hit.
- **D** is wrong because it treats the symptom and may drop policy content the assistant needs.

</details>

### Item 6

A platform team runs a Claude agent that maintains a large codebase in working sessions of about six hours, and work on one migration spans several days. After about two hours, the context is full of old file reads and test logs, and the agent starts repeating work. Each morning, the new session has no record of decisions made the day before. Which context lifecycle strategy fits best?

- A. Set reasoning effort to the maximum for every step so the agent reasons its way past the clutter.
- B. Start a fresh session every hour with no carryover, so the context never fills.
- C. Clear stale tool results with context editing during the session, and record decisions and progress with the memory tool so they are available in later sessions.
- D. Paste the full transcripts of all previous sessions into the system prompt each morning.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** There are two problems: stale content within a session, and lost continuity between sessions. Context editing removes the clutter, and the memory tool keeps the decisions outside the context window so a new session can read them back.

- **A** is wrong because more reasoning effort adds latency and does not remove the stale content filling the window.
- **B** is wrong because hourly resets with no carryover make the continuity problem worse.
- **D** is wrong because replaying full transcripts fills the context immediately and recreates the first problem.

</details>

### Item 7

A collections agency asks Claude to label each outgoing letter's tone as "neutral", "firm" or "hostile" for a compliance review. The definitions are internal: "firm" allows a clear deadline and consequence, while "hostile" means threats outside the agreed process. Zero-shot labels are inconsistent, and most errors are letters the reviewers consider firm being labelled hostile. Which change addresses the failure most directly?

- A. Add labelled examples to the prompt, including borderline letters that sit near the firm and hostile boundary.
- B. Enable native thinking at high effort for every letter.
- C. Use structured output with an enum of the three labels.
- D. Move the workload to the largest model tier.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The model does not know where the agency draws the line between firm and hostile. Boundary examples show it the line directly, which is what few-shot prompting is for.

- **B** is wrong because more reasoning does not supply the missing definition and adds cost to every letter.
- **C** is wrong because the labels are already valid. The problem is which one is chosen.
- **D** is wrong because a larger model still cannot guess an internal definition it has never seen.

</details>

### Item 8

A travel company's booking agent runs three steps for each request: classify the request type, build an itinerary that satisfies the traveller's dates, budget, visa rules and connection times, and format a confirmation. Evaluation shows that classification and formatting are accurate already, but itineraries often break one of the constraints. End-to-end latency is close to its limit. Which two configuration choices best fit? (Choose two.)

- A. Turn on high reasoning effort for all three steps so the whole pipeline is consistent.
- B. Add "think step by step" to the formatting step.
- C. Enable native thinking, or raise reasoning effort, for the itinerary step only.
- D. Remove all reasoning from the itinerary step to win back latency.
- E. Keep the classification and formatting steps at low effort without thinking.

<details>
<summary>Answer and explanation</summary>

**Answer: C and E.** Only the multi-constraint itinerary step fails, so that is where extra reasoning belongs (C). Keeping the steps that already work light protects the latency budget (E).

- **A** is wrong because it spends latency on steps that do not need it, and latency is already near its limit.
- **B** is wrong because formatting is already accurate, so extra reasoning there is wasted.
- **D** is wrong because it removes reasoning from the one step whose accuracy is failing.

</details>

### Item 9

An internal operations assistant includes eight procedures in every system prompt, each about 6,000 tokens long. Any single request needs at most one procedure. Evaluations show accuracy falls when unrelated procedures are present, and each procedure has a different owner who updates it on their own schedule. Which approach fits best?

- A. Cache all eight procedures so the repeated 48,000 tokens cost less.
- B. Merge the eight procedures into one condensed 10,000-token procedure.
- C. Include a random subset of four procedures in each request to halve the context.
- D. Package each procedure as a separate Skill, so only short descriptions load up front and the full procedure loads when a request needs it.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Skills address both stated problems. Irrelevant procedures stay out of context until they are needed, and each owner can maintain their own packaged procedure independently.

- **A** is wrong because caching lowers cost but leaves the distracting content in context, so accuracy still falls.
- **B** is wrong because condensing loses detail, and one merged document cannot have eight independent owners.
- **C** is wrong because a random subset often leaves out the one procedure the request needs.

</details>

### Item 10

A media company runs a summarisation endpoint at about one million calls a month. Each call includes a 3,000-token instruction block, twelve worked examples totalling 8,000 tokens, and an article of about 2,000 tokens. An evaluation on 500 representative articles shows that three examples give the same quality score as twelve. The instructions and examples do not change between calls. Which change cuts token spend the most without lowering quality?

- A. Move to the largest model tier so fewer summaries need to be regenerated.
- B. Reduce to the three best examples, and place the instructions and examples in a cached prefix ahead of the article.
- C. Remove the instruction block and keep all twelve examples.
- D. Send only every other paragraph of each article to halve input size.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Nine of the examples add 6,000 tokens per call with no measured quality gain, and the remaining stable content is the same every time, so it suits a cached prefix. Both changes cut spend sharply while the evaluation shows quality holds.

- **A** is wrong because a larger tier raises per-call cost, and the stem gives no evidence of regeneration problems.
- **C** is wrong because removing the instructions is untested and keeps the largest block of waste.
- **D** is wrong because dropping article paragraphs removes the information the summary depends on.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 1: Solution Design and Architecture](01-solution-design-and-architecture.md) | Next: [Domain 3: Integration](03-integration.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
