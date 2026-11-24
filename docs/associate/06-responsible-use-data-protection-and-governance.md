<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 5: Configuration and Knowledge Management](05-configuration-and-knowledge-management.md) · Next: [Exam overview: Developer, Foundations](../developer/README.md)<!-- /nav:top -->

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

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

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

A benefits administration team proposes using Claude to read applications for hardship support and send automatic approval or rejection decisions to applicants, with no person reviewing them. The team argues that the eligibility rules are clearly written. A rejection can leave an applicant without support for months. How should this proposal be classified?

- A. Inappropriate as proposed, because a consequential decision about individuals would be made with no human review.
- B. Appropriate with safeguards, provided a manager audits a sample of decisions each quarter.
- C. Appropriate, because the eligibility rules are objective and clearly written.
- D. Appropriate, provided the decisions are made in incognito chats.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The decision has serious consequences for individuals and the proposal removes all human judgment and accountability. The rationale should be documented, and a redesign could still have Claude prepare case summaries for a person who decides.

- **B** is wrong because a quarterly sample audit finds harmful decisions long after applicants have been affected.
- **C** is wrong because clear rules do not remove the stakes or the need for an accountable decision maker.
- **D** is wrong because incognito controls what is retained; it has nothing to do with who makes the decision.

</details>

### Item 3

A project manager finds a third-party Skill on a public website that claims to turn meeting notes into project plans. To work, it asks for access to the team's Google Drive. The organization keeps a list of approved Skills and connectors, and this one is not on it. Which two actions should come before anyone uses it? (Choose two.)

- A. Install it, since a large number of people have already downloaded it.
- B. Check the approved list and, if it is missing, request review through the organization's approval process.
- C. Install it only in an incognito chat so any data it touches is not kept.
- D. Test it on a real client folder first to see whether it is useful enough to request approval.
- E. Find out what the Skill does and exactly what data it can access in Drive.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** Third-party Skills and connectors need to be trusted before use. That means going through the organization's approval process and understanding what the tool does and what data it can reach.

- **A** is wrong because popularity is not approval and says nothing about what the tool does with data.
- **C** is wrong because incognito does not limit what an unvetted tool can access in Drive.
- **D** is wrong because testing on real client data exposes it before the tool is trusted.

</details>

### Item 4

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

### Item 5

A corporate development associate is drafting internal memos about a confidential acquisition that has not been announced. The organization's policy allows AI drafting support for confidential deals only if the parties cannot be identified from what is shared. She needs help structuring the memos and tightening the language. What approach is consistent with the policy?

- A. Use the real company names, since her chats are private to her account.
- B. Replace the company names and other identifying details with codenames before sharing anything with Claude.
- C. Use the real names and ask Claude not to remember any of the details.
- D. Avoid Claude entirely for the whole deal, even for structure and language.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The policy requires that the parties cannot be identified, so identifying details must be replaced before they are shared. Codenames let her get the drafting help the policy permits.

- **A** is wrong because private chats do not meet a policy that forbids sharing identifiable deal details.
- **C** is wrong because asking Claude not to remember does not change what was shared.
- **D** is wrong because the policy allows this help when the parties are not identifiable, so a total ban is more than the policy requires.

</details>

### Item 6

The organization's AI policy clearly states that any AI-assisted content sent to customers must be reviewed by the sender first. An audit finds that several sales reps have been sending Claude-drafted emails to prospects without reading them. When asked, most reps say they did not know about the rule. What is the most appropriate corrective action?

- A. Rewrite the policy to add a rule about reviewing customer-facing content.
- B. Ban the sales team from using Claude.
- C. Treat it as a training gap: give the reps targeted training on the existing rule and reinforce the review step.
- D. Take no action, since the emails were mostly accurate.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The policy already covers this behavior, and the reps did not know about it. That is a training gap, and targeted training with a reinforced review step closes it.

- **A** is wrong because the rule already exists, so rewriting the policy does not reach the people who did not know it.
- **B** is wrong because a ban overcorrects when the problem is awareness of an existing rule.
- **D** is wrong because unreviewed customer content breaches policy whether or not it happened to be accurate.

</details>

### Item 7

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

### Item 8

A communications team publishes weekly thought-leadership articles under the chief executive's name. The articles are drafted with Claude, and the chief executive has not been reading them before they go out. One recent article contained a statistic nobody could trace to a source. Which two corrective actions best address the problem? (Choose two.)

- A. Stop using Claude for any writing in the communications team.
- B. Add a note to each article saying it may contain errors and keep publishing as before.
- C. Require the named author to review and approve each article before it is published.
- D. Verify every factual claim and statistic against a source before publication.
- E. Ask Claude to check its own drafts for accuracy before each article goes out.

<details>
<summary>Answer and explanation</summary>

**Answer: C and D.** Publishing under someone's name without their review is a transparency failure, and the untraceable statistic is a Diligence lapse. Author approval and independent verification of facts close both gaps.

- **A** is wrong because a ban overcorrects; the problems are missing review and verification.
- **B** is wrong because a disclaimer leaves unverified content in place under the executive's name.
- **E** is wrong because Claude checking its own drafts is not independent verification.

</details>

### Item 9

A procurement analyst asked Claude to summarize a supplier contract and pasted the summary into a decision memo without reading the contract. The summary left out an early-termination clause, and the memo's recommendation depended on being able to exit early. The memo has been sent to a manager but no decision has been made yet. What is the right corrective action?

- A. Stop using Claude for contracts and switch to a different tool.
- B. Ask Claude to summarize the contract again and replace the old summary.
- C. Add a note to the memo saying it was prepared with AI assistance.
- D. Check the summary against the contract, correct the memo, and add a key-clause check to the team's memo process.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** This is a Diligence lapse: the analyst stayed accountable but did not verify the summary. Checking against the source fixes this memo, and a process check prevents the same lapse next time.

- **A** is wrong because the failure was a skipped verification step, which would recur with any tool.
- **B** is wrong because a new summary is still unverified.
- **C** is wrong because a disclosure note does not correct the missing clause or the recommendation.

</details>

### Item 10

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

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate), which reports percent correct by domain.

---

---

<!-- nav:bottom -->← Previous: [Domain 5: Configuration and Knowledge Management](05-configuration-and-knowledge-management.md) | Next: [Exam overview: Developer, Foundations](../developer/README.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
