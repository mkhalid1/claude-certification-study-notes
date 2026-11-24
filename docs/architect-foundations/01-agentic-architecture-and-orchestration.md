<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Exam overview: Architect, Foundations](README.md) · Next: [Domain 2: Tool Design & MCP Integration](02-tool-design-and-mcp-integration.md)<!-- /nav:top -->

# Domain 1: Agentic Architecture & Orchestration

Weight: 27% of scored items, about 16 items.

## What this domain measures

This is the largest domain. It checks whether you can build agents that run themselves correctly and fail safely.

- **The agentic loop.** Send a request, read `stop_reason`, run the tools Claude asked for when it is `"tool_use"`, append the results to the conversation, and stop when it is `"end_turn"`. Know the anti-patterns: parsing the model's wording to decide when to stop, using an iteration cap as the main stop condition, or treating "the reply contains text" as "the task is done". Know the difference between letting the model choose the next tool and hard-coding a decision tree.
- **Coordinator and subagents.** A hub-and-spoke design where the coordinator decomposes the task, decides which subagents a given query actually needs, routes all communication, handles errors and aggregates results. It may loop: check the synthesis for gaps, send targeted follow-up work, synthesize again. Decomposition that is too narrow leaves parts of a broad topic uncovered.
- **Spawning and context passing.** Subagents are spawned with the Task tool, so the coordinator's `allowedTools` must include `"Task"`. Each subagent type is described by an AgentDefinition (description, system prompt, tool restrictions). Subagents start with isolated context: anything they need, including earlier agents' findings, must be in their prompt, ideally as structured data that keeps content apart from metadata such as source URLs and page numbers. Parallel work means several Task calls in one coordinator response. Delegation prompts should state goals and quality criteria, not step-by-step procedures.
- **Enforcement and handoff.** Prompts are probabilistic. When an order of operations must hold every time (identity verification before money moves), use programmatic prerequisites or hooks. Split multi-concern requests into separate items, investigate them with shared context, then reply once.
- **Hooks.** PostToolUse hooks transform tool results before the model sees them (for example, normalizing timestamps and status codes from different MCP tools). Hooks on outgoing tool calls can block a policy-violating action and redirect to another workflow, such as human escalation.
- **Task decomposition.** Fixed prompt chains suit predictable, multi-aspect work (per-file analysis, then a cross-file pass). Adaptive decomposition suits open-ended investigation: map the structure, find the high-impact areas, plan, and re-plan as you learn.
- **Sessions.** Resume a named session with `--resume <session-name>`, tell a resumed session exactly which files changed, branch with `fork_session` to compare approaches from one baseline, and start fresh with a structured summary when the old tool results are stale.

Why it matters: most production agent failures are architecture failures (a loop that stops early, a subagent that never got the data, a rule the model followed 97% of the time), and they are cheap to prevent at design time.

## How questions look

Most stems describe a running system and a symptom: logs show the loop ending early, a subagent returning thin results, refunds slipping past a rule, a resumed session giving stale answers. You are asked for the most effective fix or the most likely root cause. The four options usually include one that fixes the mechanism (loop control, Task configuration, a hook, explicit context), one that adds a prompt instruction where a guarantee is needed, one that over-engineers (a new classifier, a rewrite), and one that treats a neighbouring problem. Multiple-response items often ask for the two changes that give deterministic behaviour, or the two design choices that match a stated goal.

## A method for these questions

1. Find the deciding constraint in the stem. Words like "must never", "every time", "financial", "compliance" mean you need code-level enforcement, not a better prompt.
2. Locate the failure on the loop: request, `stop_reason`, tool execution, result appended, next request. Many items break exactly one link.
3. For multi-agent stems, ask what each subagent was actually given. Isolated context is the usual root cause of "the subagent did not know".
4. Check who talks to whom. If an option lets subagents talk to each other directly or skips the coordinator, it loses observability and consistent error handling.
5. Match the decomposition style to the task: predictable steps get a chain, open-ended investigation gets an adaptive plan.
6. For sessions, decide how much of the prior context is still true. Mostly true: resume and name the changes. Mostly stale: start fresh with a summary. Two directions from one point: fork.
7. Prefer the smallest change that fixes the root cause over infrastructure that would also work.

## Traps that cost points

- **Trusting a system prompt for hard rules.** "Always verify identity first" in capitals still fails sometimes. If a mistake costs money or breaks compliance, the answer is a hook or prerequisite gate.
- **Stopping when the reply has text.** Claude often writes a sentence and requests a tool in the same response. Only `stop_reason` tells you whether to continue.
- **Treating an iteration cap as the stop condition.** A cap is a safety net. As the main mechanism it cuts tasks off mid-work or lets them spin.
- **Assuming subagents share memory.** They do not inherit the coordinator's history or each other's results. If it is not in the prompt, they do not have it.
- **Spawning in sequence when the work is independent.** Parallelism comes from multiple Task calls in one coordinator response, not one call per turn.
- **Writing procedural scripts for subagents.** Step-by-step instructions make subagents brittle. Give the goal and the quality bar.
- **Always running the full pipeline.** A coordinator should pick the subagents a query needs. Running everything for a simple lookup wastes time and tokens.
- **Resuming a session whose tool results are stale.** After large code changes, the old file reads mislead the agent. A fresh session seeded with a summary is more reliable.

## Timing

Guidance only: the exam gives 120 minutes for 60 items, so about 2 minutes per item. Domain 1 stems are often the longest because they describe architectures and logs. Read the last sentence of the stem first so you know what is being asked, then read the scenario looking for the deciding constraint. If two options both look right, ask which one guarantees the outcome rather than encourages it, and move on within 3 minutes.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Loop control | Continue on `stop_reason` `"tool_use"`, stop on `"end_turn"`. Append tool results before the next request. |
| Loop anti-patterns | Parsing natural language for "done", iteration caps as the primary stop, "has text" as completion. |
| Task tool | The mechanism for spawning subagents. The coordinator's `allowedTools` must include `"Task"`. |
| AgentDefinition | Per-subagent description, system prompt and tool restrictions. |
| Isolated context | Subagents do not inherit parent history or share memory between invocations. Pass findings in the prompt. |
| Structured handoff between agents | Keep content separate from metadata (URL, document name, page) so attribution survives. |
| Parallel subagents | Multiple Task calls in a single coordinator response. |
| Hub and spoke | All subagent communication goes through the coordinator: observability, consistent errors, controlled flow. |
| Iterative refinement | Coordinator checks synthesis for gaps, re-delegates targeted queries, re-synthesizes. |
| Programmatic enforcement | Hooks and prerequisite gates give deterministic ordering. Prompts have a non-zero failure rate. |
| PostToolUse hook | Transforms tool results (for example, mixed timestamp formats) before the model reasons over them. |
| Tool-call interception hook | Blocks a policy-violating call and redirects, for example to human escalation. |
| Multi-concern requests | Split into items, investigate each with shared context, reply with one unified resolution. |
| Prompt chaining vs adaptive | Chain for predictable multi-aspect reviews. Adaptive plans for open-ended investigation. |
| `--resume <session-name>` | Continue a named prior session. Tell it which files changed. |
| `fork_session` | Independent branches from one shared analysis baseline. |
| Fresh start with summary | Better than resuming when prior tool results are stale. |

## Practice items

These 5 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A logistics company runs an Agent SDK agent that answers shipment questions using three MCP tools. The loop code ends the conversation as soon as Claude's response contains a text block. In production, about a third of conversations end after the agent says "Let me check the carrier status for you" and nothing else happens. The tool request was present in the same response but was never executed. What is the correct fix?

A. Raise the loop's maximum iteration count so the agent has more turns to finish.
B. Continue the loop while `stop_reason` is `"tool_use"`, execute the requested tools and append their results, and end only when `stop_reason` is `"end_turn"`.
C. Instruct the agent to end every final answer with the word DONE, and stop the loop only when that word appears.
D. Remove text blocks from responses that also contain tool requests, so the existing check no longer fires early.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The loop's stop signal is wrong. `stop_reason` tells you whether Claude wants tools run (`"tool_use"`) or has finished (`"end_turn"`), and a response can carry text and a tool request together. Branching on `stop_reason` and appending tool results fixes the root cause.

- **A** is wrong because the loop is exiting on the first turn. More allowed iterations change nothing, and an iteration cap should never be the main stop mechanism.
- **C** is wrong because parsing natural language for a completion signal is a named anti-pattern. The model may omit or misplace the word.
- **D** is wrong because it hides the symptom by discarding useful content and still leaves loop termination tied to content checks instead of `stop_reason`.

</details>

### Item 2

A due-diligence platform uses a coordinator, a web search subagent, a document analysis subagent and a synthesis subagent. The coordinator calls the synthesis subagent with the prompt "Synthesize the findings from the search and analysis agents into a cited summary." The summaries are vague, and many citations point to sources nobody retrieved. The search and analysis subagents both returned good results to the coordinator. What is the best fix?

A. Enable a shared memory setting so the synthesis subagent can read the other subagents' results directly.
B. Give the synthesis subagent web search tools so it can find its own sources.
C. Move synthesis into the coordinator so it has the full conversation history.
D. Put the complete findings from both subagents into the synthesis prompt as structured records, each with the claim, the excerpt and its source metadata.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Subagents start with isolated context and do not inherit anything automatically, so the synthesis subagent never saw the findings and invented sources. Passing the full findings in its prompt, with content kept separate from metadata such as URLs and document names, gives it the material and preserves attribution.

- **A** is wrong because subagents do not share memory between invocations, and there is no such setting to switch on. Context must be passed explicitly.
- **B** is wrong because it hands a synthesis agent tools outside its role, which leads to misuse and duplicates work already done.
- **C** is wrong because it abandons the specialist design and loads the coordinator with verbose content, when the real problem is simply missing context in one prompt.

</details>

### Item 3

A telecom support agent receives this message: "I was charged twice for October, my new router still hasn't arrived, and please change my billing address." The agent investigates the double charge, explains it well, and ends the conversation. The customer has to write again about the router and the address. What design change best addresses this?

A. Escalate any message that contains more than one issue to a human agent.
B. Reply asking the customer to send each issue as a separate message.
C. Have the agent split the request into distinct items, investigate each one using the shared conversation context, then reply with one combined resolution.
D. Start a separate agent session for each issue, each with no knowledge of the others.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Multi-concern requests should be decomposed into distinct items, investigated (in parallel where possible) with shared context, and resolved in one unified reply. That keeps first-contact resolution high and catches links between issues, such as the same account.

- **A** is wrong because these are routine issues the agent can handle. Escalating on issue count lowers resolution rates for no reason.
- **B** is wrong because it pushes the agent's job onto the customer and adds contacts.
- **D** is wrong because separate sessions lose the shared context (same customer, same account) and produce three disconnected answers.

</details>

### Item 4

An engineering lead has two workflows to automate. The first is a weekly review of every changed infrastructure config file against the same five-point checklist. The second is investigating why a nightly import intermittently creates duplicate rows, with no known cause yet. Which pairing of decomposition approaches fits best?

A. Dynamic, adaptive decomposition for both, so each run can choose its own steps.
B. A fixed prompt chain for both, so results are predictable and easy to audit.
C. Dynamic decomposition for the config review, and a fixed prompt chain for the duplicate-row investigation.
D. A fixed prompt chain for the config review, and adaptive decomposition for the duplicate-row investigation.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The config review is predictable and multi-aspect, so a fixed sequence of focused steps suits it. The duplicate-row problem is open-ended: the next step depends on what each step finds, so it needs an adaptive plan that creates subtasks as it learns.

- **A** is wrong because adaptive decomposition adds variability to a checklist task that should run the same way every week.
- **B** is wrong because a fixed chain cannot follow the leads an unknown bug produces.
- **C** is wrong because it reverses the fit in both cases.

</details>

### Item 5

A consulting firm's research coordinator runs every request through all four subagents (search, document analysis, synthesis, report writing), even simple lookups like "What year was this regulation enacted?" The coordinator's delegation prompts are long step-by-step procedures, and subagents struggle when a source doesn't fit the steps. Which two changes best follow coordinator design guidance? (Choose two.)

A. Have the coordinator analyze each query and invoke only the subagents that query needs.
B. Keep the full pipeline for every query so all outputs share the same structure.
C. Make the step-by-step procedures even more detailed to cover more source types.
D. Rewrite delegation prompts to state the research goal and quality criteria, and let subagents decide how to meet them.
E. Let subagents send results directly to each other to cut out coordinator round trips.

<details>
<summary>Answer and explanation</summary>

**Answer: A and D.** A coordinator should choose subagents based on query complexity instead of always running the full pipeline. Delegation prompts that state goals and quality criteria let subagents adapt to sources a fixed procedure did not anticipate.

- **B** is wrong because it spends time and tokens on simple lookups that need one subagent or none.
- **C** is wrong because longer procedures make subagents more brittle, which is the problem already observed.
- **E** is wrong because routing all communication through the coordinator is what gives observability, consistent error handling and controlled information flow.

</details>

Want more? The free timed Architect, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect), which reports percent correct by domain.

---

---

---

<!-- nav:bottom -->← Previous: [Exam overview: Architect, Foundations](README.md) | Next: [Domain 2: Tool Design & MCP Integration](02-tool-design-and-mcp-integration.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
