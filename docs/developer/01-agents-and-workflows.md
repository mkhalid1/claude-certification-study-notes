<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Exam overview: Developer, Foundations](README.md) · Next: [Domain 2: Applications and Integration](02-applications-and-integration.md)<!-- /nav:top -->

# Domain 1: Agents and Workflows

Weight: 17% of scored items, about 10 items.

## What this domain measures

This domain tests whether you can choose the right shape for a Claude system and then build it so it behaves predictably. The guide groups it into three skills:

- **Architecture and runtime selection.** Deciding whether a fixed workflow or an agent fits the business requirements, the error tolerance, and the audit needs. It also covers how to split work between an orchestrator and subagents, and where the agent runs: a harness you host yourself, or a managed, sandboxed agent runtime.
- **Agent SDK construction and hooks.** Writing and fixing Claude Agent SDK agent definitions, custom agent loops and harnesses, and hook configurations, so that required actions run every time because code enforces them, not because the model chose to.
- **Agent pattern diagnosis.** Reading a trace or a description of a misbehaving agent and naming the pattern that is missing or misapplied: loop termination, subagent isolation, memory, or context-window management. Then choosing the pattern that fixes it.

Why it matters: an agent where a workflow would do costs more, is harder to audit, and fails in less predictable ways. An agent without enforced guardrails will eventually skip a step that matters.

## How questions look

Most stems describe a business process or a running agent and give you one deciding constraint: "every step must be auditable", "the steps are not known in advance", "the check must never be skipped", "the team has no one to run infrastructure". Diagnosis items show a trace pattern, such as the same tool called over and over, a coordinator that forgets the task, or subagents that overwrite each other. Options mix architecture changes, prompt changes, and code changes. The right answer is usually the smallest structural change that addresses the stated constraint or the named failure.

## A method for these questions

1. Find the deciding constraint. Underline words like auditable, deterministic, never, must, open-ended, unknown steps, no ops team.
2. For architecture items, ask whether the path can be written down in advance. If yes, prefer a workflow your code controls. If the steps depend on what the model discovers, an agent fits, as long as errors can be caught and recovered.
3. For "must always happen" requirements, look for enforcement in code: a hook, a harness check, a tool permission. A prompt instruction is a request, not a guarantee.
4. For diagnosis items, map the symptom to a pattern: endless calls point to loop termination, a forgetful coordinator to context management or isolation, lost knowledge between sessions to memory.
5. Reject options that only add capacity (bigger limits, more tokens) when the symptom is a design flaw.
6. Check that your choice does not break the agent's function or the constraint.

## Traps that cost points

- **Reaching for an agent by default.** If the steps are fixed and the audit trail must be exact, a workflow is simpler, cheaper, and easier to defend.
- **Trusting the system prompt to enforce a required step.** A model follows instructions most of the time. "Must never be skipped" calls for a hook or harness check that blocks the action.
- **Fixing an endless loop with a bigger budget.** Raising limits lets a broken loop run longer. The loop needs an exit on the stop reason and a hard cap as a safety net.
- **Letting the coordinator read everything itself.** Bulky reading fills the coordinator's context and pushes out the task. Delegate the reading to subagents that return short findings.
- **Giving subagents overlapping, vague tasks.** Overlap causes duplicate work and conflicting edits. Each subagent needs a distinct scope and a clear output.
- **Confusing memory with context.** A larger context window does not carry knowledge into tomorrow's session. Cross-session knowledge needs an external memory store.
- **Granting subagents the parent's full tool set.** A reviewer that only needs to read should only get read tools. Least privilege is part of the agent definition.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 1 is about 10 items, so budget roughly 17 minutes. Architecture items are often quick once you find the constraint. Trace-diagnosis items take longer; read the last few steps of the trace first, because that is usually where the pattern shows.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Workflow | Code decides the sequence. Predictable, testable, easy to audit. Best when the steps are known. |
| Agent | The model decides the next step and which tools to call, in a loop. Best for open-ended work where errors can be caught and recovered. |
| Orchestrator and subagents | A coordinator splits the task and delegates bounded pieces. Each subagent works in its own context and returns a condensed result. |
| Self-hosted harness | You run the loop and the compute, for example with the Claude Agent SDK on your own infrastructure. Maximum control, and you operate it. |
| Managed agent runtime | Claude Managed Agents: Anthropic runs the agent loop and a sandboxed workspace per session. Less to operate. |
| Hooks | Code that runs at fixed points in the loop, such as before a tool call. A pre-tool hook can block a call deterministically. |
| Loop termination | Continue while `stop_reason` is `tool_use`. Return the answer on `end_turn`. Handle the other stop reasons explicitly, for example `max_tokens` as incomplete. Add an iteration or budget cap as a backstop. |
| Subagent isolation | Keeps bulky intermediate work out of the coordinator's context and limits what each part can touch. |
| Memory | Durable facts stored outside the conversation and loaded when relevant, so they survive across sessions. |
| Context-window management | Prune or clear old tool output, compact long histories, and keep tool results small, so the task stays in view. |

## Practice items

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A platform team wants help investigating production incidents. An engineer describes a symptom, and the assistant needs to search logs, read recent deploy diffs, query metrics, and form and test hypotheses. The path differs every time and depends on what each search finds. Every finding is reviewed by an engineer before anyone acts on it, so a wrong lead costs only time. Which architecture fits best?

A. A fixed workflow that always runs the same log query, metric query, and diff read in that order, then asks Claude for a summary.

B. A single Messages call with every log line from the last 24 hours in context and a request to explain the incident.

C. A rules engine that maps known error codes to known fixes, with Claude used only to format the final message.

D. An agent with tools for log search, metrics, and diffs, running in a loop where Claude chooses the next query based on what it has found.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The steps cannot be written down in advance, because each next query depends on the last result. The work is complex and valuable, and errors are caught by an engineer's review. That is the case where an agent's flexibility is worth its extra cost.

- **A** is wrong because a fixed sequence cannot follow a lead that the first queries uncover.
- **B** is wrong because a day of logs may not fit in context and is costly, and a single call cannot run follow-up queries.
- **C** is wrong because new incidents do not match a known table, which is the reason the team wants an investigator.

</details>

### Item 2

A due-diligence agent must review 30 long contracts and produce a one-page risk summary. In the current design the coordinator reads every contract in full with a file tool. Around contract 18, traces show the coordinator starting to repeat earlier questions and losing track of the summary format it was given at the start. The contracts are independent of each other. Which change best addresses the failure?

A. Move to a model with a larger context window so that all 30 contracts fit alongside the instructions.

B. Ask the coordinator to read each contract twice, so that the important clauses are more likely to stay in context.

C. Have the coordinator assign each contract to a subagent that reads it in its own context and returns a short, structured list of risks.

D. Put the summary format in every tool result so that the coordinator sees it again after each file read.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The coordinator's context is filling up with raw contract text, which pushes the task and format out of view. Subagents isolate the bulky reading and return only condensed findings, so the coordinator's context stays small and focused. Because the contracts are independent, the work splits cleanly.

- **A** is wrong because a bigger window delays the problem and raises cost, while the coordinator still has to reason over a huge context.
- **B** is wrong because reading twice doubles the bloat that is causing the failure.
- **D** is wrong because it adds more text to an overloaded context and does not remove the cause.

</details>

### Item 3

A research orchestrator splits a market study across four subagents with the instructions "research the competitors", "look into pricing", "analyse the market", and "find relevant data". The final report repeats the same three competitors in every section and contradicts itself on pricing figures. Traces show that all four subagents searched for the same companies and returned long, unstructured notes. Which change best fixes the decomposition?

A. Add a fifth subagent that reads all four outputs and removes duplicate paragraphs before the report is written.

B. Raise the effort level for every subagent so that each one researches more thoroughly.

C. Give each subagent a distinct, bounded task with explicit scope and a defined output format, such as a table of named competitors with sourced price points.

D. Replace the subagents with a single agent that does all the research itself in one long context.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The vague, overlapping tasks are the cause: each subagent interpreted its brief broadly and covered the same ground. Distinct scopes stop the duplication, and a defined output format makes the results easy to combine and check against each other.

- **A** is wrong because it cleans up the symptom after paying for duplicated work, and it cannot resolve contradictions it has no way to verify.
- **B** is wrong because more thorough research on overlapping tasks produces more overlap.
- **D** is wrong because it loses the benefit of parallel, isolated work and moves all the bulk into one context.

</details>

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->← Previous: [Exam overview: Developer, Foundations](README.md) | Next: [Domain 2: Applications and Integration](02-applications-and-integration.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
