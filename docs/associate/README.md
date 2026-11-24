<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Find your weak domains first](../diagnostic.md) · Next: [Domain 1: Task Definition and Prompting](01-task-definition-and-prompting.md)<!-- /nav:top -->

# Claude Certified Associate, Foundations (CCAO-F): Study Notes

These notes cover the Claude Certified Associate, Foundations exam for anyone who uses Claude as a core productivity tool in a business role. All exam facts here come from Anthropic's official exam guide. The practice items are original.

## Exam at a glance

| Item | Detail |
|---|---|
| Exam code | CCAO-F |
| Who it is for | Professionals who use Claude as a core productivity tool and build Claude Projects in their daily work: business analysts, project managers, operations leads, marketing, communications, HR and education professionals, consultants and team leads supporting AI adoption, and knowledge workers |
| Items | 70 |
| Scored items | 60 (the other 10 are unscored, not identified, and do not affect your score) |
| Time | 120 minutes |
| Item format | Scenario-based multiple choice; each item states how many responses to select |
| Pass mark | Scaled score of 720 on a scale of 100 to 1,000 |
| Validity | 12 months from the date the credential is awarded |
| Delivery | Proctored: online proctored and/or test center, per program policy |
| Prerequisites | None. Regular, hands-on experience using Claude in a professional setting is recommended, not required |

## Which version you will sit

The exam changes on December 7, 2026. The date you sit the exam decides which version you get, not the date you registered or started studying. If your appointment is on or before December 6, 2026, you sit the earlier version. If it is on December 7, 2026 or later, you sit the updated version.

| | Through December 6, 2026 | December 7, 2026 or later |
|---|---|---|
| Items | 60 | 70 (60 scored, 10 unscored) |
| Number of domains | 7 | 6 |
| Domain 1 | Prompting and Task Execution, 14% | Task Definition and Prompting, 15% |
| Domain 2 | Output Evaluation and Validation, 21% | Output Evaluation and Quality Control, 20% |
| Domain 3 | Product and Model Selection, 12% | Capability, Model, and Context Selection, 15% |
| Domain 4 | Workflow Integration and Solution Design, 16% | Workflow Design and Integration, 18% |
| Domain 5 | Configuration and Knowledge Management, 12% | Configuration and Knowledge Management, 15% |
| Domain 6 | Governance, Risk, and Responsible Use, 15% | Responsible Use, Data Protection, and Governance, 17% |
| Domain 7 | Troubleshooting and Optimization, 10% | No separate domain (prompt diagnosis moves into Domain 1, configuration degradation into Domain 5) |

The rest of these notes follow the December 7, 2026 version. If you test earlier, the skills overlap heavily, but check the older blueprint for the separate troubleshooting domain.

## Domains and weights

Item counts are approximate: weight multiplied by the 60 scored items, rounded. The guide gives weights only, so a real form can differ by an item or two.

| # | Domain | Weight | About how many scored items | Page |
|---|---|---|---|---|
| 1 | Task Definition and Prompting | 15% | 9 | [Domain 1](01-task-definition-and-prompting.md) |
| 2 | Output Evaluation and Quality Control | 20% | 12 | [Domain 2](02-output-evaluation-and-quality-control.md) |
| 3 | Capability, Model, and Context Selection | 15% | 9 | [Domain 3](03-capability-model-and-context-selection.md) |
| 4 | Workflow Design and Integration | 18% | 11 | [Domain 4](04-workflow-design-and-integration.md) |
| 5 | Configuration and Knowledge Management | 15% | 9 | [Domain 5](05-configuration-and-knowledge-management.md) |
| 6 | Responsible Use, Data Protection, and Governance | 17% | 10 | [Domain 6](06-responsible-use-data-protection-and-governance.md) |

## How the questions work

- **Format.** Every item is scenario-based multiple choice. You read a short work situation and pick the best action, diagnosis or choice.
- **How many to pick.** Each item tells you how many responses to select. Most ask for one. Some ask for two. Read the instruction on every item, because picking one answer on a "choose two" item scores nothing.
- **Scenario style.** Items test judgment in realistic work situations, not recall. The guide says items do not require you to remember published figures such as pricing, context-window sizes or rate limits. Expect stems about a specific person, a specific deliverable and one constraint that decides the answer: a policy, an audience, a deadline, a stake.
- **Unscored items.** 10 of the 70 items are being trialled for future forms. They are mixed in with scored items and are not marked, so treat every item as if it counts.
- **Scaled score.** Results are reported as pass or fail with a scaled score from 100 to 1,000. The cut score is 720. The exam is criterion-referenced: you are measured against a fixed standard set by subject matter experts, not against other candidates.
- **Domain breakdown.** Your score report shows the percentage you answered correctly in each domain. These percentages help you see weak areas. They do not decide pass or fail; only the total scaled score does.

## How to study for this exam

The guide names no single required course. It points to the blueprint, Anthropic's AI Fluency Framework, official claude.ai documentation and help-center guidance, and hands-on use. Here is an order that follows the weights.

1. **Read the blueprint and self-assess.** Go through every skill statement in the six domains and mark each one "confident", "shaky" or "never done". This gives you your own study order on top of the one below.
2. **Learn the four AI Fluency competencies: Delegation, Description, Discernment and Diligence.** The guide says they underpin objectives in every domain. Many answers turn on one of them: what to hand to Claude, how to describe it, how to judge the output, and how to stay accountable.
3. **Start with Domain 2, Output Evaluation and Quality Control (20%).** It is the heaviest domain. Practise checking claims and citations in a research output against the sources before you use them, and deciding what an external deliverable needs that an internal draft does not.
4. **Then Domain 4, Workflow Design and Integration (18%).** Take one real recurring task and split it into steps that Claude executes, steps a person reviews, and steps a person keeps. Then build a repeatable version of it that a colleague could own.
5. **Then Domain 6, Responsible Use, Data Protection, and Governance (17%).** Read the Anthropic Usage Policy. Practise preparing a sensitive dataset for analysis in line with a policy: classify it, de-identify it, and set memory controls before anything is uploaded.
6. **Then Domains 1, 3 and 5 (15% each).** For Domain 1, take prompts that gave generic or off-target output and name the exact gap. For Domain 3, practise choosing among chat, Projects, Artifacts, research, code-executed analysis, Skills, connectors and memory, and defend a model tier and effort level. For Domain 5, configure at least one Project with instructions, knowledge sources and a Skill for a recurring task, and connect a workplace data source.
7. **Do the hands-on work the guide lists.** Configure a Project with instructions, knowledge and a Skill. Connect a workplace source such as Google Drive. Review, edit and delete memories, and try an incognito chat. The exam assumes regular use, so the hands-on part is worth more than reading.
8. **Work through the practice items in each domain page,** then do the sample questions in Section 7 of the official guide to see the item style.
9. **Finish with a timed run** so the pace of about 1.7 minutes per item feels normal.

## Rules and policies worth knowing

- **Retakes.** If you do not pass, you wait before retaking: 14 days after the first failed attempt, 30 days after the second, and 90 days after the third. You can take the exam up to four times in a rolling twelve-month period. Limits apply per exam, so failing this exam does not stop you registering for a different one. The exam fee applies to each attempt.
- **Rescheduling and cancelling.** Online proctored (OnVUE) exams can be cancelled or rescheduled up to one hour before the appointment. Test center exams can be changed up to 48 hours before. Later changes forfeit the exam fee. A no-show or arriving after the late-arrival window also forfeits the fee.
- **Identification.** Bring a valid, unexpired, government-issued photo ID. The name must match your registration exactly. You can fix your name yourself once in your Partner Academy exam profile before it locks. For test center appointments, update it at least 24 hours ahead.
- **Validity and renewal.** The credential lasts 12 months. On-time renewal means reviewing what has changed and completing a free, non-proctored assessment on the Anthropic Partner Academy. If the credential lapses, you retake the full exam. If content changes significantly, Anthropic may require the full exam instead of the renewal assessment.
- **Accommodations.** Reasonable accommodations are available for documented disabilities or needs. Pearson must approve the request before you schedule, so do not book until it is approved.
- **Appeals.** You can appeal a decision within 14 days of being notified of it, or a result concern within 14 days of your exam date, through Pearson support. The standard-setting outcome and the content of individual items cannot be appealed.
- **Conduct and confidentiality.** You accept a non-disclosure agreement before the exam. You may not copy or share exam content. Phones, smart watches, headphones, study materials and recording devices are prohibited. Misconduct can lead to invalidated results, revoked credentials or a ban.

## Practice

When you have worked through the domain pages, take this [free timed practice test for the Claude Certified Associate, Foundations exam](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate) to check your pacing and find your weak domains.

---

---

<!-- nav:bottom -->← Previous: [Find your weak domains first](../diagnostic.md) | Next: [Domain 1: Task Definition and Prompting](01-task-definition-and-prompting.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
