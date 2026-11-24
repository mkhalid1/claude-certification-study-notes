<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 2: Tool Design & MCP Integration](02-tool-design-and-mcp-integration.md) · Next: [Domain 4: Prompt Engineering & Structured Output](04-prompt-engineering-and-structured-output.md)<!-- /nav:top -->

# Domain 3: Claude Code Configuration & Workflows

Weight: 20% of scored items, about 12 items.

## What this domain measures

This domain checks whether you can set Claude Code up for a team, not only for yourself, and run it well from the terminal to CI.

- **CLAUDE.md hierarchy and organization.** There are three levels: user (`~/.claude/CLAUDE.md`), project (`.claude/CLAUDE.md` or a root `CLAUDE.md`) and directory (a `CLAUDE.md` in a subdirectory). User-level instructions apply only to that user and are not shared through version control, which is a common reason a new teammate "doesn't get" the team's rules. Keep files modular with `@import` (each package pulls in only the standards files that apply to it) and with topic files in `.claude/rules/` instead of one huge CLAUDE.md. Use `/memory` to see which memory files are loaded when behaviour differs between sessions or people.
- **Slash commands and skills.** Project commands live in `.claude/commands/` and are shared through version control. Personal commands live in `~/.claude/commands/`. Skills live in `.claude/skills/` with a `SKILL.md` whose frontmatter can set `context: fork` (run in an isolated sub-agent context so verbose or exploratory output does not pollute the main conversation), `allowed-tools` (restrict what the skill may use) and `argument-hint` (prompt for a required parameter when it is missing). Personal variants of a team skill go in `~/.claude/skills/` under a different name. Skills are for on-demand, task-specific workflows. CLAUDE.md is for standards that should always be loaded.
- **Path-specific rules.** Files in `.claude/rules/` can carry YAML frontmatter with a `paths` field of glob patterns, so the rule loads only when Claude edits matching files. That saves context and tokens, and it beats subdirectory CLAUDE.md files when a convention applies to files spread across many directories (test files, Terraform files).
- **Plan mode vs direct execution.** Plan mode is for large changes, multiple valid approaches, architectural decisions and multi-file work: explore and design before changing anything. Direct execution is for small, clear changes such as a one-function fix with a clear stack trace. A common pattern combines them: plan the migration, then execute the plan. The Explore subagent isolates verbose discovery and returns a summary, protecting the main context.
- **Iterative refinement.** When prose instructions are read inconsistently, give 2 to 3 concrete input and output examples. Write tests first and iterate by sharing failures. Use the interview pattern (have Claude ask you questions) before building in an unfamiliar domain. Send interacting issues together in one detailed message, and fix independent issues one at a time.
- **CI/CD.** Run Claude Code non-interactively with `-p` (or `--print`) so jobs do not hang. Use `--output-format json` with `--json-schema` for machine-parseable findings, for example to post inline PR comments. CLAUDE.md gives CI runs the project context (testing standards, fixtures, review criteria). On re-runs, include earlier findings and ask only for new or still-open issues. Give test generation the existing tests so it does not duplicate them. A separate, independent Claude instance reviews code better than the session that wrote it.

Why it matters: a team's Claude Code setup is shared infrastructure. Put a rule at the wrong level and half the team works without it. Leave a skill unscoped and a routine command can clutter or endanger a session.

## How questions look

Stems describe a team situation: a convention that one developer gets and another does not, a CLAUDE.md that has grown unmanageable, a skill whose output floods the conversation, a task where you must choose plan mode or direct execution, a CI job whose output cannot be parsed or keeps repeating old comments. Options typically name specific locations and settings (`.claude/commands/` vs `~/.claude/commands/`, a rules file vs a subdirectory CLAUDE.md, `context: fork` vs `allowed-tools`). Some distractors name settings or files that do not exist. Multiple-response items often pair a fix with a verification step, or two frontmatter keys that solve two stated problems.

## A method for these questions

1. Ask who should get the behaviour: one person (user level), the whole team (project level, in version control), or only certain files (path-scoped rule or directory file).
2. Ask when it should load: always (CLAUDE.md), only for matching files (`paths` rule), or only when invoked (skill or command).
3. For skills, map each stated problem to one frontmatter key: noisy output to `context: fork`, risky tools to `allowed-tools`, missing parameters to `argument-hint`.
4. For plan mode questions, look for signals: many files, several valid approaches, architecture. Any of these points to planning first. A clear, local fix points to direct execution.
5. For CI questions, check three things: non-interactive (`-p`), structured output (`--output-format json` with `--json-schema`), and the right context (CLAUDE.md, prior findings, existing tests).
6. Distrust any option that invents a config file or flag you have never seen in the guide.

## Traps that cost points

- **Putting team rules in `~/.claude/CLAUDE.md`.** That file is only loaded on the author's machine, so teammates never see the rules. Team standards belong in the project CLAUDE.md in version control.
- **Using a subdirectory CLAUDE.md for conventions that follow file type.** Test files or Terraform files spread across many folders are better served by one `paths` glob rule than by a copy in every directory.
- **Treating skills as always-on.** Skills load when invoked. Universal standards that must always apply belong in CLAUDE.md.
- **Using `context: fork` to restrict tools.** Fork runs the skill in an isolated context; limiting which tools a skill can use is done with `allowed-tools`.
- **Editing the team's skill for personal taste.** That changes it for everyone. Create a personal variant in `~/.claude/skills/` with a different name.
- **Planning a one-line fix, or diving into a migration.** Plan mode adds overhead to a trivial change, while jumping straight into an architectural change usually leads to rework.
- **Letting the generating session review its own code.** It keeps its earlier reasoning and is less likely to question it. Use an independent instance.
- **Re-running a PR review with no memory of the last one.** Developers get duplicate comments. Pass prior findings and ask for new or unresolved issues only.

## Timing

Guidance only: the exam gives 120 minutes for 60 items, so about 2 minutes per item. Many items here hinge on a single location or flag. If you know it, answer in under a minute. If two locations both seem plausible, re-read the stem for who must receive the behaviour and when it must load, and decide on that basis rather than rereading every option.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| User-level CLAUDE.md | `~/.claude/CLAUDE.md`. Applies to one user. Not shared through version control. |
| Project-level CLAUDE.md | `.claude/CLAUDE.md` or root `CLAUDE.md`. Shared with the team. |
| Directory-level CLAUDE.md | A `CLAUDE.md` in a subdirectory, for that part of the tree. |
| `@import` | Pull specific standards files into a CLAUDE.md to keep it modular. |
| `.claude/rules/` | Topic-specific rule files instead of a monolithic CLAUDE.md. |
| `paths` frontmatter | Glob patterns such as `["terraform/**/*"]` or `**/*.test.tsx`. Rule loads only for matching files. |
| `/memory` | Shows which memory files are loaded. Use it to diagnose inconsistent behaviour. |
| `.claude/commands/` | Project slash commands, shared through version control. `~/.claude/commands/` is personal. |
| `.claude/skills/` with `SKILL.md` | Skills with frontmatter. Personal variants in `~/.claude/skills/` under a different name. |
| `context: fork` | Runs a skill in an isolated sub-agent context so output does not pollute the main session. |
| `allowed-tools` | Restricts which tools a skill may use. |
| `argument-hint` | Prompts for a required parameter when the skill is invoked without one. |
| Plan mode | Large, multi-file, architectural or multi-approach work. Explore and design before changing. |
| Direct execution | Small, well-scoped changes with clear cause and scope. |
| Explore subagent | Isolates verbose discovery and returns a summary. |
| Iterative refinement | 2 to 3 input and output examples, tests first, interview pattern, interacting issues together. |
| `-p` / `--print` | Non-interactive mode for CI. |
| `--output-format json` + `--json-schema` | Structured, machine-parseable CI output. |
| Independent reviewer | A separate instance without the generator's context catches more. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

At a fintech company, a senior engineer wrote the team's coding and logging conventions in her `~/.claude/CLAUDE.md` months ago. A new hire has cloned the repository, but his Claude Code sessions ignore the logging conventions entirely, while hers follow them. The team wants everyone to get the same conventions and wants to confirm the fix. Which two steps should they take? (Choose two.)

A. Move the conventions into the project-level CLAUDE.md and commit it to the repository.
B. Have the new hire copy the senior engineer's `~/.claude/CLAUDE.md` onto his machine.
C. Run `/memory` in the new hire's session to check which memory files are loaded.
D. Add the conventions to the project's `.mcp.json` so they load with the MCP servers.
E. Turn the conventions into a skill that developers invoke at the start of each session.

<details>
<summary>Answer and explanation</summary>

**Answer: A and C.** User-level CLAUDE.md applies only to its owner and is not shared through version control, which explains the difference. Moving the conventions to the project CLAUDE.md shares them with everyone, and `/memory` shows which memory files a session has loaded, confirming the fix.

- **B** is wrong because copying files by hand is not version controlled, drifts over time, and has to be repeated for every new hire.
- **D** is wrong because `.mcp.json` configures MCP servers, not instructions or conventions.
- **E** is wrong because skills load on demand. Conventions that must always apply belong in CLAUDE.md.

</details>

### Item 2

A monorepo has twelve packages. The payments packages must follow the company's PCI coding standards, the web packages its accessibility standards, and every package its general style guide. Each standard already lives in its own file under `docs/standards/`. The package maintainers know which standards apply to their package. What is the most maintainable setup?

A. Paste all three standards into the root CLAUDE.md so every package sees everything.
B. Create a skill for each standard and ask developers to invoke the right one before editing.
C. In each package's CLAUDE.md, use `@import` to include only the standards files that apply to that package.
D. Ask each developer to keep the relevant standards in their user-level CLAUDE.md.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** `@import` keeps CLAUDE.md files modular. Each package's maintainer imports exactly the standards that apply, and the standards files stay single-sourced.

- **A** is wrong because it loads irrelevant standards into every session and grows a monolithic file that is hard to maintain.
- **B** is wrong because standards that must always apply should not depend on someone remembering to invoke a skill.
- **D** is wrong because user-level files are not shared and would drift between developers.

</details>

### Item 3

A data engineer asks Claude Code to write a function that converts free-form log lines into structured events. She has rewritten her prose description four times, and each version produces a different interpretation of which fields to extract and how to handle missing timestamps. What is the most effective next step?

A. Write a longer, more formal specification of the rules in prose.
B. Provide 2 to 3 concrete examples of input log lines with the exact structured output expected for each, including one with a missing timestamp.
C. Ask Claude to be more careful and consistent.
D. Switch to plan mode and ask Claude to design the parser architecture.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** When prose descriptions are interpreted inconsistently, concrete input and output examples communicate the transformation most reliably. Including the missing-timestamp case pins down the edge case directly.

- **A** is wrong because more prose is the approach that has already failed four times.
- **C** is wrong because a general request to be careful gives Claude no new information about the expected output.
- **D** is wrong because this is a small, well-scoped function. The problem is an unclear specification, not architecture.

</details>

### Item 4

An infrastructure team keeps Terraform files in `infra/`, in `services/*/deploy/`, and in a few legacy folders. They want Claude Code to apply their Terraform conventions (naming, tagging, module layout) whenever it edits a Terraform file, and they do not want those conventions taking up context when developers work on application code. What should they do?

A. Add the Terraform conventions to the root CLAUDE.md under a "Terraform" heading.
B. Put a CLAUDE.md with the conventions in every directory that currently has Terraform files.
C. Create a Terraform skill and ask developers to invoke it before infrastructure work.
D. Create a file in `.claude/rules/` with YAML frontmatter `paths` set to a glob such as `["**/*.tf"]`, containing the conventions.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A path-scoped rule loads only when Claude edits files that match its globs, wherever they sit in the tree. That applies the conventions to every Terraform file and keeps them out of context otherwise.

- **A** is wrong because root CLAUDE.md content is always loaded, so the conventions consume context during unrelated work.
- **B** is wrong because directory files are tied to directories. They must be copied everywhere and will miss new Terraform locations.
- **C** is wrong because it depends on developers remembering to invoke it, so the conventions are not applied automatically.

</details>

### Item 5

A team built a `/map-codebase` skill that walks a repository and produces a long report on modules, dependencies and hotspots. Developers like the report but complain that after running it, their main session is cluttered and later answers get worse as context fills up. They want the skill to keep working the same way and return only its conclusions. Which frontmatter change addresses this?

A. Add `argument-hint` so developers specify which folder to map.
B. Add `context: fork` so the skill runs in an isolated sub-agent context.
C. Add `allowed-tools` limited to Read and Grep.
D. Move the skill from `.claude/skills/` to `.claude/commands/`.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** `context: fork` runs the skill in an isolated sub-agent context, so its verbose exploration stays out of the main conversation. This is the stated use case for that option.

- **A** is wrong because prompting for a folder does not stop the output from entering the main session.
- **C** is wrong because restricting tools controls what the skill can do, not where its output goes.
- **D** is wrong because moving it to a command changes how it is defined, not whether its output pollutes the main context.

</details>

### Item 6

A team's `/release-notes` skill drafts a release notes file from recent commits. Two problems have come up: once it ran a Bash command that deleted a local branch, and developers often invoke it without saying which version they are releasing, so it guesses. Which two frontmatter settings address these problems? (Choose two.)

A. `allowed-tools` restricted to the read and file-write tools the skill needs, with no Bash.
B. `argument-hint` that prompts for the release version.
C. `context: fork` so the skill runs in an isolated context.
D. A line in the skill body saying "never run destructive commands".
E. Moving the skill to `~/.claude/skills/` so only one person can run it.

<details>
<summary>Answer and explanation</summary>

**Answer: A and B.** `allowed-tools` restricts which tools the skill can use, which removes the destructive option rather than discouraging it. `argument-hint` prompts the developer for the required parameter when they invoke the skill without it.

- **C** is wrong because isolating context does not limit tools or supply the missing version.
- **D** is wrong because an instruction is guidance. A tool restriction is a guarantee.
- **E** is wrong because it removes a useful team skill from everyone and fixes neither problem.

</details>

### Item 7

A team shares a `/pre-merge-check` skill in `.claude/skills/`. One developer wants a version that also runs her extra performance checks and uses a terser output style. Her teammates are happy with the current skill and do not want it changed. What should she do?

A. Edit the shared skill and add a flag for her extra checks.
B. Add her preferences to the project CLAUDE.md so the shared skill picks them up.
C. Create a personal variant in `~/.claude/skills/` with a different name.
D. Copy the shared skill to a new name inside the project's `.claude/skills/` and commit it for her own use.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Personal customization belongs in `~/.claude/skills/`, and giving the variant a different name keeps the team's skill untouched and avoids any confusion about which one runs.

- **A** is wrong because editing the shared skill changes it for the whole team.
- **B** is wrong because the project CLAUDE.md is shared, so her preferences would affect everyone.
- **D** is wrong because anything in the project's `.claude/skills/` is shared through the repository, so her personal variant would appear for every teammate.

</details>

### Item 8

A CI job uses Claude Code to generate unit tests for changed files. Reviewers reject most of the output: many tests duplicate scenarios that already exist, and others assert trivial things instead of the business rules the team cares about. The repository has good shared fixtures that the generated tests never use. What is the most effective improvement?

A. Ask for fewer tests per run so reviewers have less to reject.
B. Run test generation through the Message Batches API overnight.
C. Have a second CI step delete any generated test that fails.
D. Provide the existing test files in context, and document testing standards, what makes a test valuable, and the available fixtures in CLAUDE.md.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** Existing tests in context let Claude avoid duplicating covered scenarios. CLAUDE.md is how CI-invoked Claude Code gets project context, so documenting standards, valuable test criteria and fixtures there raises quality and cuts low-value output.

- **A** is wrong because fewer tests of the same quality still duplicate and still miss the business rules.
- **B** is wrong because changing when the job runs does nothing for what it generates.
- **C** is wrong because duplicates and trivial tests usually pass. Deleting failures does not address either problem.

</details>

### Item 9

A developer must replace the HTTP client library used across about 50 files in a service. There are two reasonable approaches: wrap the new library in an adapter that mimics the old interface, or replace call sites directly. They differ in how much code changes and in how retries are handled. What is the best way to use Claude Code for this?

A. Use direct execution and let Claude start replacing call sites file by file, deciding the approach as it goes.
B. Use direct execution with detailed upfront instructions that pick the adapter approach.
C. Break the work into 50 separate direct-execution tasks, one per file, with no overall plan.
D. Use plan mode to explore the call sites and compare the two approaches, then switch to direct execution to implement the chosen plan.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A library migration across many files with two valid approaches is a plan mode task. Planning first surfaces how the library is actually used before anything changes, and direct execution then carries out the agreed approach efficiently.

- **A** is wrong because deciding the approach mid-change risks inconsistent call sites and costly rework.
- **B** is wrong because it commits to an approach before exploring how the library is used, which is the decision plan mode exists to inform.
- **C** is wrong because per-file tasks without a plan produce inconsistent changes and ignore the cross-file design decision.

</details>

### Item 10

A CI step runs Claude Code with `-p` to review each pull request and post findings as inline comments. Two problems remain: the posting script often fails because Claude's review comes back as free-form prose, and when developers push fixes, the re-run posts the same comments again on issues already raised. Which two changes address these problems? (Choose two.)

A. Run with `--output-format json` and a `--json-schema` that defines each finding's file, line, issue and severity.
B. Parse the prose review with regular expressions to find file names and line numbers.
C. Run the review in the same Claude session that generated the code, so it remembers earlier comments.
D. Delete all earlier bot comments before each run and post the full set again.
E. Include the prior review's findings in context on re-runs and instruct Claude to report only new or still-unaddressed issues.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** `--output-format json` with `--json-schema` produces structured findings that a script can post reliably as inline comments. Passing prior findings and asking for only new or unresolved issues stops duplicate comments on re-runs.

- **B** is wrong because regex over prose is brittle when a supported structured output option exists.
- **C** is wrong because the generating session is a worse reviewer of its own code, and reusing it does not by itself prevent duplicates.
- **D** is wrong because it still re-posts every open finding, wiping comment threads and any discussion on them.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Architect, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-architect?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-architect), which reports percent correct by domain.

---

---

---

<!-- nav:bottom -->← Previous: [Domain 2: Tool Design & MCP Integration](02-tool-design-and-mcp-integration.md) | Next: [Domain 4: Prompt Engineering & Structured Output](04-prompt-engineering-and-structured-output.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
