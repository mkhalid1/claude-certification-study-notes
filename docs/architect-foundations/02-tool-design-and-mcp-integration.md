<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 1: Agentic Architecture & Orchestration](01-agentic-architecture-and-orchestration.md) · Next: [Domain 3: Claude Code Configuration & Workflows](03-claude-code-configuration-and-workflows.md)<!-- /nav:top -->

# Domain 2: Tool Design & MCP Integration

Weight: 18% of scored items, about 11 items.

## What this domain measures

This domain is about the interface between Claude and everything it can act on.

- **Tool descriptions.** The description is the main thing Claude uses to choose a tool. A good one states the purpose, the input format, example queries, edge cases, and when to use it instead of a similar tool. Overlapping descriptions cause misrouting. Fixes include rewriting descriptions, renaming tools so their names match their real purpose, and splitting a generic tool into purpose-specific tools with clear input and output contracts. System prompt wording can also pull Claude toward the wrong tool through keyword associations, so review it alongside the descriptions.
- **Structured errors.** MCP tools signal failure with the `isError` flag. Useful errors also say what kind of failure it was (transient, validation, business rule, permission), whether a retry could help (an `isRetryable` boolean), and a readable description, including a customer-friendly explanation for business rule violations. A generic "Operation failed" leaves the agent unable to choose a recovery. A failed lookup and a successful lookup that found nothing are different results and must look different. Subagents should recover from transient failures locally and pass up only what they cannot fix, with partial results and what was tried.
- **Tool distribution and `tool_choice`.** Too many tools on one agent (the guide contrasts 18 with 4 to 5) makes selection less reliable, and agents misuse tools outside their role. Scope each agent's tools to its job, add a narrow cross-role tool only for frequent needs, and swap generic tools for constrained ones. Know the three `tool_choice` settings: `"auto"` (Claude may answer in text), `"any"` (Claude must call some tool), and forced selection with `{"type": "tool", "name": "..."}` (Claude must call that tool).
- **MCP servers in Claude Code and agents.** Shared team servers go in project-scoped `.mcp.json`, with credentials supplied through environment variable expansion so no secret is committed. Personal or experimental servers go in user-scoped `~/.claude.json`. Tools from all configured servers are discovered at connection time and are available together. MCP resources expose content catalogs (issue summaries, documentation trees, database schemas) so the agent can see what exists without exploratory calls. Use an existing community server for standard integrations and reserve custom servers for team-specific workflows. If the agent keeps using a built-in tool such as Grep instead of a more capable MCP tool, improve the MCP tool's description.
- **Built-in tools.** Grep searches file contents. Glob matches file paths by pattern. Read and Write handle whole files. Edit makes targeted changes by matching unique text, and when the anchor text is not unique, Read followed by Write is the reliable fallback. Build understanding incrementally: Grep for entry points, then Read to follow imports, instead of reading everything up front. To trace usage through wrapper modules, list the exported names first, then search for each.

Why it matters: an agent can only be as good as its tools' contracts. Most "the model picked the wrong thing" complaints in production trace back to a vague description, a crowded tool list or an error that carried no information.

## How questions look

Stems usually describe tool behaviour seen in logs: the wrong tool chosen for a request type, retries that never succeed, an agent telling customers there is no data when the backend was down, a teammate missing an MCP server, the agent grepping when a richer tool exists. Options mix a description or contract fix, a configuration fix, an infrastructure-heavy alternative (routing layers, classifiers, consolidating everything into one tool), and a prompt-only workaround. Multiple-response items often ask for two configuration steps or two tool choices that together meet the requirement.

## A method for these questions

1. Decide whether the problem is selection (which tool), contract (what it accepts and returns), error signalling, access (which agent has which tools), or configuration (where a server is defined).
2. For selection problems, look first at the descriptions and names of the tools involved, then at any system prompt wording that mentions them.
3. For error problems, ask what the agent needed to know to recover: the category, whether to retry, and what to tell the user.
4. Check whether "nothing found" and "could not look" are being returned the same way.
5. For access problems, count tools per agent and check for tools outside the agent's role.
6. For configuration, ask who needs the server (team or one person) and whether a secret would end up in version control.
7. Choose the lowest-effort fix that addresses the root cause. Routing layers and consolidations are rarely the first step.

## Traps that cost points

- **Fixing tool selection with few-shot examples first.** Examples can help, but when descriptions are minimal, the description is the root cause and the cheaper fix.
- **Ignoring the system prompt.** A line like "always search the knowledge base" can override well-written descriptions through keyword association.
- **Returning one generic error.** "Operation failed" makes the agent retry validation errors forever and apologise vaguely for policy refusals.
- **Treating a timeout as an empty result.** The agent then tells users confidently that no records exist.
- **Giving every agent every tool.** Selection reliability drops as the list grows, and specialists start doing other agents' jobs.
- **Using `"any"` when a specific tool must go first.** `"any"` forces a tool call but lets Claude pick which. Forced selection names the tool.
- **Committing tokens to `.mcp.json`, or putting team servers in `~/.claude.json`.** The first leaks secrets. The second means teammates never get the server.
- **Retrying Edit with the same anchor text.** If the text is not unique, it will fail again. Read the file and Write the full new content.

## Timing

Guidance only: the exam gives 120 minutes for 60 items, so about 2 minutes per item. Items in this domain are often shorter and more factual (which file, which setting, which tool). Aim to answer those in about a minute and bank the time for the longer architecture items in Domain 1.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Tool descriptions | Main signal for tool selection. Include purpose, inputs, example queries, edge cases, and when to use it versus similar tools. |
| Overlap fixes | Rewrite descriptions, rename tools to match purpose, split generic tools into specific ones with defined contracts. |
| System prompt keywords | Can create unintended tool associations. Review them with the descriptions. |
| `isError` | MCP flag that marks a tool result as a failure. |
| Error categories | Transient, validation, business rule, permission. Each needs a different recovery. |
| `isRetryable` | Stops wasted retries on errors that will never succeed. |
| Business rule errors | Not retryable, with a customer-friendly explanation the agent can relay. |
| Empty vs failed | A successful query with no matches is not an access failure. Report them differently. |
| Local recovery | Subagents retry transient failures themselves and escalate only unresolved errors, with partial results and what was tried. |
| Tool count | Fewer, role-specific tools per agent give more reliable selection. |
| `tool_choice` | `"auto"` may return text, `"any"` must call some tool, `{"type": "tool", "name": ...}` must call that tool. |
| `.mcp.json` | Project scope, shared through version control. Use environment variable expansion for credentials. |
| `~/.claude.json` | User scope, for personal or experimental servers. |
| Multiple servers | Tools from all configured servers are discovered at connection and available at the same time. |
| MCP resources | Expose catalogs (schemas, issue lists, doc trees) to cut exploratory tool calls. |
| Grep vs Glob | Grep searches contents. Glob matches file paths, for example `**/*.test.tsx`. |
| Edit fallback | When anchor text is not unique, Read then Write. |

## Practice items

These 5 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A software company's internal help agent has two tools: `search_wiki` ("Searches documentation") and `search_kb` ("Searches knowledge"). The wiki holds engineering runbooks and the knowledge base holds customer-facing help articles. The system prompt says "Always check knowledge before answering any question." Logs show the agent sends almost every engineering question to `search_kb` and returns customer help articles. Which two changes most directly fix the misrouting? (Choose two.)

A. Rewrite both descriptions to state what each source contains, example queries, the input format, and when to use one instead of the other.
B. Add a third tool that searches both sources at once and merges the results.
C. Reword the system prompt line so it no longer ties the word "knowledge" to every question.
D. Force `tool_choice` to `search_wiki` on every request.
E. Add a keyword router in code that inspects each question and picks the tool before Claude sees it.

<details>
<summary>Answer and explanation</summary>

**Answer: A and C.** The descriptions are too thin to tell the tools apart, and the system prompt's wording creates a keyword pull toward `search_kb`. Fixing both removes the two causes of misrouting.

- **B** is wrong because merging sources adds a new overlapping tool and mixes runbooks with customer articles instead of fixing selection.
- **D** is wrong because forcing one tool breaks every question that genuinely belongs in the knowledge base.
- **E** is wrong because a keyword router is extra infrastructure that bypasses Claude's language understanding when the descriptions have not even been fixed yet.

</details>

### Item 2

A retail support agent calls an MCP tool, `update_order`, that returns `{"isError": true, "content": "Operation failed"}` for every kind of failure. Logs show the agent retrying malformed postcode requests five times in a row, and telling customers "please try again later" when an order cannot be changed because it has already shipped. What change best fixes both behaviours?

A. Add a system prompt rule telling the agent to retry at most twice.
B. Have the tool return free-text error messages copied from the backend's stack traces.
C. Remove the `isError` flag so failures are treated as normal responses the agent can read.
D. Return structured error metadata: an error category (such as validation or business rule), an `isRetryable` boolean, and a readable description, including a customer-friendly explanation for policy refusals.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A uniform error hides the information the agent needs. With a category and a retryable flag, the agent can fix and resend a validation error, stop retrying a business rule refusal, and explain the refusal to the customer in plain words.

- **A** is wrong because a retry cap still wastes retries on errors that can never succeed and does nothing for the misleading "try again later" message.
- **B** is wrong because raw stack traces are not reliably interpretable and are not suitable to relay to customers.
- **C** is wrong because removing the failure signal makes things worse. The agent can no longer reliably tell a failure from a success.

</details>

### Item 3

A media company built one agent that handles intake, research, fact-checking, drafting and publishing. It has 18 tools from four MCP servers. Logs show it often calls the publishing tool while still researching and uses the web search tool to "check" its own draft. What is the best structural change?

A. Add a paragraph to the system prompt describing the correct order of tool use.
B. Split the work across role-specific subagents, each with only the handful of tools its role needs, and give a narrow cross-role tool only where one role needs it often.
C. Set `tool_choice` to `"any"` so the agent always uses a tool.
D. Merge the 18 tools into three broad tools so there are fewer choices.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Too many tools on one agent lowers selection reliability, and tools outside a role get misused. Scoping each subagent to a small, relevant tool set, with limited cross-role tools for frequent needs, addresses both symptoms.

- **A** is wrong because the problem is too many choices and no role boundaries. A prompt does not reduce either.
- **C** is wrong because it forces tool calls but does nothing to improve which tool gets chosen.
- **D** is wrong because broad generic tools have vague contracts, which creates a different selection problem.

</details>

### Item 4

A product team wants every developer's Claude Code to connect to the team's issue tracker MCP server, and the tracker needs an API token. Separately, one developer wants to try an experimental MCP server without affecting anyone else. Which two configuration choices are correct? (Choose two.)

A. Paste the tracker token directly into the project's `.mcp.json` so it works on clone.
B. Define the tracker server in the project-scoped `.mcp.json` and supply the token through environment variable expansion.
C. Ask each developer to add the tracker server to their own `~/.claude.json`.
D. Configure the experimental server in that developer's user-scoped `~/.claude.json`.
E. Add the experimental server to the project `.mcp.json` with a comment saying it is optional.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Shared team tooling belongs in the project-scoped `.mcp.json`, and environment variable expansion keeps the secret out of version control. Personal or experimental servers belong in the user-scoped `~/.claude.json`, which affects only that developer.

- **A** is wrong because it commits a secret to the repository.
- **C** is wrong because per-user setup is not shared through version control and drifts between developers.
- **E** is wrong because anything in the project `.mcp.json` reaches every teammate, which is what the developer wanted to avoid.

</details>

### Item 5

A developer productivity agent must find every test file matching `**/*.spec.ts` in a large repository and then update one configuration line in a shared setup file. The Edit call on the setup file fails because the exact line it anchors on appears three times in the file. Which two actions are correct? (Choose two.)

A. Use Grep with the pattern "spec.ts" to list the test files.
B. Retry Edit with the same anchor text.
C. Use Glob with the pattern `**/*.spec.ts` to find the test files.
D. Use Bash to print the contents of every file in the repository and look for test files.
E. Read the setup file, then Write the full updated file content.

<details>
<summary>Answer and explanation</summary>

**Answer: C and E.** Glob matches file paths by pattern, which is the job of finding files by name. When Edit cannot find unique anchor text, reading the whole file and writing back the complete updated version is the reliable fallback.

- **A** is wrong because Grep searches file contents, not file names. It would list files that merely mention "spec.ts" in their text and miss spec files that never contain that string.
- **B** is wrong because the anchor is still not unique, so the same Edit fails again.
- **D** is wrong because dumping every file floods the context and is a slow, indirect way to do what Glob does directly.

</details>

Want more? The free timed Architect, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect), which reports percent correct by domain.

---

---

---

<!-- nav:bottom -->← Previous: [Domain 1: Agentic Architecture & Orchestration](01-agentic-architecture-and-orchestration.md) | Next: [Domain 3: Claude Code Configuration & Workflows](03-claude-code-configuration-and-workflows.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
