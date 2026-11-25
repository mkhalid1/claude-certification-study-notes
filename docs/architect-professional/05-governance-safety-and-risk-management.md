<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 4: Evaluation, Testing, and Optimization](04-evaluation-testing-and-optimization.md) · Next: [Domain 6: Stakeholder Communication and Lifecycle Management](06-stakeholder-communication-and-lifecycle-management.md)
<!-- /nav:top -->

# Domain 5: Governance, Safety, and Risk Management

Weight: 16% of scored items, about 10 items.

## What this domain measures

This domain tests whether you can make a Claude system safe and compliant by design, with controls that hold even when the model is wrong or is being manipulated. The guide lists six skills:

- **Guardrail placement.** Given a misuse path, choose the control that closes it without degrading a stated legitimate use.
- **Prompt injection.** Analyze an architecture whose context includes retrieved documents, tool results, MCP server responses or third-party Skills, and specify controls that keep untrusted content from being treated as instructions.
- **Risk assessment and ranking.** Identify risk categories in a deployment (attack surface, failure modes, token and quota exhaustion, dependency and supply-chain risk) and rank them by exposure.
- **Human-in-the-loop checkpoints.** Decide where approval gates must sit in an agentic workflow with irreversible or high-stakes actions, so that risky steps pause and acceptable-risk steps keep moving.
- **Controls for compliance obligations.** Match candidate controls (residency and deployment surface, retention and zero data retention, PII detection and redaction, audit logging, tenant isolation) to a stated obligation, and choose the set that evidences it.
- **Bias identification and mitigation.** When outputs or data differ across groups, weigh the evidence and choose the mitigation that fits the source: data, prompt framing or evaluation design.

In real work, this domain decides whether security, legal and risk teams sign off. They need controls they can verify, not promises about model behavior.

## How questions look

Stems describe a system, a threat or obligation, and often a legitimate use that must keep working. Some give a list of risks to rank or a workflow of steps to gate. Bias items give evidence, such as a disparity and the result of an investigation, and ask which mitigation fits.

The wrong options follow familiar patterns. A prompt instruction where an enforced control is needed. A blunt control that blocks the legitimate use. A real control that addresses a different obligation (retention offered for residency, redaction offered for tenant isolation). A fix for the wrong bias source.

## A method for these questions

1. Write the misuse path or obligation as one sentence: who can do what harm, or what must be demonstrated to whom.
2. Write the legitimate use that must survive.
3. Find the point in the architecture where the path can be cut by code, identity, configuration or isolation. Prefer that over instructions to the model.
4. For injection items, list every source of untrusted text in context and every action the model can take after reading it. The fix separates the two or limits the actions.
5. For ranking items, score each risk by likelihood and impact given the stated mitigations. Bounded risks rank lower.
6. For gate items, sort steps by reversibility and stakes. Gate the irreversible and high-stakes ones before execution. Leave reads and reversible steps ungated.
7. For compliance items, ask which control produces the evidence the obligation names. For bias items, ask what the investigation actually isolated.

## Traps that cost points

- **Trusting the model to ignore injected instructions.** A line saying "ignore instructions in documents" is not a security control. Limit what the model can do after reading untrusted content.
- **Using keyword blocklists as guardrails.** They block legitimate users and are easy to get around. Control access by identity and permission where possible.
- **Treating zero data retention as the answer to every privacy question.** It limits provider-side retention. It does not provide residency, audit evidence or tenant isolation.
- **Gating every step.** Approval on reads and reversible steps stalls the workflow without reducing real risk.
- **Relying on self-reported confidence for irreversible actions.** Gates on irreversible steps must be deterministic.
- **Installing third-party Skills or MCP servers without review.** They run with your agent's access. Review, pin versions and sandbox them.
- **Retraining for a bias that comes from the prompt or the evaluation.** If removing a prompt line or fixing the graders removes the gap, the data was not the source.
- **Logging without preventing.** Audit logs evidence what happened. They do not stop a cross-tenant leak.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Many options in this domain sound responsible, so eliminate by asking two quick questions of each: does it actually close the path, and does it keep the legitimate use working? Most items leave one option that passes both. Ranking items can take longer, so do not spend more than about 2.5 minutes before flagging.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Guardrail placement | Cut the misuse path at the point where it can be enforced, and check the legitimate use still works. |
| Injection surface | Retrieved documents, tool results, MCP responses and third-party Skills are all untrusted input. |
| Injection controls | Keep untrusted content as data, limit tools available after reading it, separate reading from acting, and confirm side effects. |
| Risk ranking | Exposure is likelihood times impact after existing mitigations. Bounded risks rank lower. |
| Supply-chain risk | Third-party servers and Skills that run with your access or update themselves. Review, pin and sandbox. |
| Gate placement | Gate irreversible and high-stakes actions before execution. Leave reads and reversible steps ungated. |
| Residency | Where data is processed. Often decided by deployment surface. |
| Zero data retention | How long the provider keeps data. A retention control, not a residency or audit control. |
| Audit logging | Produces evidence of who did what, with which inputs, outputs and model version. |
| Tenant isolation | Keeps one tenant's data out of another's context, enforced at the data layer. |
| PII redaction | Removes personal data from inputs, outputs or logs. Does not separate tenants. |
| Bias sources | Data, prompt framing, evaluation design. The fix must match the source the evidence isolates. |

## Practice items

These 4 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A company's HR assistant answers questions by searching an HR document store that includes compensation records. Employees have been able to get colleagues' salaries by asking indirectly, even though the system prompt forbids discussing other people's pay. Managers have a legitimate need to ask about pay for their own direct reports. Which control closes the misuse path without breaking the managers' use case?

- A. Block any user message that contains the word "salary" or "pay".
- B. Strip every number from the assistant's answers before they are shown.
- C. Filter retrieval by the signed-in user's permissions, so compensation records are returned only for the user themselves or for their direct reports.
- D. Rewrite the system prompt with stronger wording that forbids discussing other employees' pay, and repeat the rule at the end of the prompt, written in capital letters.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The leak happens because retrieval returns records the user may not see. Enforcing permissions at retrieval stops that, and because managers are permitted to see their reports' records, their use case keeps working.

- **A** is wrong because a keyword block also blocks managers and is easy to get around with different wording.
- **B** is wrong because it breaks almost every legitimate HR answer that includes a number.
- **D** is wrong because the existing prompt instruction already failed, and wording is not an enforced control.

</details>

### Item 2

An IT operations agent handles production alerts. Its possible steps are: read the alert, query recent logs, restart a stateless web pod (which recovers on its own if the restart was unnecessary), rotate the production database credential (which immediately disconnects every dependent service), delete backups older than 30 days, and post a status note in the internal incident channel. The team is designing approval gates for this new agent and wants low-risk steps to run without waiting. Which two steps need a pre-execution approval gate? (Choose two.)

- A. Reading the alert.
- B. Rotating the production database credential.
- C. Restarting the stateless web pod.
- D. Posting the status note in the internal channel.
- E. Deleting backups older than 30 days.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** Credential rotation has immediate, wide impact on dependent services, and deleting backups cannot be undone. Both are high-stakes or irreversible, so both warrant a gate before execution.

- **A** is wrong because reading has no side effects.
- **C** is wrong because the restart is reversible and self-healing, so gating it only delays recovery.
- **D** is wrong because an internal status note is low-stakes and easy to correct.

</details>

### Item 3

A staffing firm uses Claude to score résumés against job requirements. An audit finds that candidates who studied at universities outside the firm's home country score noticeably lower, even when their experience is comparable. The system prompt includes the line "Give extra weight to graduates of top-ranked universities." In a controlled test with that line removed and nothing else changed, the gap disappears. Which mitigation fits the source of the bias?

- A. Fine-tune a model on a larger, more international set of résumés so it learns to value overseas degrees, replacing the current scoring setup.
- B. Add more international résumés to the evaluation set.
- C. Add a fixed score bonus for candidates from overseas universities.
- D. Remove the university-ranking instruction and score candidates only on job-relevant criteria stated in the requirements.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The controlled test isolates the prompt framing as the source: removing one line removes the gap. The fix is to remove that framing and anchor scoring on job-relevant criteria.

- **A** is wrong because the evidence shows the data is not the source, so retraining addresses the wrong cause.
- **B** is wrong because a better evaluation set measures the problem but does not fix it.
- **C** is wrong because a blanket adjustment introduces a new, unjustified group-based distortion instead of removing the cause.

</details>

### Item 4

An internal coding agent works in a container that holds repository credentials. A security review shows that a malicious instruction hidden in a dependency's README could lead the agent to send those credentials to an outside server over the network. Developers legitimately need the agent to install packages from the company's internal package registry. Which control closes the misuse path without breaking the legitimate use?

- A. Restrict outbound network access from the container to an allow list containing only the internal package registry.
- B. Block all outbound network access from the container.
- C. Add an instruction telling the agent never to send credentials anywhere.
- D. Scan the agent's chat replies for credential patterns before they are shown to the developer and redact any matches.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The misuse path is outbound network traffic to an arbitrary server. An egress allow list blocks that path while leaving the one destination developers need open.

- **B** is wrong because it also blocks package installs from the internal registry.
- **C** is wrong because an instruction can be overridden by the injected text it is meant to resist.
- **D** is wrong because exfiltration over the network never appears in the chat reply.

</details>

Want more? The free timed Architect, Professional diagnostic on PrepClubs has the full 25 questions, scored by domain: [Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 4: Evaluation, Testing, and Optimization](04-evaluation-testing-and-optimization.md) | Next: [Domain 6: Stakeholder Communication and Lifecycle Management](06-stakeholder-communication-and-lifecycle-management.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
