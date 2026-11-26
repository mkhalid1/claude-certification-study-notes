<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 2: Claude Models, Prompting, and Context Engineering](02-models-prompting-and-context-engineering.md) · Next: [Domain 4: Evaluation, Testing, and Optimization](04-evaluation-testing-and-optimization.md)
<!-- /nav:top -->

# Domain 3: Integration

Weight: 21% of scored items, about 13 items.

## What this domain measures

This is the heaviest domain. It tests whether you can connect Claude to real systems safely, quickly and reliably. The guide lists eight skills:

- **Least-privilege tools.** Review an agent's or subagent's tool set and decide which connections to remove, scope down, or replace with a more context-efficient access pattern.
- **Authentication and authorization.** Find credential exposure and privilege escalation risks in an integration design and name the mitigation for each.
- **The binding performance constraint.** When accuracy or latency is the dominant constraint, choose the change (retrieval depth, model tier, caching, call pattern) that relieves it at the least cost to the other.
- **Instrumentation gaps.** Explain why a failure in a scaled agentic system went unnoticed, and choose the monitoring that would have caught it.
- **Retrieval architecture.** Choose between a pre-built RAG pipeline, agentic search with tools, and full-corpus context based on the corpus, the query pattern and the latency and cost limits. When indexing, specify the contextual-retrieval and fallback configuration.
- **Connection mechanism.** Weigh MCP, direct API or CLI, and agent-to-agent handoff against latency, security and statefulness constraints.
- **Progressive discovery.** Decide between loading capabilities on demand and loading everything up front, based on context demands and latency tolerance.
- **Production reliability.** Read error traces or throughput data and specify the control (rate-limit handling, retry and backoff, idempotency for side-effecting tools, fallback routing) that fixes the pattern without creating duplicate effects.

Most production incidents in Claude systems happen at these seams, not inside the model. This domain is where an architect's experience shows.

## How questions look

Stems often include an artifact: a tool list for an agent, a description of how credentials flow, a trace excerpt, throughput numbers during a peak, retrieval evaluation results, or a corpus profile (size, update frequency, query style). The question asks which tool to remove, which risk is worst and how to fix it, which control addresses an error pattern, or which retrieval or connection design fits.

Distractors are usually real techniques applied to the wrong problem. Idempotency keys offered for a read-only call, caching offered for an accuracy problem, a bigger model offered for a missing passage, or a prompt instruction offered where an enforceable control is needed.

## A method for these questions

1. Identify the dominant constraint in the stem: security, accuracy, latency, cost, reliability or reuse. Most items have exactly one.
2. For tool and auth items, list what the task actually needs. Anything beyond that is a removal or scoping candidate. Ask where each credential lives and whether the model can see it.
3. For performance items, find which constraint has slack. The right change spends that slack to fix the binding one.
4. For retrieval items, profile the corpus: size relative to the context window, how often it changes, whether queries need exact terms, and query volume.
5. For connection items, ask who else needs this capability, whether state must carry across turns, and how many calls happen per request.
6. For reliability items, read the trace carefully. Note whether the failing call has side effects, whether retries are synchronized, and whether errors come from your limits or a dependency.
7. Reject any option that relies on a prompt instruction where code or configuration could enforce the control.

## Traps that cost points

- **Using a system prompt to restrict tools.** If a tool is connected, a confused or injected model can call it. Remove or scope the tool instead.
- **Putting credentials where the model can see them.** Keys in prompts or tool arguments can leak through outputs or injection. Credentials belong in the tool layer, out of the model's context.
- **Using one shared admin identity for all users.** It turns any user into an admin through the agent. Delegate each user's own scoped permissions.
- **Making the model bigger when retrieval missed the passage.** No model can answer from text it never received. Fix retrieval depth or quality first.
- **Monitoring only errors and latency.** Silent failures return success codes. Watch content signals such as empty-result rates and sampled output quality.
- **Retrying immediately on rate-limit errors.** Synchronized instant retries create waves. Honor retry hints, back off with jitter and smooth demand.
- **Adding idempotency where nothing is side-effecting.** Idempotency protects repeated writes. It does nothing for read-only throughput problems.
- **Defaulting to a RAG pipeline for every corpus.** A small, stable corpus that fits in context with caching often beats an index on accuracy and simplicity.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. With about 13 scored items, this domain is roughly a fifth of your score, so do not rush it. Trace items reward a slow first read: spend the extra 20 seconds finding the one detail (a success code on an empty body, a timeout after execution, a key in the prompt) that the whole item turns on.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Least privilege | Give each agent only the tools and scopes its task needs. Read-only when the task only reads. |
| Credential custody | Secrets stay in the tool or integration layer. The model never sees them. |
| Delegated identity | Act with the end user's scoped permissions, not a shared admin identity. |
| Binding constraint | Spend the slack in one dimension (often latency) to fix the binding one (often accuracy). |
| Silent failure | Success codes with wrong or empty content. Caught by content metrics and outcome sampling, not error rates. |
| Full-corpus context | Good when the corpus is small enough, changes rarely and queries span many sections. Pair with caching. |
| RAG pipeline | Good for large corpora. Contextual retrieval adds chunk context before indexing. Hybrid keyword plus semantic search helps with exact terms. |
| Agentic search | The model uses search tools iteratively. Good for exploratory queries over structured or changing sources. |
| Retrieval fallback | Define what happens when nothing relevant is found, such as keyword fallback or an honest "not found". |
| MCP | A standard way to expose tools and data to many Claude clients with consistent definitions. Strong when reuse across hosts matters. |
| Direct API or CLI | Fewest moving parts for a single consumer with tight latency. |
| Agent-to-agent handoff | Transfers a stateful task to another agent that owns its own tools and policies. |
| Progressive discovery | Load tool or Skill details only when needed. Saves context when capabilities are many and each task uses few. |
| Rate-limit handling | Honor retry hints, exponential backoff with jitter, client-side throttling. |
| Idempotency | Makes retried side-effecting calls safe by deduplicating repeats. |

## Practice items

These 4 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A software company deploys a subagent whose only job is to summarize open support tickets in one queue for a daily digest. It is connected to a ticketing MCP server that exposes read, update and delete tools for every queue, an email tool, and a database tool that runs arbitrary SQL against the support database. A security review asks for a least-privilege configuration. Which change best meets the requirement?

- A. Keep all tools and add a system prompt instruction that the subagent must only read tickets and log every tool call for review.
- B. Keep all tools and add a human approval step before any delete call.
- C. Replace the SQL tool with the ticketing system's admin API, which has fewer endpoints.
- D. Give the subagent a read-only ticket tool scoped to its one queue and remove the update, delete, email and SQL tools.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The task only reads tickets from one queue, so that is the only access it should have. Removing the write, email and SQL tools shrinks the blast radius if the subagent is confused or injected by ticket content.

- **A** is wrong because a prompt instruction does not prevent a connected tool from being called.
- **B** is wrong because it protects only deletes and leaves update, email and SQL access in place.
- **C** is wrong because an admin API is still far broader than read access to one queue.

</details>

### Item 2

A retailer runs about 300 concurrent agent sessions that handle order questions. After a vendor update, the order-lookup tool began returning an empty list with a success status for many valid orders. For three days, agents told customers that no orders were found. The monitoring stack tracked tool error rates, HTTP status codes and latency, and none of them moved. Which two monitoring additions would most likely have surfaced the failure? (Choose two.)

- A. Track the share of empty results per tool over time and alert on sudden shifts.
- B. Lower the latency alert thresholds so slower tool responses page the on-call team.
- C. Log the HTTP status code of every tool call at a finer granularity.
- D. Add CPU and memory dashboards for the agent hosts.
- E. Sample production conversations and grade their outcomes, tracking the rate of "no order found" answers.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** The failure returned success codes with empty content, so only content-level signals could show it. A jump in empty-result rate (A) and a jump in "not found" outcomes in sampled conversations (E) would both have appeared on the first day.

- **B** is wrong because latency did not change during the failure.
- **C** is wrong because the status codes were all successes, so finer logging of them shows nothing new.
- **D** is wrong because host resources were unaffected by bad tool content.

</details>

### Item 3

A large enterprise wants Claude to work with its internal incident-management system. Engineers use Claude Code, analysts use the Claude desktop app, and two teams are building custom agents, and all of them need the same set of incident tools. Security wants one place to define those tools, enforce authentication and audit usage, rather than each team writing its own client code. Which connection mechanism do these constraints favor?

- A. A direct API integration written separately inside each team's agent.
- B. A dedicated incident agent that every other agent hands conversations off to.
- C. An MCP server that exposes the incident tools to every Claude client with one definition, one auth path and central audit.
- D. A generic web-request tool that lets each Claude client call the incident system's REST endpoints with its own credentials.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The binding constraints are reuse across many different Claude hosts and one controlled point for auth and audit. MCP is designed for exposing a shared set of tools to many clients through a standard interface.

- **A** is wrong because separate integrations per team multiply the code and the auth paths that security wants to consolidate.
- **B** is wrong because handing off whole conversations to another agent is a poor fit for engineers and analysts who need tools inside their own sessions.
- **D** is wrong because a generic web tool gives the model broad, hard-to-audit access and puts endpoint details in the model's hands.

</details>

### Item 4

A telecom's sales agent chats with customers about plans. When a customer raises a billing dispute, the conversation must move to the billing team's agent, which owns refund tools, its own policies and its own audit trail. The dispute usually takes several more turns with the customer, and the customer should not have to repeat what they already said. The billing team will not let other agents hold its refund tools. Which mechanism fits these constraints?

- A. An agent-to-agent handoff that transfers the conversation and its context to the billing agent, which continues with the customer.
- B. Expose the billing agent's refund tools to the sales agent through an MCP server, so one agent can finish the whole conversation, while the billing team keeps ownership of the server.
- C. Have the sales agent call the billing system's refund API directly.
- D. End the chat and ask the customer to start a new conversation with billing.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The work is stateful and continues over several turns, and the billing team must keep sole control of its tools. A handoff moves the conversation with its context to the agent that owns those tools and policies.

- **B** is wrong because it gives the sales agent the refund tools the billing team refuses to share.
- **C** is wrong for the same ownership reason, and it bypasses billing's policies and audit trail.
- **D** is wrong because it loses the context and forces the customer to repeat everything.

</details>

Want more? The free timed Architect, Professional diagnostic on PrepClubs has the full 25 questions, scored by domain: [Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-architect-professional).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-architect-professional) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 2: Claude Models, Prompting, and Context Engineering](02-models-prompting-and-context-engineering.md) | Next: [Domain 4: Evaluation, Testing, and Optimization](04-evaluation-testing-and-optimization.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
