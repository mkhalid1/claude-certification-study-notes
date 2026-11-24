<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 7: Security and Safety](07-security-and-safety.md) · Next: [Exam overview: Architect, Foundations](../architect-foundations/README.md)<!-- /nav:top -->

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

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A sales agent has a `get_account` tool that returns the CRM's full record for an account: every field, every historic activity, and every attachment's metadata, often several thousand lines of JSON. The agent only needs the account owner, renewal date, open opportunities, and the last three activities to answer reps' questions. Traces show high token use and answers that sometimes quote the wrong renewal date from an old activity. What should the team change?

A. Raise `max_tokens` so the agent has more room to read the full record.

B. Add a system prompt instruction telling the agent to look for the renewal date carefully.

C. Shape the tool's response to return only the fields the task needs, in a clear structure, with a way to request more activity history when needed.

D. Split `get_account` into 40 tools, one per CRM field, so the agent can call exactly what it wants.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The raw record costs tokens and buries the right renewal date among old values. Returning only the needed fields, clearly labelled, gives the model a small, actionable result. A way to fetch more history covers the rare cases that need it.

- **A** is wrong because the output limit is not the problem. The input is bloated and confusing.
- **B** is wrong because an instruction does not remove the misleading old dates from the result.
- **D** is wrong because 40 narrow tools make selection harder and multiply the calls.

</details>

### Item 2

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

### Item 3

An operations agent has 60 tools, collected over a year from several teams. Many overlap: `get_user`, `fetch_user`, `lookup_customer`, and `user_details` all return similar data from different systems with slightly different fields. Traces show the agent often picks the wrong one, and each request spends many tokens on tool definitions. Choose two.

A. Add more tools with even more specific names so that each case has its own option.

B. Consolidate overlapping tools into fewer, clearly distinct tools, each with a name and description that say what it returns and when to use it.

C. Shorten every tool description to a single word so the definitions take fewer tokens.

D. Give each agent or subagent only the tools its task needs, or load rarely used tools on demand with tool search.

E. Sort the tools alphabetically in the request so the model reads them in a predictable order.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Overlap is the cause of the wrong picks, so merging duplicates and describing each remaining tool's distinct job fixes selection. Limiting each agent to the tools it needs, or loading rarely used tools on demand, cuts the definition overhead and the chance of a wrong choice.

- **A** is wrong because more similar tools make selection harder.
- **C** is wrong because one-word descriptions remove the information the model uses to choose.
- **E** is wrong because order does not resolve overlapping purposes.

</details>

### Item 4

A manufacturing company wants Claude to answer questions about machine status. The data lives in a plant database that is reachable only from inside the company's private network. A developer proposes looking for a hosted, server-side tool that Anthropic runs, so the team does not need to write tool code. Where should the tool run?

A. As a server-side tool on Anthropic's infrastructure, given the database password in the tool definition.

B. As web search, with the plant database's internal hostname listed as an allowed domain.

C. As a prompt that asks Claude to estimate machine status from its general knowledge.

D. As a client-side custom tool executed by the company's application inside its network, which queries the database and returns the result to Claude.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Only code inside the private network can reach the database. A client-side custom tool runs in the company's application, so it can query the database and send back just the result. The credentials never leave the network.

- **A** is wrong because Anthropic's infrastructure cannot reach a private database, and putting a password in a tool definition exposes it.
- **B** is wrong because web search reaches the public web, not private hosts.
- **C** is wrong because estimates from general knowledge are not machine status.

</details>

### Item 5

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

### Item 6

A developer builds an MCP server that lets Claude Code search and summarise files in a local notes folder on her laptop. Only she uses it, and Claude Code starts the server as a local process when a session opens. She asks which transport to use. Which fits best?

A. stdio, because the client launches the server as a local process and talks to it over standard input and output.

B. Streamable HTTP on a public cloud host, so the server can be reached from anywhere.

C. Streamable HTTP with OAuth, because every MCP server must authenticate its users.

D. A custom WebSocket protocol, because the standard transports do not support local servers.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** For a single-user server that the client launches on the same machine, stdio is the natural transport. Nothing has to be hosted, and no network port is exposed.

- **B** is wrong because hosting a laptop's notes server publicly adds exposure and work for no benefit.
- **C** is wrong because HTTP with OAuth is for remote, shared servers. A local stdio server does not need it.
- **D** is wrong because the standard transports already cover local servers, and a custom protocol would not work with MCP clients.

</details>

### Item 7

A platform team builds an MCP server that exposes the company's ticketing system. Two hundred employees will connect to it from Claude Code, the Claude desktop app, and an internal agent. Each user must see only the tickets they are allowed to see in the ticketing system. A teammate suggests sharing one static API token for the server in a team wiki. What should the team do?

A. Distribute the server as a stdio package that each employee runs locally with the shared static token.

B. Expose it over HTTP with the shared static token, and trust users to stay within their own tickets.

C. Deploy it as a remote server over Streamable HTTP with OAuth, so each user authorises with their own identity and the server enforces their ticket permissions.

D. Put the ticket data in a Skill that each client loads, so no server or authentication is needed.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** A shared, remote server used from several clients fits HTTP, and per-user access needs per-user authorisation. OAuth lets each employee sign in as themselves, so the server can apply their own ticket permissions. Nobody holds a shared secret.

- **A** is wrong because a shared static token gives every user the same access and spreads a secret across 200 laptops.
- **B** is wrong because trusting users is not access control, and the token would grant everyone the same view.
- **D** is wrong because a Skill packages know-how. It cannot serve live, permissioned ticket data.

</details>

### Item 8

A team is designing an MCP server for its documentation platform. They want three things: a read-only catalogue of documents that the client application can attach to a conversation as context; a few standard workflows, such as "draft a release announcement", that users pick from a menu; and a search action Claude can call during a conversation. They are deciding which MCP primitives to use. Choose two.

A. Expose the document catalogue as resources that the client application can attach as context.

B. Expose the catalogue, the workflows, and the search all as tools so that Claude decides everything.

C. Put the full catalogue into the search tool's description so Claude always has it.

D. Write the server's progress messages to stdout so the client can show them as context.

E. Expose the standard workflows as prompts that users choose in the client.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** Read-only context that the application attaches fits resources, and templates that users pick fit prompts. Search, the third need, stays a tool because Claude decides when to call it. Each need maps to the primitive built for that kind of control.

- **B** is wrong because making everything a tool hands user and application decisions to the model and ignores the primitives built for them.
- **C** is wrong because stuffing a catalogue into a tool description bloats every request and is not how context is supplied.
- **D** is wrong because stdout on a stdio server carries protocol messages. Stray output there breaks the connection.

</details>

### Item 9

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

### Item 10

A company keeps customer contract data in an internal contracts service owned by the legal-tech team. Engineers want Claude to look up live contract terms from Claude Code, analysts want the same from the Claude desktop app, and the support team's custom agent needs it too. Legal-tech wants one place to maintain the integration and its access rules. Which mechanism fits best?

A. A Skill containing an export of the contract data, copied into each team's setup.

B. An MCP server owned by legal-tech that exposes contract lookup, which each client connects to.

C. A custom tool written separately inside each of the three applications.

D. A built-in web search tool pointed at the contracts service's internal pages.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The capability must reach live data from several different clients, and one team wants to maintain it in one place. An MCP server gives every client the same interface, discovered through the protocol, with access rules enforced centrally by its owner.

- **A** is wrong because an export goes stale, and Skills package know-how, not live data with access rules.
- **C** is wrong because three separate implementations drift apart and spread the maintenance across teams.
- **D** is wrong because web search reaches the public web, not an internal service.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 7: Security and Safety](07-security-and-safety.md) | Next: [Exam overview: Architect, Foundations](../architect-foundations/README.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
