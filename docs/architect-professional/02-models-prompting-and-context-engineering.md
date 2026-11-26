<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 1: Solution Design and Architecture](01-solution-design-and-architecture.md) · Next: [Domain 3: Integration](03-integration.md)
<!-- /nav:top -->

# Domain 2: Claude Models, Prompting, and Context Engineering

Weight: 13% of scored items, about 8 items.

## What this domain measures

This domain tests whether you can get the right behavior from Claude at the right cost. The guide lists six skills:

- **Model tier selection.** Pick the tier that meets every binding latency, cost and quality constraint for a workload, and say why each rejected tier fails.
- **System prompt diagnosis.** Find the underspecification behind degraded or inconsistent output, and choose a revision that constrains scope, format and behavior without blocking legitimate variation.
- **Prompting technique.** Match the technique (zero-shot, few-shot, prompted reasoning versus native thinking, structured output) to an observed failure, without paying for tokens the task does not need.
- **Context composition and token economics.** Look at what goes into each call and how caching behaves at a given call volume, and find where tokens can be cut without losing task-relevant information.
- **Cache breakpoints and reusable packaging.** Order prompts so stable content comes before dynamic content, place breakpoints, and use modular or Skill-based packaging to maximize reuse.
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
| Compaction | Summarizes earlier context so a long conversation can continue within the window. |
| Token economics | Multiply per-call waste by call volume. Unused tool definitions and redundant examples add up fast. |

## Practice items

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

An HR assistant uses this system prompt: "You are a helpful assistant for Northwind HR. Answer employee questions." Users report that it sometimes discusses other employees' pay when asked, its answers range from one line to several pages, and it gives legal opinions on employment disputes. Which revision best fixes the problem without over-constraining it?

- A. State that it answers questions about Northwind's HR policies from the provided handbook, declines questions about other employees or legal disputes and points users to the HR portal, and leads with a short answer followed by the policy reference.
- B. Instruct it to answer only by quoting exact sentences from the handbook, to refuse any question the handbook does not answer word for word, and to keep every answer under three sentences.
- C. Add "Be careful, accurate and professional at all times" to the end of the existing prompt.
- D. Add a list of forty topics it must NEVER discuss, including colleagues' pay and legal disputes, written in capital letters at the top of the prompt, and keep the rest unchanged.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The failures come from missing scope, missing out-of-scope handling and missing format guidance. Option A supplies all three while still letting the assistant phrase answers naturally.

- **B** is wrong because it over-constrains. Many legitimate questions need a short explanation, not a verbatim quote.
- **C** is wrong because generic adjectives do not define scope or format, so the same failures continue.
- **D** is wrong because a long prohibition list is brittle, still leaves format unspecified, and misses topics nobody listed.

</details>

### Item 2

A customer-support assistant sends a 20,000-token policy document with every request. The system prompt begins with the current timestamp and the customer's name, followed by the policy document, and the team set a cache breakpoint at the end of the system prompt. Monitoring shows a cache hit rate close to zero even though the policy document never changes during the day. What is the most effective fix?

- A. Extend the cache lifetime so entries survive longer between requests, since a short lifetime is the most common reason for low hit rates.
- B. Move the timestamp and customer name after the policy document, so the stable policy forms an identical prefix, and keep the breakpoint at the end of the policy.
- C. Add several more cache breakpoints inside the policy document so smaller sections of it can be reused independently.
- D. Shorten the policy document so that the cache matters less.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The prefix changes at its very first tokens on every call because of the timestamp and name, so nothing after them can be reused. Placing stable content first and dynamic content after it makes the policy an identical prefix that the cache can serve.

- **A** is wrong because a longer lifetime does not help when every prefix is different.
- **C** is wrong because each breakpoint's prefix still begins with the changing timestamp, so none of them hit.
- **D** is wrong because it treats the symptom and may drop policy content the assistant needs.

</details>

### Item 3

An internal operations assistant includes eight procedures in every system prompt, each about 6,000 tokens long. Any single request needs at most one procedure. Evaluations show accuracy falls when unrelated procedures are present, and each procedure has a different owner who updates it on their own schedule. Which approach fits best?

- A. Cache all eight procedures in a stable prefix so the repeated 48,000 tokens cost far less on every request.
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

Want more? The free timed Architect, Professional diagnostic on PrepClubs has the full 25 questions, scored by domain: [Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-architect-professional).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-architect-professional) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 1: Solution Design and Architecture](01-solution-design-and-architecture.md) | Next: [Domain 3: Integration](03-integration.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
