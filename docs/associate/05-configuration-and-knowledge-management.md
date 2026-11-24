<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 4: Workflow Design and Integration](04-workflow-design-and-integration.md) · Next: [Domain 6: Responsible Use, Data Protection, and Governance](06-responsible-use-data-protection-and-governance.md)<!-- /nav:top -->

# Domain 5: Configuration and Knowledge Management

Weight: 15% of scored items, about 9 items.

## What this domain measures

This domain is about setting Claude up so that recurring work comes out right every time, and keeping that setup healthy as things change. The guide lists four skills:

- **Project configuration.** Configuring a Project for a defined recurring use case with instructions, knowledge sources, Skills and scoped memory. The key judgment is deciding, for each requirement, whether it belongs in an instruction, a knowledge source or a Skill, and keeping unrelated work streams apart.
- **Connector and knowledge management.** Giving a workflow the sources it needs: connecting sources such as Google Drive and Gmail, weighing upload against connector, curating uploads, and keeping connected knowledge current, relevant and free of noise.
- **Persistent-instruction design.** Writing Project instructions and other persistent instructions for repeated interactions that need consistent behavior. That includes dividing the work between persistent instructions and per-request prompts, and anticipating the cases and constraints that keep outputs uniform.
- **Configuration lifecycle and degradation.** Diagnosing an established setup whose output quality has changed by checking each layer (instructions, knowledge freshness, Skill versions and accumulated memory), finding the layer responsible and restoring performance.

Why it matters: a well-configured Project turns a good prompt into a reliable team tool, and knowing which layer to fix saves hours of guessing when quality slips.

## How questions look

Configuration stems describe a recurring use case and one requirement, then ask where it belongs: instruction, knowledge or Skill, or upload or connector. Degradation stems describe a setup that used to work and a specific change in output, along with what has and has not changed. You pick the layer to fix first. Some items ask for two actions.

Distractors usually suggest rewriting instructions when the instructions did not change, switching models when the problem is grounding, or using memory or per-request prompts for things that should be configured.

## A method for these questions

1. For a "where does it go" question, sort the requirement into one of three kinds. A rule about how Claude should behave every time goes in instructions. Reference material Claude should draw on goes in knowledge. A repeatable procedure or standard output format goes in a Skill.
2. For a source question, ask how often the source changes and who maintains it. A live, changing source in a workplace tool suits a connector. A fixed, final document suits an upload.
3. For noise problems, look for duplicates, drafts and superseded versions in knowledge, and curate.
4. For isolation problems, give unrelated work streams their own Projects with their own knowledge and memory.
5. For degradation, list the layers: instructions, knowledge, Skill version, memory. Cross out any the stem says are unchanged. Match the symptom to the layer that did change.
6. Fix the responsible layer, then confirm with a test on the same input.

## Traps that cost points

- **Rewriting instructions that have not changed.** If the instructions are the same as when output was good, they cannot explain a change in output.
- **Switching model to fix a grounding problem.** A different model or effort level does not fix stale knowledge, a changed Skill or polluted memory.
- **Putting behavior rules in a knowledge file.** A style rule that must apply every time is clearer and more reliable as an instruction.
- **Putting weekly specifics into persistent instructions.** Details that change per task belong in the per-request prompt; editing the instructions each week makes behavior unstable for everyone.
- **Uploading a copy of a document that changes.** The copy goes stale. Connect to the live source when the owner updates it regularly.
- **Adding more files to fix confused answers.** When knowledge holds drafts and old versions, more files add more noise. Curate first.
- **One Project for unrelated clients or work streams.** Shared knowledge and memory let details bleed across. Separate them.
- **Leaving gaps in instructions.** If instructions do not say what to do when information is missing, Claude may fill the gap with plausible content.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, which is about 1.7 minutes per item. Degradation items reward a quick elimination pass: cross out every layer the stem says is unchanged, and the answer is often what remains. Aim for about 1.5 minutes on these.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Project instructions | Stable rules for how Claude behaves in every chat in the Project |
| Knowledge sources | Reference material Claude draws on; keep it current and curated |
| Skills | Repeatable procedures and standard output formats; track versions |
| Scoped memory | Memory kept within a Project, separate from other work streams |
| Instruction, knowledge or Skill | Behavior rule, reference material, or repeatable procedure |
| Upload versus connector | Upload for fixed documents; connector for live sources that change |
| Curation | Remove duplicates, drafts and superseded versions from knowledge |
| Persistent versus per-request | Stable rules persist; task specifics go in the request |
| Anticipating cases | Instructions should cover edge cases such as missing information |
| Degradation layers | Instructions, knowledge freshness, Skill version, accumulated memory |
| Layer attribution | Rule out unchanged layers, then fix the one that changed |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A communications team uses a Project to draft replies to press enquiries. Every reply must be in British English, must stay under 150 words and must never speculate about unreleased products. These rules apply to every chat in the Project, and team members keep forgetting to include them in their requests. Where should these requirements go?

- A. In a style guide document uploaded as a knowledge source.
- B. In the Project instructions.
- C. In a Skill that formats press replies.
- D. In each team member's request, using a shared reminder note.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** These are stable behavior rules that must apply to every chat, which is what Project instructions are for. Putting them there means no one has to remember them per request.

- **A** is wrong because reference material in knowledge is drawn on as needed; must-follow rules are clearer and more reliable as instructions.
- **C** is wrong because a Skill suits a repeatable procedure or format, not a set of standing behavior rules.
- **D** is wrong because relying on each request is the problem the team already has.

</details>

### Item 2

A bid team writes responses to formal requests for proposals. Each response follows the same multi-step procedure and must come out in the same document structure with set sections. The team has an approved custom Skill that does this. The Project already has instructions for tone and knowledge with past bids. Where should the procedure and document structure live?

- A. In the approved Skill, attached to the Project.
- B. Pasted into the start of every chat as a long prompt.
- C. In memory, so Claude remembers the procedure over time.
- D. In knowledge, by uploading more past bids and hoping Claude copies the structure.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** A repeatable procedure with a standard output structure is the job of a Skill, and an approved one already exists. Attaching it to the Project makes every response follow the same steps and format.

- **B** is wrong because pasting a long prompt each time is error-prone and depends on each person.
- **C** is wrong because memory is not a reliable way to hold a defined procedure.
- **D** is wrong because examples do not guarantee Claude follows a specific structure, and they add noise.

</details>

### Item 3

A team is deciding how to give its Project access to several sources. It can upload files or use connectors such as Google Drive and Gmail. For which two sources is a connector the better choice? (Choose two.)

- A. A single signed contract that is final and will not change.
- B. A PDF of slides from a conference the team attended last year.
- C. A shared Drive folder of policies that the policy owner updates every week.
- D. The team's ongoing Gmail threads with a vendor, needed for summaries of the latest correspondence.
- E. An archived dataset that has been frozen for a one-time analysis.

<details>
<summary>Answer and explanation</summary>

**Answer: C and D.** Both are live sources that change after you would have uploaded them. A connector reads the current version where it is maintained, so Claude works from the latest policies and emails.

- **A** is wrong because a final document does not change, so an upload is simple and stays accurate.
- **B** is wrong because a past conference deck is fixed and suits an upload.
- **E** is wrong because a frozen dataset for one analysis does not need live access.

</details>

### Item 4

A Project for answering staff questions about expense rules has 60 knowledge files. They include three drafts of the expense policy, two copies of the final policy and an older version from two years ago. Answers sometimes mix rules from different versions. What should the Project owner do?

- A. Add an instruction telling Claude to always use the most recent version.
- B. Upload more examples of correct answers so Claude learns the right rules.
- C. Switch to a more capable model so it can tell the versions apart.
- D. Remove the drafts, duplicates and old version, and keep only the authoritative final policy.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The knowledge is noisy, with several conflicting versions of the same document. Curating it so only the authoritative version remains removes the source of mixed answers.

- **A** is wrong because the conflicting files remain, and it may not be clear which is "most recent".
- **B** is wrong because more files add more noise to a knowledge base that is already confused.
- **C** is wrong because the problem is conflicting sources, not reasoning ability.

</details>

### Item 5

A freelance consultant works for two competing clients. She uses one Project for both, with both clients' documents in its knowledge. In a draft for the first client, Claude referred to a product launch plan that belongs to the second client. She wants to prevent this from happening again. What should she do?

- A. Add a Project instruction telling Claude not to mix up the two clients.
- B. Use incognito chats for all work for both clients.
- C. Create a separate Project for each client, each with its own knowledge and memory.
- D. Delete memory once a week to clear out details from both clients.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Unrelated work streams should be isolated. Separate Projects mean each client's knowledge and memory are only available when working for that client.

- **A** is wrong because both clients' documents stay in the same knowledge, so the risk remains.
- **B** is wrong because incognito does not separate the knowledge in the Project, and it loses useful context.
- **D** is wrong because the shared knowledge is still in one Project, and weekly deletion does not stop mixing in between.

</details>

### Item 6

A team's Project instructions include stable rules about tone and format. Each Monday a team lead edits the instructions to add that week's campaign topic and deadline. Colleagues who use the Project for other campaigns now get outputs about the wrong topic. What is the best fix?

- A. Keep stable rules in the instructions and put each week's topic and deadline in the per-request prompt.
- B. Ask colleagues to tell Claude to ignore the campaign mentioned in the instructions.
- C. Create a new Project every week for that week's campaign.
- D. Move the tone and format rules into a knowledge file and leave only the weekly topic in the instructions.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Persistent instructions should hold what stays the same across requests. Details that change per task belong in the request, so they do not affect everyone else's work.

- **B** is wrong because it makes every colleague work around a misplaced instruction.
- **C** is wrong because it multiplies setup work and does not fix where the details belong.
- **D** is wrong because it moves stable rules to the wrong place and keeps the changing detail in the instructions.

</details>

### Item 7

A recruiting team uses a Project to draft job advertisements. The instructions set the structure and tone, and knowledge holds the benefits summary. When a hiring manager leaves out details such as the work location, Claude writes plausible but invented details to complete the ad. What change to the configuration would best prevent this?

- A. Ask every hiring manager to remember to include all details.
- B. Add an instruction to mark missing details with a clear placeholder and never invent them.
- C. Switch to a more capable model so it guesses details more accurately.
- D. Upload more past job ads so Claude has more examples to draw on.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The instructions did not anticipate the case where information is missing, so Claude filled the gap. Telling it how to handle missing details makes outputs uniform and stops invented content.

- **A** is wrong because it depends on every person remembering, which is the failure already happening.
- **C** is wrong because a better guess is still invented content.
- **D** is wrong because past ads give Claude more material to borrow wrong details from.

</details>

### Item 8

A Project has produced a well-structured weekly newsletter draft for months. For the last two weeks, drafts have dropped the usual section structure. The Project instructions have not been edited, and the knowledge files are refreshed every week as they always have been. Two weeks ago, a colleague installed an updated version of the newsletter Skill the Project uses. Which two actions should the team take first? (Choose two.)

- A. Rewrite the Project instructions with stricter rules about section structure.
- B. Compare the new Skill version with the previous one for changes to the section structure.
- C. Delete all of the Project's memories to remove anything that might be interfering.
- D. Switch to a more capable model and regenerate the last two drafts.
- E. Run the previous Skill version on the same input to confirm whether the structure returns.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** The only layer that changed when the problem started is the Skill version. Comparing the versions and testing the previous one on the same input confirms the cause and points to the fix.

- **A** is wrong because the instructions are unchanged and cannot explain the change in output.
- **C** is wrong because nothing in the symptoms points to memory, and deleting it all throws away useful context.
- **D** is wrong because a model change does not address a change in the Skill that shapes the format.

</details>

### Item 9

A learning team's Project answers staff questions about the training calendar. The Project holds an uploaded copy of the calendar from January. The calendar owner updates the live document in Google Drive every month, but nobody re-uploads it. Staff are now being told about sessions that were moved or cancelled. What is the best long-term fix?

- A. Re-upload the calendar once a year at the start of the year.
- B. Add an instruction telling Claude to give only current session dates.
- C. Rely on memory to track which sessions have changed.
- D. Replace the uploaded copy with a Google Drive connector to the live calendar.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The source changes every month and lives in Drive, so a static upload goes stale. A connector to the live document keeps the Project's knowledge current without anyone re-uploading it.

- **A** is wrong because a yearly upload is still stale for eleven months of the year.
- **B** is wrong because an instruction cannot make Claude aware of changes in a document it does not have.
- **C** is wrong because memory is not a reliable way to track changes to a source document.

</details>

### Item 10

A Project produces detailed monthly client reports and short weekly summaries. Last week, a team member added "Keep every response brief" to the Project instructions to make the weekly summaries tighter. Since then, the monthly reports have also become too short. Knowledge, Skills and memory have not changed. What should the team do?

- A. Revise the new instruction so brevity applies to the weekly summaries and not to the monthly reports.
- B. Refresh the knowledge files to restore the report length.
- C. Delete the Project's memories in case they are shortening the reports.
- D. Update the report Skill to a newer version.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The problem began with an instruction change, and every other layer is unchanged. Scoping the instruction to the case it was meant for restores the reports and keeps the tighter summaries.

- **B** is wrong because knowledge has not changed and does not control response length.
- **C** is wrong because memory is unchanged and the cause is already clear.
- **D** is wrong because the Skill has not changed, and a new version adds a new variable.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 4: Workflow Design and Integration](04-workflow-design-and-integration.md) | Next: [Domain 6: Responsible Use, Data Protection, and Governance](06-responsible-use-data-protection-and-governance.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
