<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 7: Security and Safety](07-security-and-safety.md) · Next: [Exam overview: Architect, Foundations](../architect-foundations/README.md)
<!-- /nav:top -->

# Domain 8: Tools and MCP

Weight: 10% of scored items, about 6 items.

## What this domain measures

This domain tests whether you can give Claude good tools and connect it to systems in the right way. The guide groups it into three skills:

- **Tool design and response shaping.** Building a tool for an integration need: its function schema, a description that leads to correct calls, results shaped so the model gets a small, actionable answer, error handling, where the tool runs, and an approval pattern for risky actions. Also, putting together a tool set that performs well as a whole.
- **MCP server development and integration.** Building, deploying, and connecting an MCP server to Claude applications and workflows: its resources, tools, and prompts, the choice of transport (stdio or HTTP), and authentication.
- **Capability mechanism selection.** Comparing built-in tools, custom tools, Skills, and MCP servers for a use case, based on where the capability has to live, who maintains it, and how Claude finds and calls it, then justifying the choice.

Why it matters: the model only knows a tool through its name, description, schema, and results. A poorly shaped tool wastes tokens and causes wrong calls. A capability built in the wrong place gets copied, drifts, or cannot reach the clients that need it.

## How questions look

Tool items show a tool definition, a trace of calls and results, and a symptom: wrong tool chosen, huge results, crashes on errors, an action taken without consent. MCP items describe a server and where it needs to run or who needs to use it. Mechanism items describe a capability and several possible homes for it. The right answer fixes the tool's contract or places the capability where its users and maintainers are.

## A method for these questions

1. For tool items, check the parts in order: name, description, input schema, execution side, result shape, error path, and approval.
2. Find the weak part the symptom points to. Wrong arguments point to the description and schema. Bloated or confusing results point to response shaping. Crashes or dead ends point to error handling. Unwanted actions point to the approval pattern.
3. For tool sets, look for overlap and excess. Fewer, distinct, well-described tools beat many similar ones.
4. For MCP, decide the transport from where the server runs and who connects: a local process for one user means stdio, and a shared remote service means HTTP with authentication.
5. For mechanism selection, ask three questions: where must the capability live, who maintains it, and how will Claude discover it? Then match: built-in tool if one already does the job, custom tool for one application's own logic, Skill for packaged know-how and scripts, MCP server for a system that several clients need to reach.

## Traps that cost points

- **Returning the raw API payload.** Thousands of lines of JSON cost tokens and bury the answer. Return the fields the task needs, and page or summarise the rest.
- **Throwing on tool errors.** A crash or an empty result leaves the model guessing. Return a `tool_result` with `is_error: true` and a message that says what went wrong and what to try.
- **Adding tools to fix tool confusion.** Overlapping tools make selection harder. Merge, rename, and describe them so each has a distinct job.
- **Running a private-system tool on the wrong side.** A tool that needs your internal network has to run in your application, not as a server-side tool hosted by Anthropic.
- **Asking the model to confirm risky actions.** Consent must come from the user, through your interface, before your code executes the action.
- **Writing logs to stdout in a stdio MCP server.** stdout carries the protocol messages. Logs go to stderr.
- **Making everything an MCP tool.** Read-only context fits resources, and user-chosen templates fit prompts. Tools are for actions and queries the model decides to call.
- **Building a Skill when several clients need live data.** Skills package know-how. A live system that many clients need is an MCP server.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 8 is about 6 items, so budget roughly 10 minutes. Tool items often include a definition and a trace. Read the result or error in the trace first, because it usually tells you which part of the tool contract failed.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Tool definition | `name`, `description`, and `input_schema` (JSON Schema). The description says what the tool does, when to use it, and what each parameter means. |
| Strict tool use | `strict: true` on a tool definition makes the inputs match the schema exactly. |
| Response shaping | Return a small, relevant, structured result. Page large sets and say how to get more. |
| Tool errors | Return a `tool_result` with `is_error: true` and an actionable message. Do not drop the result or crash the loop. |
| Execution side | Client tools run in your application, which can reach your systems. Server tools, such as web search, run on Anthropic's side. |
| Approval pattern | For risky actions, the tool prepares the action, your interface asks the user, and your code executes only after confirmation. |
| Tool set design | Few, distinct tools. Give each agent only what it needs. Tool search with deferred loading helps when the catalogue is large. |
| MCP primitives | Tools are called by the model. Resources are data the application attaches as context. Prompts are templates the user chooses. |
| MCP transports | stdio for a local process launched by the client. Streamable HTTP for remote, shared servers. |
| MCP authentication | Remote HTTP servers use OAuth so each user's access is authorised. Do not share one static secret across users. |
| stdio rule | stdout is reserved for protocol messages. Log to stderr. |
| Mechanism choice | Built-in tool if it exists, custom tool for one app's logic, Skill for packaged know-how and scripts, MCP server for a system reached from many clients. |

## Practice items

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A logistics agent's `track_shipment` tool calls a carrier API. When the carrier API times out, the tool raises an exception, the harness catches it and sends back an empty string as the tool result, and the agent tells the customer the shipment does not exist. The team wants the agent to handle carrier failures sensibly. Which change fits best?

A. Return a `tool_result` with `is_error: true` and a message such as "Carrier API timed out, the shipment may exist, try again shortly or offer to follow up", so the model can respond correctly.

B. Retry the carrier call forever inside the tool until it succeeds.

C. Remove the tool's error handling so the whole request fails and the customer sees a generic error.

D. Have the tool return made-up tracking data when the carrier is unavailable, so the customer always gets an answer.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** An empty result reads as "no shipment", which is wrong. A result flagged as an error, with a message that explains the failure and suggests a next step, lets the model tell the customer the truth and offer a follow-up. The loop keeps running.

- **B** is wrong because an endless retry can hang the conversation and pile up load on a struggling API.
- **C** is wrong because failing the whole request is a worse experience and gives the model no chance to recover.
- **D** is wrong because invented data is a hallucination built into the tool.

</details>

### Item 2

A subscription app gives its support agent a `cancel_subscription` tool. In testing, the agent sometimes cancels after a customer only asks how cancellation works. The business requires that the customer explicitly confirms before any cancellation runs, and wants the rest of the conversation to stay automated. Which pattern fits best?

A. Add "only cancel when you are sure" to the tool description.

B. Have the tool prepare the cancellation and return a pending status, show the customer a confirmation step in the app, and execute the cancellation only when the customer confirms.

C. Ask the model to reply "Are you sure?" and cancel when the next message contains the word "yes".

D. Remove the tool and email the support team for every cancellation question.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Consent must come from the customer through the application, and the code must not execute until that consent arrives. A prepare-then-confirm pattern makes the confirmation a real gate. The conversation stays automated around it.

- **A** is wrong because a description is guidance, and the failure shows the model's judgment is not enough.
- **C** is wrong because matching "yes" in free text is fragile, and the model still decides when to act.
- **D** is wrong because it removes the automation the business wants to keep.

</details>

### Item 3

A developer's stdio MCP server works when he runs its functions from a test script, but when Claude Code connects to it, the connection fails with parse errors. The server code uses `print()` statements to show debug information such as "Loaded 42 records" while it handles requests. Nothing else has changed. What is the most likely fix?

A. Switch the server to Streamable HTTP, because stdio does not support debug output.

B. Add more `print()` statements to find out where the parsing fails.

C. Increase the client's connection timeout so it waits longer for valid messages.

D. Send debug output to stderr or a log file, because stdout is reserved for the protocol's messages on a stdio server.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** On stdio, the client reads protocol messages from the server's stdout. Debug text printed there gets mixed into the stream, and the client cannot parse it. Logging to stderr or a file fixes the connection without changing anything else.

- **A** is wrong because stdio supports logging fine, just not on stdout. Switching transports is unnecessary.
- **B** is wrong because more prints to stdout make the corruption worse.
- **C** is wrong because the problem is invalid content on the stream, not slowness.

</details>

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 7: Security and Safety](07-security-and-safety.md) | Next: [Exam overview: Architect, Foundations](../architect-foundations/README.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
