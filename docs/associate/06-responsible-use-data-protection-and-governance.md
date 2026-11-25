<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 5: Configuration and Knowledge Management](05-configuration-and-knowledge-management.md) · Next: [Exam overview: Developer, Foundations](../developer/README.md)
<!-- /nav:top -->

# Domain 6: Responsible Use, Data Protection, and Governance

Weight: 17% of scored items, about 10 items.

## What this domain measures

This domain tests judgment about whether a use of Claude is appropriate, how to handle sensitive data, and how to respond when real usage drifts from policy. The guide lists three skills:

- **Use-case appropriateness.** Assessing a proposed use for task suitability, delegation limits, trust in third-party Skills and connectors, and fit with the Anthropic Usage Policy. Then classifying it as appropriate, appropriate with safeguards, or inappropriate, with a documented rationale.
- **Data protection in practice.** Handling sensitive, confidential or regulated information in line with organizational policy: classifying data, redacting and anonymizing it, judging what to upload for code-executed analysis, and using memory and incognito controls.
- **Policy, ethics, and diligence.** Comparing observed usage with organizational AI policies to find compliance gaps, ethical risks such as bias and transparency failures, and lapses in Diligence. A key distinction is between a policy gap (the policy does not cover the situation) and a training gap (the policy covers it but people do not follow or know it). Then choosing the corrective action that actually closes the gap.

Why it matters: one careless upload or one unreviewed decision about a person can do lasting harm to customers, staff and the organization, and the person using Claude stays accountable for it.

## How questions look

Stems describe a proposed use case, a dataset someone wants to work with, a third-party tool someone wants to add, or a pattern found in an audit. You classify the use, choose how to prepare data, decide what to check before adopting a tool, or pick the corrective action. Distractors include controls that sound protective but do not address the actual risk, blanket bans, disclaimers in place of fixes, and treating every compliance problem as a training problem.

## A method for these questions

1. Identify what is at stake: people affected by a decision, confidential business information, regulated personal data, or the organization's reputation.
2. Find the organizational policy in the stem. Most answers must be consistent with it. If the policy is silent, that is itself a clue.
3. For use cases, ask whether Claude is supporting a person or replacing a person's judgment on something consequential. Decide among appropriate, appropriate with safeguards, and inappropriate, and name the safeguard or the reason.
4. For data, protect before sharing. Classify first, then remove or replace what the policy says must not be shared. A control that acts after upload does not undo the disclosure.
5. For third-party Skills and connectors, check approval status and what data the tool can reach before anyone uses it.
6. For audit findings, decide whether the policy covers the behavior. If it does and people did not follow it, it is a training gap. If it does not, it is a policy gap. Then pick the action that closes that kind of gap.

## Traps that cost points

- **Mixing up retention controls and disclosure.** Incognito and memory settings affect what is kept afterward. They do not change what data you shared in the first place.
- **Protecting data after upload.** Asking Claude to anonymize data you have already uploaded means the sensitive data was shared before any protection applied.
- **Assuming "internal" means "not sensitive".** Internal data can still be confidential or regulated. Check its classification.
- **Treating written rules as a reason to automate decisions about people.** High-stakes decisions about individuals need human judgment and accountability, whatever rules exist.
- **Installing popular third-party tools without vetting.** Popularity is not approval. Check what the Skill or connector does and what it can access.
- **Using a disclaimer instead of a fix.** "May contain errors" does not correct a biased or unverified output.
- **Calling every problem a training gap.** If the policy never covered the situation, retraining people on it changes nothing. Update the policy.
- **Banning the tool as the first response.** A blanket ban usually overcorrects when a safeguard or a clearer policy would close the gap.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, which is about 1.7 minutes per item. Governance stems often hide the deciding detail in one clause, such as what the policy permits or whether the tool is approved. Read the policy sentence twice, then budget about 1.5 to 2 minutes.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Three classifications | Appropriate, appropriate with safeguards, inappropriate, each with a documented rationale |
| Delegation limits | Consequential decisions about people stay with an accountable person |
| Third-party Skills and connectors | Check approval and data access before use |
| Anthropic Usage Policy | Part of assessing whether a use case is appropriate |
| Classification first | Know how data is classified before deciding what to share |
| Redaction and anonymization | Remove or replace identifying or confidential details before sharing |
| Upload judgment | Decide what can go into code-executed analysis before uploading |
| Memory and incognito | Control what is kept afterward; they do not undo disclosure |
| Policy gap | The policy does not cover the situation; update the policy |
| Training gap | The policy covers it but people do not know or follow it; train and reinforce |
| Ethical risks | Bias and transparency failures need correction, not disclaimers |
| Diligence | You remain accountable for AI-assisted work and must verify it |

## Practice items

These 4 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A secondary school teacher wants to use Claude to draft first-pass feedback comments on student essays. She plans to paste essays in with student names removed, then edit each comment herself before returning it. The school's policy allows AI drafting support if no student-identifying information is shared and the teacher stays responsible for the feedback. How should this use case be classified?

- A. Inappropriate, because AI should not be involved in giving feedback to students.
- B. Appropriate with no safeguards needed, because the feedback is only a draft.
- C. Appropriate with safeguards: names removed before sharing and the teacher reviewing every comment.
- D. Inappropriate unless each student's parents approve every individual comment.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The use is suitable and the policy permits it, but it depends on two safeguards: no identifying information shared and the teacher reviewing each comment. Naming those safeguards is what makes the classification complete.

- **A** is wrong because the policy allows this support, and the teacher stays responsible.
- **B** is wrong because the use is only acceptable because of the safeguards; dropping them would breach the policy.
- **D** is wrong because the policy does not require parental approval, and it adds a condition with no basis in the stem.

</details>

### Item 2

A customer insights analyst wants to use code-executed analysis on a large export of support chat transcripts to find common complaint themes. The export comes from a system she has never worked with before, and she does not know how the data is classified. The organization has a data classification policy. What should she do first?

- A. Upload it, because it is internal company data.
- B. Upload a small sample first and ask Claude whether it looks sensitive.
- C. Upload it and ask Claude to ignore any personal information it finds.
- D. Confirm the export's classification under the policy, with the data owner if needed, before uploading anything.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** She cannot decide what is safe to share until she knows how the data is classified. Classification comes first, and it decides whether she needs to redact, anonymize or not upload at all.

- **A** is wrong because internal data can still be confidential or regulated.
- **B** is wrong because uploading a sample still shares data before its classification is known.
- **C** is wrong because the data is disclosed by the upload, whatever Claude is asked to do with it afterward.

</details>

### Item 3

An IT governance lead notices that several staff have connected their personal Gmail accounts to the Projects they use for work. The organization's AI policy covers approved tools and data classification but says nothing about connectors or personal accounts. Staff say they assumed it was allowed. What should the governance lead do?

- A. Treat it as a policy gap: add a clear rule on connectors and personal accounts, then tell staff about it.
- B. Treat it as a training gap and retrain staff on the current policy.
- C. Discipline the staff who connected personal accounts.
- D. Do nothing, because the policy does not forbid it.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The policy does not cover the situation at all, so this is a policy gap. Adding a clear rule and communicating it closes the gap.

- **B** is wrong because there is nothing in the current policy to train staff on for this behavior.
- **C** is wrong because staff did not break a rule that existed.
- **D** is wrong because a real risk is left in place just because the policy has not caught up.

</details>

### Item 4

A recruiting team has been using Claude to rank applicants' résumés for a role, and the hiring manager interviews only the top ten. A recruiter notices that candidates with career breaks are consistently ranked low, even when their experience matches the job requirements. The organization's policy requires fair, job-related hiring criteria. What should the team do?

- A. Keep using the ranking, since it saves the team a lot of time.
- B. Treat it as a bias risk: stop using the ranking to filter candidates and have people assess them against the job criteria.
- C. Add an instruction asking Claude to be fair and keep ranking as before.
- D. Run the ranking in incognito chats so earlier memory does not influence it.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The pattern is an ethical risk that conflicts with a fair-hiring policy and affects real people's opportunities. Stopping the automated filtering and returning the assessment to people using job criteria closes the gap.

- **A** is wrong because time saved does not justify a biased filter that breaches policy.
- **C** is wrong because a general instruction does not show the bias is gone, and the ranking would still filter people out.
- **D** is wrong because nothing suggests memory is the cause, and incognito does not address bias in the ranking.

</details>

Want more? The free timed Associate, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate) to practise pacing across the whole exam.

---

---

---

<!-- nav:bottom -->
← Previous: [Domain 5: Configuration and Knowledge Management](05-configuration-and-knowledge-management.md) | Next: [Exam overview: Developer, Foundations](../developer/README.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
