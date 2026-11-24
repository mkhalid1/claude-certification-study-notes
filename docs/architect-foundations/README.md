<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 8: Tools and MCP](../developer/08-tools-and-mcp.md) · Next: [Domain 1: Agentic Architecture & Orchestration](01-agentic-architecture-and-orchestration.md)<!-- /nav:top -->

# Claude Certified Architect, Foundations (CCAR-F): Study Notes

These notes cover the Claude Certified Architect, Foundations exam (code CCAR-F). Every exam fact here comes from the official exam guide (Version 1.1, effective November 2026). Where the guide is silent, these notes say so instead of guessing.

The exam is about judgment. Most items describe a production system built with the Claude Agent SDK, Claude Code, the Claude API or MCP, show you a problem, and ask for the best fix. Feature names help, but the point goes to the fix that addresses the root cause at the right cost.

## Exam at a glance

| Item | Detail |
|---|---|
| Exam code | CCAR-F |
| Who it is for | Solution architects who design and build production applications with Claude, typically with 6+ months of hands-on work across the Claude API, Agent SDK, Claude Code and MCP |
| Items | 60 |
| Scored items | The guide does not separate scored and unscored items. It lists 60 items, and these notes use 60 for the per-domain estimates. |
| Time | 120 minutes |
| Item format | Multiple-choice and multiple-response. Each item states how many responses to select. |
| Structure | 4 scenarios, picked at random from a bank of 6 |
| Pass mark | Scaled score of 720 on a scale of 100 to 1,000 |
| Validity | 12 months from the date the credential is awarded |
| Delivery | Proctored, online (OnVUE) and/or at a Pearson VUE test center, per program policy |

## Which version you will sit

This exam is not part of the December 7, 2026 update. The Claude Certified Architect, Foundations exam has a single guide (Version 1.1, effective November 2026), so there is only one version to prepare for, whatever date you book. Everything in these notes follows that guide.

## Domains and weights

Item counts are approximate: weight multiplied by 60 items, rounded. The guide describes weights as the approximate share of scored items from each domain, so your form may differ by an item or two.

| # | Domain | Weight | About how many items | Domain page |
|---|---|---|---|---|
| 1 | Agentic Architecture & Orchestration | 27% | 16 | [Domain 1](01-agentic-architecture-and-orchestration.md) |
| 2 | Tool Design & MCP Integration | 18% | 11 | [Domain 2](02-tool-design-and-mcp-integration.md) |
| 3 | Claude Code Configuration & Workflows | 20% | 12 | [Domain 3](03-claude-code-configuration-and-workflows.md) |
| 4 | Prompt Engineering & Structured Output | 20% | 12 | [Domain 4](04-prompt-engineering-and-structured-output.md) |
| 5 | Context Management & Reliability | 15% | 9 | [Domain 5](05-context-management-and-reliability.md) |

## How the questions work

- **Format.** Items are multiple-choice (one answer) or multiple-response (more than one answer). Each item tells you how many responses to select, so you never have to guess the count.
- **Scenario style.** The exam is built around scenarios. Each scenario sets up a realistic production context, and a set of items is asked inside that context. You get 4 scenarios, picked at random from 6. The guide does not say how many items belong to each scenario.
- **What the items reward.** The guide says items test practical judgment about architecture, configuration and tradeoffs, grounded in real customer use cases. Expect stems that describe observed behaviour (logs, error rates, user complaints) and options that are all plausible fixes. Usually one fixes the root cause proportionately, and the others are over-engineered, rely on hope (a prompt where a guarantee is needed), or treat a different problem.
- **Unscored items.** The guide does not mention unscored or pretest items for this exam.
- **Scaled score.** The exam is criterion-referenced: you are measured against a fixed standard, not against other candidates. The cut score of 720 (on 100 to 1,000) was set by a formal standard-setting study. Scaling lets scores be compared across forms of slightly different difficulty.
- **Score report.** You get pass or fail and your scaled score, plus the percentage you answered correctly in each domain. Those domain percentages are for feedback only. Pass or fail depends on your total scaled score.

## The six exam scenarios

Your exam draws 4 of these 6. You cannot know which 4, so prepare for all of them. The summaries below are in our own words.

| # | Scenario | What it is about | Domains it tends to test |
|---|---|---|---|
| 1 | Customer Support Resolution Agent | An Agent SDK agent resolves messy support requests (returns, billing disputes, account problems) through custom MCP tools for customer lookup, orders, refunds and human escalation. The goal is high first-contact resolution while still escalating the right cases. | Agentic Architecture & Orchestration, Tool Design & MCP Integration, Context Management & Reliability |
| 2 | Code Generation with Claude Code | A team uses Claude Code day to day for writing, refactoring, debugging and documenting code. The questions are about custom slash commands, CLAUDE.md setup, and when to plan first versus act directly. | Claude Code Configuration & Workflows, Context Management & Reliability |
| 3 | Multi-Agent Research System | A coordinator agent built on the Agent SDK hands work to specialist subagents (web search, document analysis, synthesis, report writing) and produces cited research reports. | Agentic Architecture & Orchestration, Tool Design & MCP Integration, Context Management & Reliability |
| 4 | Developer Productivity with Claude | An Agent SDK agent helps engineers explore unfamiliar or legacy code, write boilerplate and automate chores, using the built-in tools (Read, Write, Bash, Grep, Glob) plus MCP servers. | Tool Design & MCP Integration, Claude Code Configuration & Workflows, Agentic Architecture & Orchestration |
| 5 | Claude Code for Continuous Integration | Claude Code runs inside a CI/CD pipeline to review code, generate tests and comment on pull requests. The focus is on feedback developers can act on, with few false positives. | Claude Code Configuration & Workflows, Prompt Engineering & Structured Output |
| 6 | Structured Data Extraction | Claude pulls structured data out of unstructured documents, the output is validated against JSON schemas, and results feed downstream systems. Edge cases must be handled without losing accuracy. | Prompt Engineering & Structured Output, Context Management & Reliability |

Notice that Context Management & Reliability appears in four scenarios even though it carries the lowest weight. Its ideas (structured errors, escalation, provenance, context trimming) show up inside items that look like they belong to other domains.

## How to study for this exam

The guide's own preparation list is hands-on: build things, then test them on edge cases. This plan follows the weights and that advice.

1. **Read the guide's task statements once, in full.** Items are written against them, and they are unusually specific (named hooks, CLI flags, frontmatter keys, error metadata fields). Keep the appendix list of technologies open as a checklist.
2. **Start with Domain 1 (27%).** Build a small Agent SDK agent with a real agentic loop that branches on `stop_reason`. Add a coordinator with `"Task"` in its `allowedTools`, two subagents defined with AgentDefinition, explicit context passing, and parallel Task calls in one response. Add one hook that blocks a tool call and one PostToolUse hook that normalizes tool output. Try `--resume` with a named session and `fork_session`.
3. **Then Domains 3 and 4 (20% each).** For Domain 3, configure a real repository: project CLAUDE.md, an `@import`, `.claude/rules/` files with `paths` globs, one project slash command, one skill using `context: fork`, `allowed-tools` and `argument-hint`, and a CI job using `-p`, `--output-format json` and `--json-schema`. Run the same task in plan mode and direct execution to feel the difference. For Domain 4, build an extraction pipeline with a JSON schema tool, `tool_choice` set three ways, nullable fields, an "other" plus detail enum, a validation-retry loop, and a small Message Batches job that handles failures by `custom_id`.
4. **Then Domain 2 (18%).** Write two MCP tools with similar purposes and make their descriptions unambiguous. Return structured errors with `isError`, an error category and a retryable flag. Configure one shared server in `.mcp.json` with environment variable expansion for its token and one personal server in `~/.claude.json`. Expose one content catalog as an MCP resource.
5. **Finish with Domain 5 (15%), and weave it through everything.** Practise a "case facts" block, trimming verbose tool results, scratchpad files, structured error propagation with partial results, escalation rules with examples, and confidence-based human review by document type and field.
6. **Work the guide's four preparation exercises** (a multi-tool agent with escalation, a team Claude Code setup, an extraction pipeline, a multi-agent research pipeline). Together they touch every domain.
7. **Drill items by elimination.** For each practice item, name why each wrong option is wrong. On this exam the distractors are often good ideas applied to the wrong problem.

The guide names the Anthropic Partner Academy as the place to register and to complete renewal, and recommends reading the guide in full before scheduling. It does not name a specific official course for this exam.

## Rules and policies worth knowing

- **Retakes.** If you do not pass, you can retake after a waiting period that grows with each failed attempt: 14 days after the first, 30 days after the second, 90 days after the third. You can sit the exam up to four times in a rolling twelve-month period. Limits are per exam, and the fee applies to each attempt.
- **Validity and renewal.** The credential is valid for 12 months. On-time renewal means reviewing what has changed and completing a free, non-proctored assessment on the Anthropic Partner Academy. If the credential lapses, you retake the full exam. If exam content changes significantly, Anthropic may require the full exam instead of the renewal assessment.
- **Rescheduling and cancelling.** OnVUE exams can be cancelled or rescheduled up to one hour before the appointment, test center exams up to 48 hours before. Later changes forfeit the fee. No-shows and late arrivals also forfeit the fee and must re-register.
- **Identification.** Bring a valid, unexpired, government-issued photo ID. The name must match your registration exactly. You can change the name yourself once in your Partner Academy exam profile; for test center bookings, change it at least 24 hours ahead.
- **Accommodations.** Reasonable accommodations are available for documented disabilities or needs. Request them through Pearson VUE and wait for approval before you schedule.
- **Exam-day conduct.** Online, stay in view of the webcam the whole time. Keep the workspace clear of notes, phones, extra monitors and other materials. Do not talk to anyone or copy exam content. Misconduct can void your result, revoke the credential and ban you from future exams.
- **Confidentiality.** You must accept a non-disclosure agreement before the exam starts. Declining ends the session with no refund.
- **Appeals.** You can appeal a decision within 14 days of being notified, or a result within 14 days of your exam date, through Pearson VUE support. The cut score and the content of individual items cannot be appealed.

## Practice

When you have worked through the domain pages, test yourself under real timing with this [free timed practice test for the Claude Certified Architect, Foundations exam](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect).

---

<!-- nav:bottom -->← Previous: [Domain 8: Tools and MCP](../developer/08-tools-and-mcp.md) | Next: [Domain 1: Agentic Architecture & Orchestration](01-agentic-architecture-and-orchestration.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
