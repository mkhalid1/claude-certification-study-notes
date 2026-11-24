<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 6: Stakeholder Communication and Lifecycle Management](06-stakeholder-communication-and-lifecycle-management.md) · Next: [How scoring works](../scoring.md)<!-- /nav:top -->

# Domain 7: Developer Productivity and Operational Enablement

Weight: 8% of scored items, about 5 items.

## What this domain measures

This domain tests whether you can take Claude Code and the Claude Agent SDK from one team's success to safe, organization-wide use. The guide lists three skills:

- **Packaging and distribution.** Decide how to package a Claude Code workflow proven on one team for the whole organization: what goes in the plugin (skills, hooks, subagents, commands, MCP configuration), how it is distributed through a marketplace, what managed settings enforce, and which controls are central versus left to teams, so the workflow stays safe when it runs unattended.
- **Agent SDK and Claude Code configuration.** Configure permission modes (default, acceptEdits, plan, auto, dontAsk, bypassPermissions), hooks, subagent definitions, and headless or CI operation so a team workflow stays within its safety and automation guardrails.
- **Accountability for AI-generated code.** Review how a team handles review and ownership of AI-assisted code, and identify the control that keeps a human accountable.

In practice, this is where adoption succeeds or stalls. Teams want speed, security wants guarantees, and the architect has to give both.

## How questions look

Stems describe a team's workflow, a rollout goal, or an incident in an automated pipeline. Some list the parts of a workflow (a skill, a hook, a subagent, an MCP server) and ask how to distribute them. Others describe what an unattended job must and must not do, and ask which permission mode and settings fit. Accountability items describe a review process that has gone wrong and ask which control restores human ownership.

Wrong options usually rely on instructions where configuration can enforce, give automation more permission than the task needs, or treat labeling and extra tests as a substitute for a named human owner.

## A method for these questions

1. List what the workflow must be able to do and what it must never do. Note whether a person is present when it runs.
2. If no person is present, rule out any option that depends on answering permission prompts.
3. Match the permission mode to the phase: planning without changes, editing with automatic acceptance, or running only pre-approved actions.
4. For anything that must always happen or never happen, prefer a hook or a permission rule over an instruction in CLAUDE.md or a prompt.
5. For rollout items, separate what must be identical and enforced everywhere (security rules, blocked actions) from what teams should own (their own skills and commands). Central controls go in managed settings. Shared workflow parts go in a plugin distributed through a marketplace.
6. For accountability items, look for a named human who reviews and owns each change before it merges.

## Traps that cost points

- **Using bypassPermissions to make automation work.** It removes every check. Unattended jobs need a narrow allow list, not no list.
- **Running default mode in CI.** Default mode asks for approval, and nobody is there to answer.
- **Using plan mode for a job that must edit.** Plan mode is for analysis and proposals before changes are made.
- **Putting must-always rules in CLAUDE.md.** Instructions guide the model but are not guaranteed. Hooks and permission rules enforce.
- **Shipping security controls in a plugin that teams can switch off.** If a control must hold for everyone, enforce it through managed settings.
- **Copying workflow files between repositories.** Copies drift, and fixes never reach everyone. Distribute one versioned plugin.
- **Labeling AI-generated code instead of assigning an owner.** A label says where code came from. It does not make anyone accountable for it.
- **Letting an AI reviewer replace human approval.** A second agent can help reviewers, but a person must own the decision to merge.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. These items are usually short and configuration-focused, so aim for about a minute each. The fastest route is to ask two questions: is anyone present when this runs, and what must be enforced rather than requested? Those two answers eliminate most options.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Plugin | Bundles skills, slash commands, subagents, hooks and MCP configuration into one installable, versioned unit. |
| Marketplace | Where plugins are published and installed from. One source, so updates reach every team. |
| Managed settings | Organization-level settings that users and projects cannot override. Use them for controls that must hold everywhere. |
| Central versus team | Enforce security rules and blocked actions centrally. Leave team-specific skills and commands to teams. |
| default mode | Asks for permission when a tool is first needed. Needs a person present. |
| acceptEdits mode | Accepts file edits automatically, while other actions, such as running scripts, still follow permission rules. |
| plan mode | Analyze and propose a plan without making changes. |
| dontAsk mode | Runs only what is pre-approved by permission rules and declines the rest instead of asking. |
| bypassPermissions mode | Skips permission checks entirely. Only for fully isolated, disposable environments. |
| Hooks | Run at defined points such as before or after a tool call. Can block an action or run a step every time. |
| Subagent definition | Gives a subagent its own prompt and its own limited tool list. |
| Headless operation | Runs Claude Code without an interactive session, for example in CI. Needs permissions that work with nobody present. |
| Human accountability | A named person reviews, understands and owns each AI-generated change before it merges. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A platform team has built a Claude Code workflow for reviewing database changes. It includes a skill describing the company's migration standards, a slash command that starts a review, a review subagent with a limited tool list, a hook that blocks direct edits to applied migration files, and MCP configuration for the issue tracker. Thirty other teams want to adopt it, and the platform team will keep improving it. Which distribution approach fits best?

- A. Publish a wiki page with each file's contents so teams can copy what they need, with a changelog for each update.
- B. Email a zip archive of the files to each team lead after every update.
- C. Paste the standards and instructions into every repository's CLAUDE.md file.
- D. Package the components as a plugin and publish it through the organization's internal marketplace.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A plugin bundles the skill, command, subagent, hook and MCP configuration into one versioned unit, and a marketplace gives every team one place to install and update it. Improvements then reach all thirty teams.

- **A** is wrong because copied files drift and updates do not propagate.
- **B** is wrong because manual archives repeat the drift problem with extra effort.
- **C** is wrong because CLAUDE.md text cannot carry a hook, a subagent definition or MCP configuration, and instructions are not enforcement.

</details>

### Item 2

A company is rolling out Claude Code to every engineering team. Security requires two things for all users, which no team may switch off: Claude Code must never read files matching the company's secret file patterns, and bypassPermissions mode must not be available. Teams want to keep adding their own skills and commands. Where should the security requirements be enforced?

- A. In a security plugin published through the internal marketplace, which every team is asked to install and keep enabled at all times.
- B. In each repository's project settings, added by each team during onboarding.
- C. In managed settings deployed by IT, with deny rules for the secret patterns and bypass mode disabled, while teams remain free to add their own skills and commands.
- D. In the onboarding guide, with a reminder in each team's CLAUDE.md file.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Requirements that must hold for everyone and cannot be switched off belong in managed settings, which users and projects cannot override. Leaving skills and commands to teams keeps their flexibility.

- **A** is wrong because a plugin that teams can disable does not guarantee the control.
- **B** is wrong because project settings depend on every team doing it and can be changed locally.
- **D** is wrong because documentation and instructions are not enforcement.

</details>

### Item 3

A team runs headless Claude Code in CI to fix lint errors on pull requests. No person watches the job. It must be able to edit files and run the project's lint and test commands, and every other action must be declined immediately rather than waiting for an approval that will never come. Which configuration fits?

- A. dontAsk mode with permission rules that pre-approve file edits and the specific lint and test commands.
- B. bypassPermissions mode, so the job is never blocked waiting for an approval.
- C. default mode, so each new action is approved before it runs.
- D. plan mode, so the job proposes fixes without risk.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** dontAsk runs only what the permission rules pre-approve and declines everything else instead of prompting. That matches an unattended job that needs a narrow, fixed set of actions.

- **B** is wrong because bypassing all checks allows every other action too, which breaks the requirement.
- **C** is wrong because default mode waits for approvals that nobody is present to give.
- **D** is wrong because plan mode does not make the edits the job exists to make.

</details>

### Item 4

A developer is using Claude Code on a risky refactor of a payments module. The team's rule is that Claude must first study the code and propose a plan, with no changes made, for the developer to review. After the plan is approved, file edits may be accepted automatically, but running test suites or deploy scripts must still ask for permission. Which two permission modes match the two phases? (Choose two.)

- A. bypassPermissions for both phases, so the developer is not interrupted.
- B. plan mode for the first phase.
- C. default mode for the second phase, approving each edit by hand.
- D. acceptEdits mode for the second phase.
- E. bypassPermissions for the second phase only.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** plan mode supports analysis and a proposed plan without changes (B). acceptEdits accepts file edits automatically while other actions, such as running scripts, still follow the normal permission flow (D).

- **A** is wrong because it removes the no-changes rule for phase one and the prompts for scripts in phase two.
- **C** is wrong because the team wants edits accepted automatically in phase two.
- **E** is wrong because test and deploy scripts must still ask for permission, and bypass removes that.

</details>

### Item 5

An engineering group requires two things of every Claude Code session. Every file Claude writes must be run through the code formatter, and no file may ever be written if it contains something that matches the company's credential patterns. Both rules are in CLAUDE.md, but reviews show Claude sometimes skips formatting, and once a test key was written to a config file. Which change enforces both rules?

- A. Rewrite the CLAUDE.md rules in stronger language and move them to the top of the file.
- B. Add a pre-tool-use hook that checks proposed writes for credential patterns and blocks matches, and a post-tool-use hook that runs the formatter after every write.
- C. Ask code reviewers to check formatting and look for credentials in every pull request.
- D. Create a formatting and secrets-check subagent that the main agent can call whenever it judges a file needs formatting or review, guided by a stricter system prompt.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** Hooks run every time at fixed points, so they enforce rules instead of requesting them. A check before the write can block credentials, and a step after the write guarantees formatting.

- **A** is wrong because instructions already failed and stay advisory however they are worded.
- **C** is wrong because review catches problems after the file is written, and the credential has already leaked into the branch.
- **D** is wrong because the main agent still decides whether to call the subagent, so formatting remains optional.

</details>

### Item 6

A team wants a security-review subagent inside its Claude Code workflow. The subagent should read and search code and run the company's static analysis command, and it must never edit files or run any other command. The main agent keeps its normal editing tools. Which configuration fits best?

- A. Define the subagent with a tool list limited to reading, searching and the static analysis command.
- B. Give the subagent all tools and state in its prompt that it must never edit files.
- C. Skip the subagent and run the review inside the main agent's session to save setup time.
- D. Give the subagent all tools and add a hook that logs any edits it makes for later review, so any unwanted change can be reverted.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** A subagent definition can restrict which tools the subagent has. Limiting it to reading, searching and the analyser makes editing impossible for that subagent, while the main agent keeps its own tools.

- **B** is wrong because a prompt instruction does not remove the editing tools.
- **C** is wrong because the main agent has editing tools, so the review would not be restricted.
- **D** is wrong because logging edits after they happen does not prevent them.

</details>

### Item 7

A product team lets an unattended agent open pull requests for small bug fixes, and branch rules merge them automatically when CI passes. After an incident caused by one of these merges, nobody on the team could explain what the change did or why it had been written that way. Leadership wants to keep the agent but restore human ownership of the code. Which control does that?

- A. Add more automated tests to the CI pipeline.
- B. Label every agent-created pull request as AI-generated.
- C. Require approval from a named human reviewer, who becomes accountable for the change, before any agent-created pull request can merge.
- D. Add a second agent that reviews the first agent's pull requests in detail and blocks any merge it cannot explain, using a larger model than the first agent.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** The failure is that no person owned the change. Requiring a named human to approve and take accountability before merge restores ownership while keeping the agent's productivity.

- **A** is wrong because tests catch some defects but do not make anyone understand or own the code.
- **B** is wrong because a label records origin but assigns no responsibility.
- **D** is wrong because another agent's review still leaves no human accountable.

</details>

### Item 8

An engineering organization has adopted Claude Code widely. Engineers now open pull requests of several thousand lines generated in one session, and reviewers admit they approve them after skimming. The CTO wants a review process in which a human clearly owns every AI-assisted change. Which two controls best achieve that? (Choose two.)

- A. Make the engineer who directs the change its accountable author, and limit change size so each diff can be reviewed in full.
- B. Credit Claude as the commit author so it is clear who wrote the code.
- C. Measure each engineer's productivity by the number of lines generated per week.
- D. Allow the AI to approve its own pull request when all tests pass.
- E. Require human code review on every pull request through branch protection, with code owners for sensitive paths.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** A named author who must be able to explain a reviewable change keeps ownership with a person (A). Mandatory human review, with code owners for sensitive areas, makes sure a second accountable person checks it (E).

- **B** is wrong because crediting the tool as author moves attention away from the human who is accountable.
- **C** is wrong because rewarding volume encourages larger, less reviewable changes.
- **D** is wrong because self-approval removes human review entirely.

</details>

### Item 9

A platform team wants a nightly job that runs Claude Code headless across 40 repositories to update dependencies, run tests and open pull requests. Nobody watches it run. Security requires that the job cannot push to protected branches, cannot run arbitrary commands, and cannot merge its own changes. Which configuration keeps the job safe while it runs unattended?

- A. bypassPermissions mode with an organization admin token, so the job is never blocked, plus a nightly report to security of every command it ran.
- B. default mode with the platform lead's credentials, so the lead can approve anything unusual.
- C. The developer's personal access token with full repository rights, to avoid setting up a new identity.
- D. Headless operation with an explicit allow list for the package manager and test commands, a token that can open pull requests but not push to protected branches or merge, and human review before merge.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Each security requirement maps to a control: the allow list blocks arbitrary commands, the token scope blocks protected-branch pushes and merges, and human review keeps a person in charge of what lands.

- **A** is wrong because bypassing checks with an admin token violates every requirement.
- **B** is wrong because default mode waits for approvals that nobody gives at night, and borrowing a person's credentials blurs accountability.
- **C** is wrong because full repository rights allow the pushes and merges security forbids.

</details>

### Item 10

The database-review plugin from one team is now used by twenty teams. A flaw is found in its hook that blocks edits to applied migrations, and the fix must reach every team quickly. Some teams have also been copying the plugin's files into their repositories and changing them locally. The platform team wants every team on the same, current version. Which approach fits best?

- A. Ask each team to fork the plugin into its own repository, apply the fix, and confirm in the engineering channel when it is done.
- B. Publish the fixed version through the internal marketplace and use managed settings to point every machine at that marketplace and enable the plugin centrally.
- C. Post a message in the engineering channel asking teams to update when they can.
- D. Copy the fixed hook into every repository by hand.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** A single versioned plugin in the marketplace, enabled through managed settings, puts every team on the same current version and stops the drift that local copies cause.

- **A** is wrong because forks multiply versions and depend on each team acting.
- **C** is wrong because a request leaves the flawed hook running wherever teams do not act.
- **D** is wrong because manual copies drift again and are hard to keep current.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Architect, Professional diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect-professional?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect-professional) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->← Previous: [Domain 6: Stakeholder Communication and Lifecycle Management](06-stakeholder-communication-and-lifecycle-management.md) | Next: [How scoring works](../scoring.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
