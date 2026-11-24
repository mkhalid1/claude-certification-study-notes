<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 5: Governance, Safety, and Risk Management](05-governance-safety-and-risk-management.md) · Next: [Domain 7: Developer Productivity and Operational Enablement](07-developer-productivity-and-operational-enablement.md)<!-- /nav:top -->

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

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A pension provider's discovery notes include this requirement from its audit team: "The assistant must give word-for-word identical answers to identical member questions, every time, so auditors can verify consistency." The proposed design is a Claude assistant answering from the scheme rules. About 40 questions make up most of the traffic, and auditors mainly care about those. Which response best resolves the commitment?

- A. Accept the requirement and set the sampling temperature to zero, which removes randomness so the same input always gives the same output.
- B. Clarify the requirement as consistent outcomes measured against an agreed evaluation threshold, and serve reviewed answers for the 40 common questions from an approved answer store.
- C. Accept the requirement now and revisit it if auditors find differences after launch.
- D. Accept the requirement and use the largest model tier, which produces identical answers to identical inputs.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Generated text is not guaranteed to be identical between runs, so the commitment as written cannot be met. Redefining it as measured consistency, and serving approved answers for the questions auditors care about most, meets the real need.

- **A** is wrong because low temperature reduces variation but does not guarantee identical output.
- **C** is wrong because it signs a commitment the team already knows it cannot keep.
- **D** is wrong because no model tier guarantees identical output.

</details>

### Item 2

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

### Item 3

A procurement client's draft SLA says: "Every request will be fully answered within one second." The proposed design retrieves supplier documents, then makes three sequential Claude calls to compare long contracts and draft a recommendation. The prototype's p95 for the full flow is nine seconds. Users said in interviews that they mostly need to know quickly that work has started, and they are happy to receive the final recommendation a little later. Which change best resolves the commitment?

- A. Keep the one-second commitment and plan to add more compute capacity, parallel workers and a faster network path before launch.
- B. Remove retrieval from the flow so that only model calls remain.
- C. Switch all three calls to the smallest model tier without re-running the quality evaluation.
- D. Redefine the commitment around time to first visible response with streaming, and run the full comparison as an asynchronous job with a completion notice.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Users need fast confirmation and accept a later final result. Committing to a quick first response, and running the long comparison asynchronously with a notice, matches that need with something the architecture can deliver.

- **A** is wrong because three sequential calls over long contracts cannot be made to finish in one second by adding capacity.
- **B** is wrong because removing retrieval removes the supplier documents the comparison depends on.
- **C** is wrong because it trades unknown quality for latency and still may not reach one second.

</details>

### Item 4

A draft ADR for a claims-triage system says: "Decision: use the largest Claude tier for all classification. Consequence: best possible quality. The model will never misclassify a fraud case." The finance director, who must approve the budget, has asked for predictable running costs. The evaluation shows the largest tier at 96% accuracy on fraud cases and a mid tier at 94%. Which two revisions should the architect make? (Choose two.)

- A. Remove the alternatives section to keep the record short for executive readers.
- B. Add a statement that future model versions are guaranteed to improve accuracy further.
- C. Add a cost projection at expected volume, with a range, comparing the largest and mid tiers, and state why the accuracy difference justifies the chosen option.
- D. Add published vendor benchmark scores as proof that fraud cases will be classified correctly.
- E. Replace "will never misclassify" with the measured 96% fraud accuracy, the human review step for flagged cases, and the residual risk that remains.

<details>
<summary>Answer and explanation</summary>

**Answer: C and E.** The record fails the finance director's cost constraint and overstates certainty about model behavior. A cost comparison makes the trade-off explicit (C), and replacing the absolute claim with measured accuracy, mitigation and residual risk makes it honest (E).

- **A** is wrong because alternatives are where the trade-off is made visible.
- **B** is wrong because it overstates certainty about future model behavior.
- **D** is wrong because generic benchmarks are not evidence for this workload, and they do not address cost.

</details>

### Item 5

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

### Item 6

A consultancy is handing a production Claude system over to a client's operations team. The package contains architecture diagrams, all prompts, the evaluation suite, deployment scripts and a monitoring dashboard. During the handoff review, the client notices that the API account, its keys and its rate-limit tier all sit under the consultancy's own organization, and the consultancy's engagement ends next month. Which documentation and action close the gap?

- A. Add a page explaining the account's rate-limit tier, key rotation schedule and billing contacts, so the client understands how the account works and who to contact.
- B. Transfer the API account, keys, quotas and billing to the client's organization, store the keys in the client's secret manager, and document the owners.
- C. Add more detail to the architecture diagrams so the dependency is easier to see.
- D. Give the client the consultancy's support email for any account issues after the engagement ends.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The system depends on an account the receiving team does not control, which will block operation when the engagement ends. Moving ownership and secrets to the client, and documenting who owns them, removes the block.

- **A** is wrong because explaining limits does not give the client control of the account.
- **C** is wrong because a clearer diagram still leaves the dependency in the consultancy's hands.
- **D** is wrong because an email address is not a substitute for owning the account the system runs on.

</details>

### Item 7

A support assistant is being handed to an internal platform team. The package includes prompts, the evaluation suite, dashboards and an alert that fires when the weekly groundedness score drops below its threshold. The receiving team asks what it should actually do when that alert fires, and nobody on the build team has written it down. Prompts are versioned, but only the build team knows which version is the last known-good one. What should be added to close the gap?

- A. A runbook for the alert: triage steps (check retrieval results, tool errors and the model version in use), how to roll back to the last known-good prompt version, and who owns each step and escalation.
- B. More detailed architecture diagrams of the retrieval pipeline and evaluation suite, so the team can work out for itself where a groundedness drop comes from.
- C. A list of every prompting technique used in the system.
- D. The personal phone number of the lead engineer on the build team.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The receiving team has an alert but no procedure, and it does not know the rollback target. A runbook with triage steps, a rollback path to a named version and clear owners is exactly what lets it act.

- **B** is wrong because diagrams describe the system but do not say what to do when it degrades.
- **C** is wrong because technique lists do not help with an incident.
- **D** is wrong because relying on one person is not an operational handoff.

</details>

### Item 8

A document-summary system is being handed over to a client's IT team. It was built and tested only on English documents under 50 pages. It also relies on a third-party OCR service whose contract the build team signed on a trial basis, and that contract ends in two months. The client plans to onboard German subsidiaries with 200-page reports next quarter. Which two additions to the handoff package close the gaps that would block the receiving team? (Choose two.)

- A. A list of the prompt versions tried during development and why each was rejected.
- B. The tested input assumptions (language, document length, volume), and what the system does with documents outside those limits.
- C. The full evaluation results from the final release candidate.
- D. The OCR dependency, including its owner, contract terms, renewal date and fallback if it becomes unavailable.
- E. An updated diagram of the summarization pipeline's components.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** The client's German, 200-page documents fall outside the tested assumptions, which must be written down (B). The OCR contract will end soon after handoff, so the dependency, its owner and its fallback must be documented (D).

- **A** is wrong because development history explains past choices but does not state the system's limits or dependencies.
- **C** is wrong because results on the tested set describe performance there, but do not state the limits or what happens outside them.
- **E** is wrong because a diagram shows the OCR service exists but not its owner, contract end or fallback.

</details>

### Item 9

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

### Item 10

A draft ADR for a document-processing agent includes: "Prompt injection risk: eliminated by our input filter, which blocked all 50 test attacks." The CISO, who must sign off, has asked for an honest view of what remains. The architecture also limits the agent's tools and requires confirmation before external actions. Which revision best meets the CISO's need?

- A. Keep the statement, since the filter blocked all 50 test attacks, and attach the test report as an appendix so the CISO can see the evidence.
- B. Remove the security section to avoid making claims that could be challenged.
- C. State that injection risk is reduced rather than eliminated, list the layered controls (input filter, restricted tools, confirmation for external actions), describe the residual risk, and explain how it is monitored.
- D. State that the model is trained to resist injection, so no further controls are needed.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The CISO needs the residual risk stated honestly. Describing the layered controls, what they do not cover and how the remainder is watched gives a decision-maker what they need to sign off.

- **A** is wrong because 50 passing tests cannot prove a risk is eliminated.
- **B** is wrong because removing the section hides the trade-off from the person who must approve it.
- **D** is wrong because it overstates certainty about model behavior and ignores the controls the design actually relies on.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 5: Governance, Safety, and Risk Management](05-governance-safety-and-risk-management.md) | Next: [Domain 7: Developer Productivity and Operational Enablement](07-developer-productivity-and-operational-enablement.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
