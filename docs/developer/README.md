<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 6: Responsible Use, Data Protection, and Governance](../associate/06-responsible-use-data-protection-and-governance.md) · Next: [Domain 1: Agents and Workflows](01-agents-and-workflows.md)
<!-- /nav:top -->

# Claude Certified Developer, Foundations (CCDV-F): Study Notes

Free study notes for the Claude Certified Developer, Foundations exam, written against the exam guide that takes effect on December 7, 2026 (guide version 1.1). Each domain page explains what the domain measures, how its questions tend to look, a method for answering them, common traps, and 3 to 4 original practice items with explanations.

## Exam at a glance

| Item | Detail |
|---|---|
| Exam code | CCDV-F |
| Who it is for | Hands-on developers who build agents and workflows, integrate Claude into applications, and ship Claude-powered products: software engineers, AI/ML engineers, full-stack and backend developers, and solutions engineers. The guide describes the typical candidate as having one to five years of software engineering experience and at least six months of hands-on work with Claude. |
| Items | 70 |
| Scored items | 60 (the other 10 are unscored, not identified, and do not affect your score) |
| Time | 120 minutes |
| Item format | Scenario-based multiple choice. Each item states how many responses to select. |
| Pass mark | Scaled score of 720 on a scale of 100 to 1,000 |
| Validity | 12 months from the date the credential is awarded |
| Delivery | Proctored, online and/or at a test center, booked through Pearson |
| Prerequisites | None. The credential is awarded on exam performance alone. |

## Which version you will sit

CCDV-F changes on December 7, 2026. The date you sit the exam decides which version you get: through December 6 you take the original form, and from December 7 onward you take the revised one. The revised version has more items (70, of which 60 are scored) and a very different weighting. Applications and Integration drops from about a third of the exam to a fifth, while Claude Code, evaluation, and prompt engineering all grow.

| | Through December 6, 2026 | December 7, 2026 or later |
|---|---|---|
| Items | 53 | 70 (60 scored, 10 unscored) |
| Time | 120 minutes | 120 minutes |
| Domain 1: Agents and Workflows | 14.7% | 17% |
| Domain 2: Applications and Integration | 33.1% | 20% |
| Domain 3: Claude Code | 3.1% | 8% |
| Domain 4: Eval, Testing, and Debugging | 2.6% | 10% |
| Domain 5: Model Selection and Optimization | 16.8% | 12% |
| Domain 6: Prompt and Context Engineering | 11.0% | 15% |
| Domain 7: Security and Safety | 8.1% | 8% |
| Domain 8: Tools and MCP (named "Tools and MCPs" in the earlier guide) | 10.6% | 10% |

The rest of these notes follow the December 7 version. If your appointment is on or before December 6, the topics still apply, but weight your study time by the left-hand column.

## Domains and weights

Item counts are approximate: weight multiplied by the 60 scored items, rounded. The real split on any form can differ by an item or two.

| # | Domain | Weight | About how many scored items | Page |
|---|---|---|---|---|
| 1 | Agents and Workflows | 17% | 10 | [Domain 1](01-agents-and-workflows.md) |
| 2 | Applications and Integration | 20% | 12 | [Domain 2](02-applications-and-integration.md) |
| 3 | Claude Code | 8% | 5 | [Domain 3](03-claude-code.md) |
| 4 | Eval, Testing, and Debugging | 10% | 6 | [Domain 4](04-eval-testing-and-debugging.md) |
| 5 | Model Selection and Optimization | 12% | 7 | [Domain 5](05-model-selection-and-optimization.md) |
| 6 | Prompt and Context Engineering | 15% | 9 | [Domain 6](06-prompt-and-context-engineering.md) |
| 7 | Security and Safety | 8% | 5 | [Domain 7](07-security-and-safety.md) |
| 8 | Tools and MCP | 10% | 6 | [Domain 8](08-tools-and-mcp.md) |

## How the questions work

- **Format.** Every item is a scenario-based multiple-choice question. The item tells you how many responses to select, so read that instruction before you look at the options.
- **Scenario style.** Expect a short production situation: a trace, an error output, a config file, a usage report, or a design with a stated constraint. Your job is to find the defect or the best design choice. The guide's sample items all share the same shape: three options that treat a symptom or add risk, and one that fixes the cause.
- **Unscored items.** 10 of the 70 items are being trialled for future forms. They are mixed in with the scored items and are not marked, so treat every item as if it counts.
- **Scaled score.** Results are reported as pass or fail with a scaled score from 100 to 1,000. The cut score is 720. It is a scaled score set through a standard-setting study, so do not read it as "72% correct". The exam is criterion-referenced: you are measured against a fixed standard, not against other candidates.
- **Domain breakdown.** Your score report shows the percentage of items you answered correctly in each domain. Those percentages help you see weak areas but do not decide pass or fail. Only the total scaled score does.

## How to study for this exam

The guide names no required course and says no single resource guarantees a pass. It recommends studying the blueprint, reading the official Anthropic documentation, building something real, practising the core competencies, and working through the sample questions. Here is an order that follows the weights.

1. **Self-assess against the blueprint first.** Read the skill statements on each domain page and mark each one green, amber, or red. Spend your time on the reds in the heavy domains.
2. **Start with Domain 2, Applications and Integration (20%).** It is the largest domain and it underpins the rest. Learn the Messages API request and response shape, the streaming event sequence and which event ends a message, every stop reason, the API error classes and which ones are worth retrying, the Files API, and batch versus realtime. Write and break real requests.
3. **Then Domain 1, Agents and Workflows (17%).** Practise deciding between a fixed workflow and an agent from requirements like error tolerance and audit needs. Build an Agent SDK agent with a hook that enforces a required step, and learn to read a trace for a missing loop exit, missing isolation, or context overflow.
4. **Then Domain 6, Prompt and Context Engineering (15%).** Revise a failing prompt against a regression set, use structured outputs with validation, and build a grounded question-answering request that cites sources and says "not found" when it should.
5. **Then Domain 5, Model Selection and Optimization (12%).** Practise placing prompt-cache breakpoints by how often each part of the prompt changes, reading usage fields to find the cost driver, and choosing a model tier and effort level against a binding constraint.
6. **Then Domains 4 and 8 (10% each).** Build a small golden set and an LLM-as-judge, and measure the judge against human labels before trusting it. Build one custom tool with a precise description and a structured error, and one small MCP server over stdio and over HTTP.
7. **Finish with Domains 3 and 7 (8% each).** In Claude Code, learn configuration precedence, permission modes, headless mode, and which extension mechanism fits which workflow. For security, learn layered injection defences, deterministic guardrails through hooks and least privilege, and correct API key handling.
8. **Build the project the guide asks for.** The guide recommends shipping at least one tool-using agent or workflow, built with the Claude Agent SDK or your own harness, that includes custom tools or an MCP server, structured outputs, prompt caching, an evaluation against a golden set, and deterministic guardrails. That one project touches almost every domain.
9. **Practise under time pressure.** Work through each domain's practice items, then take a full timed practice test so you get used to the pace.

## Rules and policies worth knowing

- **Retakes.** If you do not pass, you wait before retaking: 14 days after the first failed attempt, 30 days after the second, and 90 days after the third. You can take the exam up to four times in a rolling twelve-month period. Limits apply per exam, so failing CCDV-F does not stop you registering for a different exam. The exam fee applies to every attempt.
- **Rescheduling and cancelling.** Online proctored (OnVUE) exams can be changed up to one hour before the appointment, and test center exams up to 48 hours before. Later changes forfeit the fee. No-shows and late arrivals beyond the permitted window also forfeit the fee.
- **Identification.** Bring a valid, unexpired, government-issued photo ID. The name must match your registration exactly. You can change your name yourself once in your exam profile; after that, you need to contact Anthropic certification support.
- **Validity and renewal.** The credential lasts 12 months. Renewing on time means reviewing what has changed and completing a free, non-proctored assessment on the Anthropic Partner Academy. If it lapses, you retake the full exam. If content changes significantly, Anthropic may require a full retake instead of the renewal assessment.
- **Accommodations.** Reasonable accommodations are available for documented disabilities or needs. Request them through Pearson and wait for approval before you schedule.
- **Appeals.** You can appeal a decision within 14 days of being notified, or a concern about your result within 14 days of your exam date, through Pearson support. The standard-setting outcome and the content of individual items cannot be appealed.
- **Conduct and confidentiality.** You accept a non-disclosure agreement before the exam starts. Capturing or sharing exam content can lead to invalidated results, revoked credentials, or a ban.

## Practice

When you have worked through the domain pages, test your pacing with this [free timed practice test for the Claude Certified Developer, Foundations exam](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

---

---

<!-- nav:bottom -->
← Previous: [Domain 6: Responsible Use, Data Protection, and Governance](../associate/06-responsible-use-data-protection-and-governance.md) | Next: [Domain 1: Agents and Workflows](01-agents-and-workflows.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
