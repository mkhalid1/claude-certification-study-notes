<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 2: Output Evaluation and Quality Control](02-output-evaluation-and-quality-control.md) · Next: [Domain 4: Workflow Design and Integration](04-workflow-design-and-integration.md)<!-- /nav:top -->

# Domain 3: Capability, Model, and Context Selection

Weight: 15% of scored items, about 9 items.

## What this domain measures

This domain tests whether you pick the right tool for the job inside Claude, and whether you manage context and memory well over long work. The guide lists three skills:

- **Capability selection.** Matching a task to the capabilities available (chat, Projects, Artifacts, research, code-executed analysis, Skills, connectors and memory) and choosing the combination that does the job best. Many tasks need more than one.
- **Model and effort selection.** Weighing model tiers and effort levels against a task's quality, speed and cost needs. That includes matching the tier to complexity and stakes, recognizing when the faster, lower-cost option is the right call, and being able to defend your choice against the alternatives.
- **Context and memory management.** Handling long or multi-session work: knowing when to restart, summarize, persist to a Project, or rely on memory. It also covers keeping memory accurate by reviewing, editing and deleting memories, and using incognito chats when a conversation should not shape memory.

Why it matters: the same request can be quick and reliable with the right capability, or slow and error-prone with the wrong one, and good selection is where most of the time and cost savings in daily Claude use come from.

The guide states that items do not test recall of published figures such as pricing, context-window sizes or rate limits. Expect judgment about fit, not numbers.

## How questions look

A typical stem describes a task with one feature that points to a capability: it needs current information from the web, exact computation, files that change over time, work that spans weeks, a standard document format, or a privacy wish about memory. You choose the capability or combination that fits. Model items describe a task's complexity, stakes and urgency and ask which tier and effort level to use, or which justification holds up. Context items describe a long chat that is degrading or memory that has gone stale.

## A method for these questions

1. Find the feature of the task that rules options in or out: current external information, exact numbers, ongoing work, shared files, repeatable format, or sensitivity.
2. Map that feature to a capability. Current web information points to research. Exact figures point to code-executed analysis. Ongoing work with shared material points to a Project. A live document in a workplace tool points to a connector. A repeatable procedure or document format points to a Skill. A standalone piece you will refine and reuse points to an artifact.
3. If the task has two such features, expect the answer to combine two capabilities.
4. For model choice, rate the task on complexity and stakes, then check urgency and volume. Simple, high-volume, low-stakes work suits a faster, lower-cost tier. Complex, high-stakes reasoning suits a more capable tier and higher effort.
5. For context problems, ask whether the issue is a long, cluttered conversation (summarize and restart, or persist to a Project) or stale memory (review, edit or delete).
6. Prefer the option that fixes the actual cause over a general upgrade such as a bigger model.

## Traps that cost points

- **Always picking the most capable model.** For simple, high-volume or low-stakes tasks, a faster, lower-cost tier gives adequate quality and is the better choice. The exam rewards matching the tier to the task.
- **Always picking the cheapest model.** Complex, high-stakes analysis justifies a more capable tier and more effort, especially when there is time.
- **Using model knowledge for current events.** When a task needs recent, cited information from the web, use research rather than a plain chat answer.
- **Trusting text answers for exact numbers.** Exact computation belongs in code-executed analysis.
- **Running weeks of work in one chat.** Long conversations degrade. Summarize decisions and restart, or persist shared material and instructions in a Project.
- **Turning off memory as a cure for one bad memory.** When a specific memory is wrong or out of date, review and edit or delete it.
- **Assuming memory only keeps "relevant" things.** If you do not want a conversation to shape future chats, use an incognito chat rather than hoping it will not be remembered.
- **Using memory as a data store.** Figures and documents that a workflow needs each month belong in a connected source or Project knowledge, not memory.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, which is about 1.7 minutes per item. Capability items are often quick once you spot the deciding feature, so aim for 1 to 1.5 minutes. Model-selection items with a "defend the choice" angle can take longer; read each justification for an absolute claim ("always", "never") that makes it wrong.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Chat | Quick, one-off tasks that do not need shared material or persistence |
| Projects | Ongoing work with shared knowledge and instructions across many chats |
| Artifacts | Standalone content you want to view, refine and reuse outside the chat flow |
| Research | Current information gathered from sources, with citations to check |
| Code-executed analysis | Computation that must be exact, on uploaded or connected data |
| Skills | Repeatable procedures or standard document formats, pre-built or approved custom |
| Connectors | Live access to workplace sources such as Google Drive, Gmail and calendar |
| Memory | Carries context across chats; review, edit and delete it to keep it accurate |
| Incognito chats | For conversations that should not shape memory or appear in history |
| Model tier and effort | Match to complexity, stakes, speed and cost; be able to defend the choice |
| Long-chat degradation | Summarize, restart, or persist to a Project |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A business development manager needs a briefing on how competitors in her sector have responded to a new data-privacy law announced last month. She wants each point backed by a source she can check before the briefing goes to her director. She has no internal documents on the topic. Which capability fits best?

- A. Research, so Claude gathers current information from sources and cites them for her to check.
- B. A standard chat, relying on what Claude already knows about the sector.
- C. A Project loaded with last year's competitor analysis documents.
- D. Memory, so Claude recalls what she has told it about competitors in earlier chats.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The task needs recent information with sources she can verify, which is what research provides. The citations give her something concrete to check before the briefing goes up.

- **B** is wrong because a plain chat answer from model knowledge may not cover a law announced last month and gives no sources to check.
- **C** is wrong because last year's documents predate the law.
- **D** is wrong because her earlier chats do not contain current competitor responses.

</details>

### Item 2

A consultant is starting a three-month engagement with one client. She will refer to the same set of client documents, a glossary of the client's terms, and the same working style in dozens of separate chats over that time. Last engagement she pasted the documents into each new chat and lost hours. What should she set up?

- A. One long chat for the whole engagement, so everything stays in one place.
- B. A saved prompt that she pastes, with the documents, into each new chat.
- C. Memory, by telling Claude about the documents once so it recalls them later.
- D. A Project for the engagement, with the documents as knowledge and her working style as instructions.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Ongoing work across many chats with the same material is the core use for a Project. Knowledge holds the documents and instructions hold the working style, so each new chat starts ready.

- **A** is wrong because a single chat over three months will become long and cluttered, and quality degrades.
- **B** is wrong because that repeats the manual effort that cost her hours last time.
- **C** is wrong because memory is not a store for full documents.

</details>

### Item 3

A training coordinator is building a one-page onboarding checklist with Claude. She expects to revise it several times over the afternoon, view it on its own without scrolling through the conversation, and then download it to share. Which capability fits this best?

- A. An inline chat response, which she copies out after each revision.
- B. An artifact, so the checklist sits on its own and updates as she revises it.
- C. Research, so the checklist reflects current onboarding practices.
- D. A connector to her calendar, so the checklist links to onboarding dates.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** She wants a standalone piece of content to view, refine over several rounds and reuse, which is what artifacts are for. Each revision updates the same piece instead of scattering versions through the chat.

- **A** is wrong because copying out each revision from the chat is the clumsy pattern artifacts avoid.
- **C** is wrong because the need is about building and revising a document, not gathering outside information.
- **D** is wrong because calendar access does not help her view, revise or download the checklist.

</details>

### Item 4

A support operations lead is choosing models for several tasks. He wants to use a faster, lower-cost model tier wherever quality would still be adequate. For which two tasks is the faster, lower-cost tier the best choice? (Choose two.)

- A. Drafting a recommendation to the executive team that weighs several conflicting strategic trade-offs.
- B. Working through a multi-step analysis of how a contract change affects three linked agreements.
- C. Sorting 500 short support tickets into five categories with clear, written rules.
- D. Synthesizing conflicting research findings into a single position for a board paper.
- E. Reformatting a list of meeting dates and attendees into a clean table.

<details>
<summary>Answer and explanation</summary>

**Answer: C and E.** Both are simple, well defined and low stakes, and the ticket sorting is high volume. A faster, lower-cost tier handles that kind of work well, so a more capable tier would add cost and time without a matching gain.

- **A** is wrong because weighing conflicting strategic trade-offs for executives is complex and high stakes.
- **B** is wrong because multi-step reasoning across linked agreements benefits from a more capable tier.
- **D** is wrong because synthesizing conflicting findings for a board is complex, high-stakes work.

</details>

### Item 5

A strategy manager is preparing an analysis of how two business units should be combined after an acquisition. The work involves many interdependent decisions, and errors would affect hundreds of staff. The analysis is due next week, so speed is not a concern today. Which choice is easiest to defend?

- A. The fastest tier, because it lowers cost and he can always run it again.
- B. A more capable tier with a higher effort level, because the task is complex and high stakes and there is time.
- C. The fastest tier, followed by asking Claude to check its own reasoning.
- D. Whatever model is set by default, because effort level makes no difference to analysis.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Complexity and stakes are both high, and the deadline allows for slower, deeper work. That combination is where a more capable tier and higher effort earn their cost.

- **A** is wrong because rerunning a weaker analysis does not make a complex task simple.
- **C** is wrong because a self-check by the same fast setting does not add the depth the task needs.
- **D** is wrong because effort level is a real lever on reasoning depth.

</details>

### Item 6

A project manager has used one chat for three weeks to plan a product launch. Recently Claude has started forgetting decisions made in the first week and contradicting itself on dates. She still has two weeks of planning left. What is the best next step?

- A. Ask Claude to summarize the decisions so far and open questions, check that summary, and continue in a fresh chat or a Project with it.
- B. Keep working in the same chat and repeat the early decisions whenever Claude forgets them.
- C. Switch to a more capable model in the same chat so it can hold more detail.
- D. Turn on incognito mode so the long history stops affecting the answers.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The chat has become long enough that early decisions are being lost. A checked summary carried into a fresh chat or persisted in a Project gives Claude the decisions in a clean, compact form for the remaining work.

- **B** is wrong because it adds even more length to a chat that is already degrading.
- **C** is wrong because the cluttered history stays the same.
- **D** is wrong because incognito controls memory and history for new chats; it does not repair an existing long conversation.

</details>

### Item 7

An employee moved from the marketing team to HR two months ago. Claude still often assumes she works in marketing, suggests campaign ideas she did not ask for, and refers to her old manager. Everything else about her experience with memory has been helpful. What should she do?

- A. Remind Claude at the start of every chat that she now works in HR.
- B. Turn off memory entirely so nothing old can affect future chats.
- C. Create a new Project and do all her work there from now on.
- D. Review her memories and edit or delete the ones about her old role and manager.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The problem is specific out-of-date memories. Reviewing and correcting or deleting them fixes the cause and keeps the parts of memory that work well.

- **A** is wrong because it treats the symptom every time instead of fixing the stored memory.
- **B** is wrong because it throws away memory she finds helpful to fix a few stale entries.
- **C** is wrong because the outdated memories would still be there and still be wrong.

</details>

### Item 8

A marketing specialist wants to spend an hour exploring ideas for a friend's unrelated home bakery. She does not want any of it to shape Claude's suggestions in her normal work chats afterward. Her work chats rely on memory and she wants to keep it on. What is the best approach?

- A. Use a normal chat, since memory only keeps details that relate to her job.
- B. Create a separate Project for the bakery and work there.
- C. Use an incognito chat for the bakery session.
- D. Use a normal chat and tell Claude at the end to forget the conversation.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Incognito chats are meant for conversations that should not shape memory or appear in chat history. She keeps memory on for her normal work and keeps this session out of it.

- **A** is wrong because there is no basis for assuming memory will skip unrelated details.
- **B** is wrong because a Project organizes ongoing work; it is not the tool for keeping a one-off session out of memory.
- **D** is wrong because asking in the chat is not a reliable control over what memory keeps.

</details>

### Item 9

An operations analyst prepares a monthly performance report. The source spreadsheet is updated by the finance team in a shared Google Drive folder, and the report needs exact totals and growth percentages. Last month he downloaded an old version by mistake and his estimates were off. Which two capabilities should the workflow use? (Choose two.)

- A. A Google Drive connector, so Claude works from the current file in the shared folder.
- B. Research, so Claude can find the latest figures on the web.
- C. Memory, so Claude remembers last month's numbers for comparison.
- D. Code-executed analysis, so the totals and percentages are computed exactly.
- E. An incognito chat, so the figures are not stored after the session.

<details>
<summary>Answer and explanation</summary>

**Answer: A and D.** The connector solves the stale-file problem by reading the current file where finance maintains it. Code-executed analysis provides the exact computation the report needs.

- **B** is wrong because the figures are internal and live in Drive, not on the web.
- **C** is wrong because memory is not a reliable store for figures the report depends on.
- **E** is wrong because the stem raises no sensitivity concern, and incognito does nothing for accuracy or freshness.

</details>

### Item 10

A team lead uses a faster, lower-cost model tier to summarize each day's short standup notes into three bullets for the team channel. A colleague says he should always use the most capable tier. The summaries are read by the team the same morning, and the team corrects anything that is off. Which justification for his choice is strongest?

- A. The lowest-cost option is always the right one when using Claude at work.
- B. The task is simple, low stakes and repeated daily, and the faster tier gives adequate quality at lower cost and time.
- C. Faster tiers are more accurate than capable tiers on short pieces of text.
- D. The most capable tier is not able to work with short notes.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The defense rests on matching the tier to the task: simple, low stakes, frequent and quickly corrected. That is when the faster, lower-cost option is the right call.

- **A** is wrong because "always cheapest" ignores complexity and stakes, and would be wrong for hard tasks.
- **C** is wrong because there is no basis for claiming faster tiers are more accurate.
- **D** is wrong because more capable tiers can handle short text; they are simply not needed here.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Associate, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-associate?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-associate), which reports percent correct by domain.

---

---

<!-- nav:bottom -->← Previous: [Domain 2: Output Evaluation and Quality Control](02-output-evaluation-and-quality-control.md) | Next: [Domain 4: Workflow Design and Integration](04-workflow-design-and-integration.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
