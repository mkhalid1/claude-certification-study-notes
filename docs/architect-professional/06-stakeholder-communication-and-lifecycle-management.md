<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 5: Governance, Safety, and Risk Management](05-governance-safety-and-risk-management.md) · Next: [Domain 7: Developer Productivity and Operational Enablement](07-developer-productivity-and-operational-enablement.md)
<!-- /nav:top -->

# Domain 6: Stakeholder Communication and Lifecycle Management

Weight: 7% of scored items, about 4 items.

## What this domain measures

This domain tests whether you can write and review the documents that sit around a Claude system: requirements, SLAs, decision records and handoff packages. The guide lists three skills:

- **Unsupportable commitments.** Spot a promise in discovery notes or a draft SLA that the proposed architecture cannot keep, and specify the requirement clarification or architectural change that resolves it.
- **Architecture decision records.** Review a draft ADR against a stakeholder constraint it ignores, and choose the revision that states the trade-off and its residual risk openly, without overstating certainty about model behavior.
- **Operational handoff.** Find the missing assumption, dependency or runbook element in a handoff package that would block the receiving team, and specify the documentation that closes the gap.

It is a small domain on the exam, but in practice these documents are where projects fail. A promise of perfect accuracy in an SLA, or a missing runbook, can sink a system that works well technically.

## How questions look

Items present a short artifact in prose: a line from discovery notes, a clause in a draft SLA, an excerpt from an ADR, or a list of what a handoff package contains. A stakeholder concern is usually stated next to it, from a CFO, a legal team, a CISO or an operations lead. The question asks which commitment cannot be met, which revision fixes the record, or which gap would block operation.

Wrong options tend to fall into three groups: promising the impossible more firmly, deleting the hard part instead of addressing it, or adding volume (more diagrams, more history) that does not close the specific gap.

## A method for these questions

1. Read the artifact as the stakeholder named in the stem would read it. Ask what they need to decide or do.
2. For commitments, test each one against how language models behave: outputs vary, accuracy is measured rather than guaranteed, and long multi-step work takes time.
3. Turn an unsupportable promise into one that can be measured: an agreed threshold on an agreed test set, a review step, or a redesign that changes what is being promised.
4. For ADRs, check that the record names the constraint, the alternatives, the trade-off accepted and the residual risk. Replace any certainty about model behavior with measured evidence and mitigations.
5. For handoffs, imagine the receiving team on its first bad day. What account, credential, assumption, owner or procedure would they need that is not in the package?
6. Choose the option that adds that one missing piece, not the option that adds the most material.

## Traps that cost points

- **Promising determinism.** Even with low sampling temperature, outputs are not guaranteed to be identical. Commit to consistent outcomes measured by evaluation.
- **Accepting "100% accuracy" with a disclaimer.** A footnote does not make the commitment supportable. Replace it with a measured threshold and a review path.
- **Deleting the requirement.** Removing an inconvenient commitment leaves the stakeholder's real need unmet.
- **Claiming a risk is eliminated.** Controls reduce risk. An ADR should state what remains and how it is monitored.
- **Citing vendor benchmarks as proof for your workload.** Your own evaluation on your own data is the evidence.
- **Handing over prompts and diagrams but not ownership.** If the API account, keys or quotas still belong to the delivery team, the client cannot operate the system.
- **Leaving input assumptions unwritten.** A system tested on short English documents may fail on long German ones. The limits must be in the handoff.
- **Promising the system learns by itself.** Improvement comes from a feedback, review and release process that must be designed and owned.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Items here are usually shorter than in Domains 1 and 3, so aim to finish them in about a minute and bank the time for heavier trace and architecture items. Read the stakeholder's concern first. The right answer almost always addresses it directly.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Supportable commitment | Stated as a measured threshold on an agreed test set, with a defined review or fallback path. |
| Determinism | Model outputs can vary between runs. Promise consistent decisions, not identical wording. |
| Latency promises | Long multi-step work may need streaming, asynchronous jobs or a different commitment, such as time to first response. |
| ADR essentials | Context, decision, alternatives, trade-off accepted, residual risk, how the risk is monitored. |
| Certainty about model behavior | Replace "always" and "never" with measured rates and mitigations. |
| Stakeholder fit | Each ADR must address the constraint of the person who will rely on it, such as cost, legal defensibility or security. |
| Handoff ownership | Accounts, keys, quotas and billing must move to the receiving organization. |
| Handoff assumptions | Input limits, languages, volumes and tested ranges, plus what happens outside them. |
| Handoff dependencies | Third-party services, contract terms, owners and fallbacks. |
| Runbook | Alerts, thresholds, triage steps, rollback to a known-good version, owners and escalation. |
| Improvement process | Feedback is collected, reviewed, turned into prompt or knowledge changes, evaluated and released. |

## Practice items

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A draft SLA for a logistics client's customs-document extraction service states: "The service will extract all declared fields with 100% accuracy." The prototype scores 97.5% field accuracy on an agreed test set of 2,000 documents, and most errors are on handwritten declarations. The client's real concern is that incorrect declarations cause fines. Which revision resolves the commitment?

- A. Commit to a measured field-accuracy threshold on an agreed, refreshed test set, and route handwritten declarations and fields that fail validation checks to human review before submission.
- B. Keep 100% in the SLA, since the client asked for it, and add a footnote saying AI systems can make mistakes and that errors will be fixed promptly.
- C. Keep 100% and move to the largest model tier to close the remaining gap.
- D. Remove accuracy from the SLA entirely, since it cannot be guaranteed.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** A measured threshold is a commitment the system can meet and that both sides can verify. Human review on the fields most likely to be wrong addresses the client's actual concern about fines.

- **B** is wrong because a footnote contradicts the headline promise without making it supportable.
- **C** is wrong because no tier guarantees perfect extraction, so the commitment still cannot be met.
- **D** is wrong because it drops the client's main concern instead of addressing it.

</details>

### Item 2

An ADR for an internal legal research assistant records the decision to use agentic search over the document repository instead of a fixed retrieval pipeline, citing better results on complex questions. The general counsel has said that, if an answer is challenged in a dispute, the team must be able to show exactly what material the system relied on. The ADR does not mention this. Which revision best addresses the constraint?

- A. State that agentic search is deterministic at low temperature, so the same question will always retrieve the same documents and produce the same answer.
- B. Leave the ADR unchanged, since evidence handling is a legal matter rather than an architectural one.
- C. State that search paths can vary between runs, specify that each answer stores its search trace and cited sources, and record the residual risk that a re-run may phrase the answer differently.
- D. Reverse the decision to a fixed retrieval pipeline without explaining the change.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The constraint is evidence of what the system relied on. Storing the trace and sources per answer meets it, and stating that runs can vary is an honest description of the residual risk.

- **A** is wrong because it overstates certainty. Agentic search can take different paths on different runs.
- **B** is wrong because it ignores a stakeholder constraint the architecture must meet.
- **D** is wrong because an unexplained reversal does not record the trade-off, and the stored trace addresses the need without losing quality.

</details>

### Item 3

A retail client's discovery notes contain this expectation from the head of customer experience: "The assistant will learn from every conversation automatically and get better each week without any work from us." The proposed design is a Claude assistant with a fixed system prompt and a knowledge base. Which response resolves this commitment?

- A. Accept it, since model providers release improved models regularly.
- B. Agree to retrain a model every night on all raw conversations, so the assistant picks up new questions and corrections automatically, as the client wants.
- C. Add every past conversation to the system prompt so the assistant can learn from them.
- D. Clarify that the assistant does not improve on its own, and design an owned improvement process: collect feedback, review it, update prompts and knowledge, re-run evaluations, and release on a set cadence.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The assistant does not learn from conversations by itself, so the expectation must be clarified. A designed, owned feedback and release process delivers steady improvement in a way that can be measured.

- **A** is wrong because new model versions are not automatic weekly learning, and each one needs evaluation before use.
- **B** is wrong because nightly retraining on raw conversations bypasses review and evaluation and raises privacy concerns.
- **C** is wrong because the conversations would soon exceed the context window, and raw transcripts are not curated knowledge.

</details>

Want more? The free timed Architect, Professional diagnostic on PrepClubs has the full 25 questions, scored by domain: [Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-architect-professional).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=p5gza7&utm_content=claude-certified-architect-professional) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 5: Governance, Safety, and Risk Management](05-governance-safety-and-risk-management.md) | Next: [Domain 7: Developer Productivity and Operational Enablement](07-developer-productivity-and-operational-enablement.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
