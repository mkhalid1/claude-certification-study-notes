<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 5: Context Management & Reliability](../architect-foundations/05-context-management-and-reliability.md) · Next: [Domain 1: Solution Design and Architecture](01-solution-design-and-architecture.md)<!-- /nav:top -->

# Claude Certified Architect, Professional (CCAR-P)

Study notes for Anthropic's Claude Certified Architect, Professional exam, written for the version that takes effect on December 7, 2026. Each domain page explains what the domain measures, how its questions tend to work, a method for answering them, common traps, and 10 original practice items with explanations.

## Exam at a glance

| | |
|---|---|
| Exam code | CCAR-P |
| Who it is for | Mid- to senior-level solution architects, AI/ML engineers, technical leads and senior software engineers who design, build and deliver production Claude solutions. The guide describes typical candidates as having three or more years in systems architecture or platform engineering and at least six months of hands-on production work with Claude. |
| Items | 70 |
| Scored items | 60 (the other 10 are unscored and not identified) |
| Time | 120 minutes |
| Item format | Scenario-based multiple choice. Each item states how many responses to select. |
| Pass mark | Scaled score of 720 on a scale of 100 to 1,000 |
| Prerequisites | None. The Claude Certified Architect, Foundations certification is recommended preparation but not required. |
| Validity | 12 months from the date the credential is awarded |
| Delivery | Proctored, online and/or at a test center, per program policy. Registration runs through the Anthropic Partner Academy and Pearson. |

## Which version you will sit

The exam guide changes on December 7, 2026. The version you sit depends on the date you take the exam. If you test on or before December 6, 2026, you sit the earlier version. If you test on December 7, 2026 or later, you sit the updated version.

| | Through December 6, 2026 | December 7, 2026 or later |
|---|---|---|
| Items | 63 (scored and unscored split not stated) | 70, of which 60 are scored |
| Time | 120 minutes | 120 minutes |
| 1. Solution Design and Architecture | 17% | 18% |
| 2. Claude Models, Prompting, and Context Engineering | 13% | 13% |
| 3. Integration | 19% | 21% |
| 4. Evaluation, Testing, and Optimization | 16% | 17% |
| 5. Governance, Safety, and Risk Management | 14% | 16% |
| 6. Stakeholder Communication and Lifecycle Management | 14% | 7% |
| 7. Developer Productivity and Operational Enablement | 7% | 8% |

The biggest shift is Domain 6, which drops from 14% to 7%, with that weight spread across the other domains, mostly Integration and Governance. The updated guide also rewrites every objective as a specific skill statement, such as choosing a deployment surface and agent runtime, or calibrating an LLM judge against human review. The rest of these notes follow the December 7 version.

## Domains and weights

Item counts are approximate: each is the domain weight multiplied by the 60 scored items and rounded, so they add up to 61 rather than 60. Your real form can vary.

| # | Domain | Weight | About how many scored items | Page |
|---|---|---|---|---|
| 1 | Solution Design and Architecture | 18% | 11 | [Domain 1](01-solution-design-and-architecture.md) |
| 2 | Claude Models, Prompting, and Context Engineering | 13% | 8 | [Domain 2](02-models-prompting-and-context-engineering.md) |
| 3 | Integration | 21% | 13 | [Domain 3](03-integration.md) |
| 4 | Evaluation, Testing, and Optimization | 17% | 10 | [Domain 4](04-evaluation-testing-and-optimization.md) |
| 5 | Governance, Safety, and Risk Management | 16% | 10 | [Domain 5](05-governance-safety-and-risk-management.md) |
| 6 | Stakeholder Communication and Lifecycle Management | 7% | 4 | [Domain 6](06-stakeholder-communication-and-lifecycle-management.md) |
| 7 | Developer Productivity and Operational Enablement | 8% | 5 | [Domain 7](07-developer-productivity-and-operational-enablement.md) |

## How the questions work

- **Format.** Every item is scenario-based multiple choice. Each item tells you how many responses to select, so read that instruction before the options.
- **Scenario style.** Expect a client or team situation with several requirements, one of which decides the answer: a residency attestation, an SLA, an accountable person, an evaluation gap, a stakeholder's constraint. The guide says scenarios present artifacts such as architecture diagrams, configuration files, logs and traces, evaluation results and code, and expect you to reason from them.
- **Unscored items.** 10 of the 70 items are unscored. They are mixed in with the scored items and not identified, so treat every item as if it counts.
- **Scaled score.** Your result is pass or fail with a scaled score from 100 to 1,000. The cut score is 720. The exam is criterion-referenced: you are measured against a fixed standard, not against other candidates.
- **Domain breakdown.** The score report shows your percentage correct in each domain. Those percentages help you see strengths and gaps but do not decide pass or fail, which rests on the total scaled score.
- **Pace.** 120 minutes for 70 items is about 1.7 minutes per item. Each domain page has timing guidance.

## How to study for this exam

1. **Start with the blueprint and the Foundations scope.** The guide says the Claude Certified Architect, Foundations scope is assumed knowledge. Read the blueprint in Section 5 of the CCAR-P guide and rate yourself against each skill statement. Mark the ones you have never done in production.
2. **Study Integration first (21%, about 13 items).** It is the largest domain. Focus on least-privilege tool sets, credential custody and delegated identity, retrieval architecture choices, MCP versus direct API versus agent handoff, and reliability controls such as backoff and idempotency.
3. **Then Solution Design and Architecture (18%, about 11 items).** Practice choosing between workflow, agentic, augmented-LLM and Skills-based patterns, and choosing a deployment surface and agent runtime under residency and operational-ownership constraints. Learn to say what each choice costs.
4. **Then Evaluation, Testing, and Optimization (17%, about 10 items).** Practice reading traces to find the first broken stage, spotting the bias in an LLM judge, designing paired comparisons with repeated trials, and planning a model version migration.
5. **Then Governance, Safety, and Risk Management (16%, about 10 items).** Practice placing guardrails and human-in-the-loop gates by reversibility and stakes, controlling prompt injection, and mapping compliance obligations (residency, retention, redaction, audit, tenant isolation) to the right control.
6. **Then Claude Models, Prompting, and Context Engineering (13%, about 8 items).** Practice tier selection against numeric constraints, prompt-cache ordering, technique selection, and context lifecycle choices (context editing, the memory tool, compaction).
7. **Finish with Domains 7 and 6 (8% and 7%).** They are small but quick to score on. For Domain 7, get hands-on with Claude Code permission modes, hooks, subagent definitions, plugins and managed settings. For Domain 6, review SLAs, decision records and handoff packages for gaps.
8. **Do the hands-on work the guide names.** The guide recommends designing and delivering at least one end-to-end Claude solution in production, covering model and deployment-surface selection, prompt and context engineering, retrieval or tool integration, an evaluation suite, observability and layered guardrails. It also recommends practicing review of artifacts made by others: architecture diagrams, configuration files, logs and traces, evaluation results and code.
9. **Use the official resources.** The guide says there is no single required course. It points to the blueprint, the Foundations exam guide, official Anthropic documentation and engineering guidance for the Claude Developer Platform, Agent Skills, Claude Code and the Claude Agent SDK, Claude Managed Agents and the Model Context Protocol, the feature-availability and data-handling differences across the Anthropic API, Amazon Bedrock, Google Vertex AI and Microsoft Foundry, and the sample questions in Section 7 of the guide.
10. **Practice under time pressure.** Work through the 70 items in these notes, then take a timed practice test and review every miss by domain.

## Rules and policies worth knowing

- **Retakes.** If you do not pass, you can retake after a waiting period that grows with each failed attempt: 14 days after the first, 30 days after the second and 90 days after the third. You can take the exam up to four times in a rolling twelve-month period. Limits apply per exam. The exam fee applies to each attempt.
- **Rescheduling and no-shows.** Online proctored exams can be canceled or rescheduled up to one hour before the appointment, and test center exams up to 48 hours before. Later changes forfeit the exam fee. Missing the exam, or arriving after the late-arrival window, also forfeits the fee.
- **Identification.** You need a valid, unexpired, government-issued photo ID, and the name must match your registration exactly. You can change your registration name yourself once in your Partner Academy profile. For test center appointments, update it at least 24 hours ahead.
- **Validity and renewal.** The credential is valid for 12 months from the date it is awarded. On-time renewal means reviewing what has changed and completing a free, non-proctored assessment on the Anthropic Partner Academy. If the credential lapses, you must retake the full exam. If exam content changes significantly, Anthropic may require a full retake instead of the renewal assessment.
- **Accommodations.** Reasonable accommodations are available for documented disabilities or needs. They must be requested through Pearson and approved before you schedule, so do not book until approval arrives.
- **Appeals.** You can appeal a decision within 14 days of being notified of it, or within 14 days of your exam date for a concern about your result, through Pearson support. The standard-setting outcome and the content of individual items cannot be appealed.
- **Conduct and confidentiality.** You must accept a non-disclosure agreement before the exam starts. Capturing or sharing exam content can lead to an invalidated result, revoked credentials or a ban.

## Practice

When you have worked through the domain pages, test yourself with this [free timed practice test for the Claude Certified Architect, Professional exam](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional).

---

<!-- nav:bottom -->← Previous: [Domain 5: Context Management & Reliability](../architect-foundations/05-context-management-and-reliability.md) | Next: [Domain 1: Solution Design and Architecture](01-solution-design-and-architecture.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
