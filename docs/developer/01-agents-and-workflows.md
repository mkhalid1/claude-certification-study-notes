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

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A logistics company wants to automate supplier invoice handling. Every invoice goes through the same steps: extract fields, check them against the purchase order, flag mismatches over a set tolerance, and post matched invoices to the ledger. Finance auditors require a record of exactly which step produced each decision, and a wrong posting is expensive to reverse. The team is choosing between a fixed workflow and an autonomous agent. Which design fits best?

A. An autonomous agent with all four capabilities as tools, told in its system prompt to process each invoice in whatever order it judges best.

B. A workflow in which code runs the steps in a fixed order, calls Claude for extraction and mismatch explanations, and logs each step's input and output.

C. A single prompt that asks Claude to extract, validate, and post each invoice in one response, with the full purchase order list attached.

D. An orchestrator agent that spawns a new subagent for each invoice and lets each subagent decide which checks are needed.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The steps are known in advance, errors are costly, and the auditors need to see which step made each decision. A workflow your code controls gives a fixed order and a clean log per step. Claude is still used where it adds value, in extraction and explanation.

- **A** is wrong because letting the model choose the order adds unpredictability to a fixed process and makes the audit trail harder to reconstruct.
- **C** is wrong because one combined response mixes the steps together, so there is no per-step record and no chance to stop a bad posting.
- **D** is wrong because per-invoice agents that decide their own checks add cost and variation where the checks should always be the same.

</details>

### Item 2

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

### Item 3

A sales-assist agent built with the Claude Agent SDK drafts and sends customer emails. Compliance requires that the `check_compliance` tool runs on every draft before `send_email` is called. The system prompt states this rule in bold, but an audit of traces finds a small number of emails that were sent without a compliance check. The team needs the rule to hold every time. What should they change?

A. Add a pre-tool-use hook on `send_email` that blocks the call unless a passing compliance check has been recorded for that draft.

B. Repeat the compliance rule at the end of the system prompt and in every user message, so the model sees it more often.

C. Lower the effort level so that the model takes fewer, more consolidated actions and is less likely to skip steps.

D. Remove `check_compliance` as a separate tool and ask the model to judge compliance itself before sending.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The requirement is "every time", and a prompt instruction cannot guarantee that. A hook that runs before `send_email` is code, so it blocks an unchecked send no matter what the model decides. The model can still be told about the rule, but enforcement moves out of its hands.

- **B** is wrong because repeating the instruction may reduce misses but cannot eliminate them.
- **C** is wrong because effort changes how much the model thinks and acts. It does not enforce an ordering rule.
- **D** is wrong because it replaces a checkable tool with the model's own judgment, which is weaker and leaves no record.

</details>

### Item 4

A developer writes a custom agent loop on the Messages API for a documentation assistant. The loop is `while True:` and it sends the next request after every response, appending tool results when there are any. Traces show that after Claude gives a complete final answer, the loop keeps sending requests, and in some runs it alternates between the same two searches for hundreds of turns until the job is killed. The developer wants the loop to end correctly and never run away again. Choose two.

A. Continue the loop only while `stop_reason` is `tool_use`, and return the final answer when it is `end_turn`, handling any other stop reason explicitly.

B. Raise `max_tokens` so that Claude can fit its whole answer and all its searches into a single response.

C. Add a maximum number of iterations or a token budget that ends the run gracefully and reports where it stopped.

D. Remove the search tool so that Claude has no way to keep calling it.

E. Add a sentence to the system prompt asking Claude to stop searching when it has enough information, and keep the loop as it is.

<details>
<summary>Answer and explanation</summary>

**Answer: A and C.** A tool-use loop should continue only while Claude asks for a tool, so checking `stop_reason` is the correct exit. A hard cap on iterations or tokens is the backstop for cases where the model keeps calling tools without making progress. Together they end normal runs correctly and stop runaway ones.

- **B** is wrong because the loop ignores the stop signal. A larger output limit does not change that.
- **D** is wrong because it breaks the assistant's main function to hide a loop bug.
- **E** is wrong because the loop ignores `end_turn` even after a complete answer. A prompt change cannot fix the code's missing exit.

</details>

### Item 5

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

### Item 6

A startup wants to offer an agent that analyses spreadsheets that customers upload and writes and runs code to produce charts. The generated code must run in an isolated environment, separate for each customer session. The team is three developers with no one to run or secure container infrastructure, and they want to launch soon. Which execution environment fits best?

A. A self-hosted harness that runs the generated code directly on the team's application servers, with a prompt instruction to avoid harmful code.

B. A managed agent runtime that runs the agent loop and provides a sandboxed workspace for each session, such as Claude Managed Agents.

C. A self-hosted harness on a single shared virtual machine that every customer's session uses for code execution.

D. No code execution at all: ask Claude to describe the charts in words and let customers build them in their own spreadsheet tools.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The binding constraints are isolated execution per session and no capacity to run infrastructure. A managed, sandboxed runtime provides both, because Anthropic runs the loop and a separate workspace for each session. The team can focus on the agent itself.

- **A** is wrong because running generated code on application servers has no isolation, and a prompt instruction is not a sandbox.
- **C** is wrong because a shared machine mixes customers' sessions and data, and the team still has to operate and secure it.
- **D** is wrong because it drops the core feature to avoid an infrastructure decision.

</details>

### Item 7

A customer-success agent helps account managers prepare for calls. Account managers complain that every new session starts from zero: the agent forgets a client's preferred contact times and past escalations, even though it learned them last week. Each session currently begins with only the system prompt and the new request. Within a session, the agent behaves well. Which pattern is missing?

A. Subagent isolation, so that each client gets a separate subagent within the session.

B. A larger context window, so that last week's session fits into this week's conversation.

C. A stricter loop termination rule, so that sessions end sooner and lose less information.

D. Memory: store durable facts about each client outside the conversation and load the relevant ones when a session for that client starts.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The failure is knowledge lost between sessions, and nothing carries it across today. A memory store keeps durable facts outside any single conversation and brings back the relevant ones when needed. Sessions stay small, and the knowledge persists.

- **A** is wrong because isolation keeps work apart within a run. It does not keep anything after the run ends.
- **B** is wrong because context belongs to one request. Last week's session is not in it unless something stores and reloads it.
- **C** is wrong because ending sessions sooner does not help remember anything across them.

</details>

### Item 8

A team defines a `code-reviewer` subagent with the Claude Agent SDK. It is supposed to read changed files and report issues, never edit them. In testing, the reviewer occasionally rewrites files it disagrees with, because its definition grants it the same Write, Edit, and Bash tools as the main agent. Separately, the main agent rarely delegates to the reviewer, because its description just says "Helps with code." Choose two.

A. Keep the full tool set and add a line to the reviewer's prompt saying it must never modify files.

B. Restrict the reviewer's tools to read-only ones such as file reading and search, so it cannot edit files at all.

C. Give the reviewer every tool the main agent has, plus permission to approve its own changes, so it can fix the issues it finds.

D. Run the reviewer in a permission mode that skips all approval prompts, so it finishes faster.

E. Rewrite the reviewer's description to say when to use it, for example after code changes and before a commit, and what it returns.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** Limiting the reviewer to read-only tools makes "never edit" a property of its definition, not a request. A specific description tells the main agent when to delegate and what to expect back, which fixes the missing delegation. Each change addresses one of the two problems.

- **A** is wrong because a prompt line can be ignored while the write tools remain available.
- **C** is wrong because it widens the reviewer's privileges, the opposite of the requirement.
- **D** is wrong because skipping approvals removes a safeguard and does not address either problem.

</details>

### Item 9

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

### Item 10

A migration agent upgrades a large codebase file by file. Each step runs the test suite with a shell tool, and every test run returns several thousand lines of output. After about 150 tool calls, the agent starts ignoring its migration plan and eventually fails because the request exceeds the context window. The early test outputs are no longer relevant once those files pass. Which change best addresses the failure?

A. Keep the tool output small and clear out old tool results: have the test tool return only failures and a summary, and drop stale outputs from earlier steps.

B. Restart the agent from the beginning every 100 tool calls, with the same history replayed into the new run.

C. Move the migration plan to the end of every tool result so the agent always sees it last.

D. Switch to a smaller, faster model so that each step completes before the context fills up.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The context is filling with test output that has stopped being useful. Shaping the tool result down to what matters, and clearing old tool results, keeps the plan and the recent state in view and the request within limits. This is context-window management, which the agent was missing.

- **B** is wrong because replaying the same history brings the same bloat straight back.
- **C** is wrong because it adds more text to every result and does not remove the cause.
- **D** is wrong because the model's speed has nothing to do with how fast the context fills, which depends on the tool output size.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Exam overview: Developer, Foundations](README.md) | Next: [Domain 2: Applications and Integration](02-applications-and-integration.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
