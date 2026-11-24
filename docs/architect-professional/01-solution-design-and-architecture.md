<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Exam overview: Architect, Professional](README.md) · Next: [Domain 2: Claude Models, Prompting, and Context Engineering](02-models-prompting-and-context-engineering.md)<!-- /nav:top -->

# Domain 1: Solution Design and Architecture

Weight: 18% of scored items, about 11 items.

## What this domain measures

This domain tests whether you can turn a business process into a Claude architecture and defend each choice. The guide groups it into six skills:

- **Delegation between Claude and people.** Decide which steps Claude does, which people keep, and which are shared, and justify each by reversibility, stakes and accountability.
- **End-to-end architecture review.** Check an input, processing, output and feedback design against scalability, maintainability and access-control requirements, and name the component that breaks first under a stated load or trust condition.
- **Pattern selection.** Choose between a workflow, an agent, an augmented LLM and a Skills-based design given reuse, governance and risk constraints, and explain where the pattern puts the delegation boundary.
- **Orchestrator and subagent decomposition.** Judge a multi-agent split against observability and error-recovery needs, and spot where task boundaries, control flow or result synthesis will fail.
- **Value-based decisions.** Pick the option that moves a measurable business outcome under a cost or SLA ceiling, using value pillars such as efficiency, transformation, productivity, solution cost and performance SLAs.
- **Deployment surface and agent runtime.** Choose a surface (Anthropic API, Amazon Bedrock, Google Vertex AI, Microsoft Foundry) and a runtime (your own Messages API loop, a self-hosted Agent SDK or headless Claude Code, or Claude Managed Agents) under residency, compliance, model-availability and operational-ownership constraints, and state what the choice costs in latency, features, hosting, session state, sandboxing and data custody.

In real work, these are the decisions that are expensive to reverse. A wrong surface or a wrong agent split can cost a quarter of rework.

## How questions look

Most items describe a client or internal team, a business process, and three or four requirements that pull in different directions. One requirement is binding: a regulator, a residency attestation, a named accountable person, an SLA, or a team that cannot run infrastructure. The options are usually four plausible architectures. Some are better in general, but only one respects the binding constraint and states the trade-off honestly.

Expect artifacts in prose form: a description of data flow, a list of subagents and what they return, a load profile, or a set of competing proposals with expected value. Some items ask you to name the component that fails first rather than to choose a design. Options that claim every requirement is met with no trade-off are a common distractor.

## A method for these questions

1. Underline every requirement in the stem, then mark which one is hard (legal, contractual, physical) and which ones are preferences.
2. Eliminate any option that breaks the hard requirement, however attractive its other features.
3. For delegation items, rank each step by reversibility and stakes, and check who is named as accountable. Irreversible or accountable decisions stay with a person.
4. For pattern items, ask how predictable the steps are. Fixed steps favour a workflow. Open-ended exploration favours an agent. Shared, reusable procedures favour Skills. A single call with retrieval favours an augmented LLM.
5. For decomposition items, trace one failure through the design. Ask what the orchestrator sees, whether it can retry one part, and whether the final answer can be traced to its sources.
6. For "fails first" items, apply the stated load or trust condition to each component in order and stop at the first one that cannot meet it.
7. Prefer the option that names the trade-off it accepts. Be suspicious of answers that claim none.

## Traps that cost points

- **Choosing the most capable architecture by default.** An agent with many tools looks powerful, but if the steps are fixed and auditors want the same path every run, a workflow is the correct answer.
- **Treating zero data retention as a residency control.** Retention governs how long data is kept. Residency governs where it is processed. They answer different obligations.
- **Letting the model own accountable decisions because it is consistent.** Consistency does not transfer legal accountability. If a person must sign, a person decides.
- **Putting access control in the UI.** If retrieval or tools can reach data the user may not see, the leak happens before any UI filter runs.
- **Splitting work by size instead of by dependency.** Even file counts or alphabetical splits cut across shared interfaces and produce inconsistent results that synthesis cannot repair.
- **Restarting a whole multi-agent run on one failure.** It hides which part failed and wastes the work that succeeded. Recovery should be per subtask.
- **Picking the feature with the biggest wow factor.** Value items reward the option that moves the stated metric under the stated ceiling, not the most impressive capability.
- **Assuming every runtime is available on every surface.** Surface choice constrains runtime options and feature timing. Answers that ignore this are usually wrong.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 1 stems are long, so read the question line first, then the stem with that question in mind. If you can eliminate two options in the first minute, decide between the last two on the binding constraint and move on. Flag items where you are torn between two architectures and come back with fresh eyes.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Delegation test | Reversibility, stakes and accountability decide who does a step. Irreversible or signed decisions stay human. |
| Workflow pattern | Code defines the steps. Predictable, auditable, easy to test. Best for fixed processes. |
| Agentic pattern | The model chooses steps and tools in a loop. Best for open-ended tasks where the path cannot be known in advance. |
| Augmented LLM | One model call enhanced with retrieval, tools or memory. Often enough for single-step tasks. |
| Skills-based pattern | Reusable, versioned procedures loaded only when relevant. Fits shared know-how across many agents or teams. |
| Delegation boundary | Every pattern sets where model judgment ends and code or people take over. Be able to say where it sits. |
| Fails first | Apply the stated load or trust condition to each component in order. The first that cannot meet it is the answer. |
| Decomposition | Split by dependency and responsibility, return structured results, recover per subtask, keep a trace from claim to source. |
| Value pillars | Efficiency, transformation, productivity, solution cost, performance SLAs. Match the option to the pillar the stem funds. |
| Surface choice | Residency and existing attestations often force a cloud surface. The trade-offs can include feature timing and owning more of the runtime. |
| Runtime choice | Your own loop or self-hosted SDK means you own hosting, session state and sandboxing. A managed runtime shifts that work to Anthropic. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A regional insurer wants Claude in its property claims process. The steps are: classify incoming claim emails, extract policy and damage details, draft a repair estimate, decide whether a claim is denied, and write the customer letter. State regulation requires that a named, licensed adjuster is accountable for every denial, and denied customers have a right to appeal that decision. Which delegation design is most appropriate?

- A. Claude decides and sends denials automatically for claims below a value threshold, and adjusters review denials above it.
- B. Claude classifies, extracts and drafts the estimate and letters, and a licensed adjuster makes and signs every denial decision using Claude's summary.
- C. Adjusters handle intake and extraction, and Claude makes the coverage decisions because it applies policy wording more consistently.
- D. Claude handles every step, and a weekly sample audit by the claims manager provides accountability.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The binding constraint is that a named, licensed person is accountable for each denial. Claude can safely do the reversible, low-stakes work (classification, extraction, drafts) that a person then reviews, while the denial itself stays with the adjuster who signs it.

- **A** is wrong because a value threshold does not change who must be accountable. Low-value denials still need a licensed adjuster.
- **C** is wrong because it inverts the split. It gives the accountable decision to Claude and the low-risk clerical work to people.
- **D** is wrong because a sample audit after the fact cannot make a person accountable for each individual denial.

</details>

### Item 2

A law firm proposes a matter-research assistant. Lawyers ask questions in a web app, a retrieval service searches one vector index that holds documents from all client matters, Claude writes the answer, and thumbs-up ratings feed a weekly prompt review. Ethical walls forbid some lawyers from seeing certain matters, and the design enforces this by hiding walled matter names in the web app's matter list. Under the ethical-wall condition, which component fails first?

- A. The model, because a smaller tier cannot recognise privileged content and should be replaced by a larger one.
- B. The output renderer, because answers should be shown without citations to walled matters.
- C. The feedback loop, because ratings from walled lawyers are pooled with everyone else's.
- D. The retrieval service, because it searches all matters without the user's permissions, so walled content reaches the context before any app control applies.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Access control must apply where data enters the model's context. The index is queried without per-user filtering, so text from walled matters can be retrieved and used in an answer even though the matter list hides the names.

- **A** is wrong because no model tier enforces permissions. Access control is an architecture responsibility.
- **B** is wrong because hiding citations conceals the leak but the walled content has already shaped the answer.
- **C** is wrong because pooled ratings are a minor issue that comes later. Exposure happens at retrieval.

</details>

### Item 3

A bank wants to automate checks on new business-account applications. Every application goes through the same five checks in the same order: extract company details, verify registration, screen names against sanctions lists, compare declared ownership with registry data, and produce a findings summary. The compliance team requires that every run follows an identical, documented path that auditors can replay step by step. Which architectural pattern fits best?

- A. A workflow where code runs the five steps in order and calls Claude for the extraction, comparison and summary steps.
- B. An autonomous agent with tools for each check that decides the order and number of checks per application.
- C. A single augmented-LLM call that receives all documents and retrieval results and returns the full findings at once.
- D. A Skills-based agent that loads a skill for each check when it judges the check relevant.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** The steps are fixed and auditors need an identical, replayable path. A workflow puts control flow in code, so the sequence is guaranteed and each step can be logged and tested on its own, while Claude does the steps that need language understanding.

- **B** is wrong because letting the model choose order and count breaks the identical-path requirement.
- **C** is wrong because one combined call gives no step-level record for auditors to replay.
- **D** is wrong because the agent decides when a check is relevant, so a check could be skipped. That violates the governance constraint.

</details>

### Item 4

A consultancy runs a research system in which an orchestrator sends five subagents to investigate different market questions. Each subagent returns a prose summary, and the orchestrator writes the final report. Clients now require that every claim in the report can be traced to the subagent and source behind it. When any subagent fails, the orchestrator restarts the whole run, and the operations team cannot tell which subtask failed. Which two changes address the stated requirements? (Choose two.)

- A. Have each subagent return a structured result with its findings, the source for each finding, and a completion status.
- B. Merge the five subagents into one agent with a larger context window so that there is only one result to synthesise.
- C. Handle failures per subagent: retry or mark that subtask incomplete and log it, instead of restarting the full run.
- D. Move the orchestrator to a larger model tier so it can infer which source each claim came from.
- E. Let subagents message each other directly so they can fill gaps in each other's findings before reporting.

<details>
<summary>Answer and explanation</summary>

**Answer: A and C.** Structured results with sources and status give the orchestrator what it needs to keep a trace from claim to source (A). Per-subtask recovery makes failures visible and recoverable without throwing away successful work (C).

- **B** is wrong because merging removes the parallel split and does not by itself add source tracing.
- **D** is wrong because inferring sources after the fact is guesswork. Traceability has to be carried in the data.
- **E** is wrong because direct peer messaging makes control flow harder to observe, which works against the operations team's need.

</details>

### Item 5

A telecom's customer service programme is funded on one metric: cost per resolved contact. It must also keep first-response time under four seconds, which is a contractual SLA. Today every chat goes to the largest Claude tier, which resolves contacts well but costs far more than the budget allows. About 70% of chats are simple intents such as balance checks and plan questions. Which proposal should the architect back?

- A. Add a voice channel with real-time speech so customers can call instead of chat.
- B. Keep the largest tier for all chats and reduce cost by shortening every answer.
- C. Route simple intents to a smaller, faster tier and escalate complex chats to the larger tier, measuring resolution rate by route.
- D. Build an executive dashboard that shows sentiment trends across all chats.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The programme is funded on cost per resolved contact under a latency SLA. Routing the large share of simple intents to a cheaper, faster tier lowers cost and protects the SLA, and measuring resolution by route checks that quality holds.

- **A** is wrong because a new channel is a transformation feature that does not reduce cost per resolved contact.
- **B** is wrong because shorter answers barely touch the cost of using the largest tier for every chat and risk lower resolution.
- **D** is wrong because visibility does not change the funded metric.

</details>

### Item 6

A software company is building a support agent that runs multi-hour troubleshooting sessions and executes diagnostic scripts in a sandbox. Its legal team has confirmed there is no requirement to keep processing inside any particular cloud provider. The platform team is two people and has said clearly that it cannot host, scale or maintain session state and sandboxes for an agent runtime. Which choice best fits?

- A. Amazon Bedrock with a self-hosted Agent SDK service on the company's container platform.
- B. The Anthropic API with a Messages API loop the platform team writes and hosts.
- C. Headless Claude Code running on the company's own CI runners, triggered per support session.
- D. The Anthropic API with Claude Managed Agents, so Anthropic hosts the agent loop, sessions and sandbox.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** The binding constraint is operational ownership: the team cannot run a runtime, session state or sandboxes. With no residency constraint in play, the first-party API with a managed runtime moves that work off the team.

- **A** is wrong because a self-hosted SDK service puts hosting, sessions and sandboxing on the two-person team.
- **B** is wrong because a custom loop is the option with the most operational ownership.
- **C** is wrong because CI runners are not built for multi-hour interactive sessions, and the team would still own them.

</details>

### Item 7

A retailer has twelve product teams building separate Claude agents for marketing copy, product pages and customer emails. All of them must follow the same brand and legal review procedure, which legal updates every month. Today each team pastes its own copy of the procedure into its system prompt, and the copies have drifted apart. Legal wants one maintained version, and the agents should load it only when they produce customer-facing text. Which approach fits best?

- A. Ask each team to update its system prompt on the first day of each month from a shared document.
- B. Package the procedure as a shared, versioned Skill that each agent loads when it produces customer-facing text.
- C. Hard-code the review steps as a separate workflow in each team's codebase.
- D. Fine-tune a model on examples of approved copy so the procedure is learned implicitly.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** The constraints are one maintained source, many consumers, and loading only when relevant. A shared Skill gives legal a single versioned artifact and lets each agent pull it in when the task calls for it.

- **A** is wrong because manual copying is what caused the drift. It keeps twelve sources.
- **C** is wrong because twelve hard-coded copies recreate the same maintenance problem in code.
- **D** is wrong because a monthly-changing procedure cannot be maintained through training examples, and it removes the explicit, reviewable version legal wants.

</details>

### Item 8

A manufacturer has chosen to run the Claude Agent SDK on its own Kubernetes cluster. Security requires that agent tool calls run inside the company network, where the plant databases live. The model itself is reached through the company's chosen API surface. Which two responsibilities does this choice place on the manufacturer's team? (Choose two.)

- A. Obtaining a new data residency attestation, because self-hosting the SDK moves model inference into the cluster.
- B. Isolating and sandboxing the environment where the agent executes tools and code.
- C. Building a custom prompt caching layer, because caching is only available inside managed runtimes.
- D. Rewriting the tool-use protocol, because the SDK supports only hosted tools.
- E. Persisting and restoring agent session state across restarts and scaling events.

<details>
<summary>Answer and explanation</summary>

**Answer: B and E.** A self-hosted runtime means the team owns where tools execute and how that environment is contained (B), and it owns session state when pods restart or scale (E). These are the hosting, sandboxing and session-state trade-offs of self-hosting.

- **A** is wrong because the SDK still calls the model through the chosen API surface. Inference does not move into the cluster.
- **C** is wrong because prompt caching is requested through the model API, so a self-hosted runtime can use it without building its own layer.
- **D** is wrong because the SDK supports custom tools defined by the team. No protocol rewrite is needed.

</details>

### Item 9

A logistics firm processes delivery documents each night. An upload service accepts files, a single worker sends one synchronous Claude request per document and waits for each response before starting the next, results go to a managed database sized for twenty times current write volume, and a review screen lets staff correct errors. Tonight's volume is about 8,000 documents, which finish in the window. At month end, volume rises to about 60,000 documents that must finish before 6 a.m. Which component fails first?

- A. The processing worker, because one-at-a-time synchronous calls cannot finish month-end volume in the window and have no queue to absorb the backlog.
- B. The results database, because month-end write volume exceeds its capacity.
- C. The upload service, because 60,000 files cannot be accepted in one night.
- D. The review screen, because staff cannot correct that many errors before 6 a.m.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Volume rises by more than seven times while the worker runs strictly in sequence, so throughput, not storage, is the first limit reached. A queue with parallel workers, or batch processing for this non-interactive job, is the natural fix.

- **B** is wrong because the stem says the database is sized for twenty times current writes, which covers month end.
- **C** is wrong because nothing in the stem suggests upload capacity is limited.
- **D** is wrong because review happens after processing, and the stem's deadline applies to processing.

</details>

### Item 10

An engineering team uses an orchestrator to migrate a 200-file service to a new framework. The orchestrator splits the files alphabetically into ten groups and gives each group to a subagent. Many files share data types and interfaces defined across groups. The final step concatenates the subagents' changes into one pull request, which now fails to compile because different subagents changed the same shared types in different ways. Where does the decomposition fail, and what is the fix?

- A. Too few subagents. Split the work into forty smaller groups so each has less to change.
- B. Context per subagent. Give every subagent all 200 files so each one can see everything.
- C. Task boundaries. Split by module and dependency, and give one step ownership of the shared types before dependent modules are migrated.
- D. Sampling variation. Lower the temperature so subagents make the same choices for shared types.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The boundaries cut across shared dependencies, so several subagents edit the same contracts independently and concatenation cannot reconcile them. Splitting along dependency lines, with shared types migrated once first, removes the conflict at its source.

- **A** is wrong because more, smaller alphabetical groups cut across even more shared types.
- **B** is wrong because giving everyone everything removes the benefit of decomposition and still lets ten agents edit the same types.
- **D** is wrong because lower temperature does not coordinate independent design decisions across agents.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Exam overview: Architect, Professional](README.md) | Next: [Domain 2: Claude Models, Prompting, and Context Engineering](02-models-prompting-and-context-engineering.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
