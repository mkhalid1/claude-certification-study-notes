<!-- nav:top -->[Contents](../../README.md#contents) · Previous: [Domain 2: Applications and Integration](02-applications-and-integration.md) · Next: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md)<!-- /nav:top -->

# Domain 3: Claude Code

Weight: 8% of scored items, about 5 items.

## What this domain measures

This domain checks that you can run Claude Code properly and extend it for a team. The guide groups it into two skills:

- **Claude Code operation.** Using Claude Code correctly across the CLI, desktop, IDE, web, and mobile surfaces. That covers session management, the CLAUDE.md hierarchy and which file wins, settings.json, permission modes, headless and streaming modes for automation, and the built-in commands.
- **Customization and extension.** Looking at a repeatable team workflow and choosing the mechanism that fits it: a Skill, a plugin, a Rules file, a hook, a subagent, or a slash command. Then writing that extension and packaging it so other people can install it.

Why it matters: on a team, Claude Code's behaviour comes from layered files and settings. If you cannot predict which layer wins, you cannot explain why a teammate's session behaves differently from yours, and you cannot roll out a shared workflow safely.

## How questions look

Stems usually describe a team situation: a repo layout with several CLAUDE.md and settings files, a CI job that needs to run Claude Code unattended, or a workflow people repeat by hand. You are asked what the effective behaviour will be, or which mechanism to use. Options are often all real Claude Code features. The skill is matching the feature to the need: always-on context, on-demand know-how, deterministic automation, isolated context, or distribution.

## A method for these questions

1. Decide what kind of need it is. Always-applies guidance, guidance for some files only, a procedure used sometimes, something that must happen every time, bulky work to keep out of the main context, or sharing across repos.
2. Map it. Always-on guidance goes in CLAUDE.md. Guidance scoped to file paths goes in a Rules file. An on-demand procedure with resources becomes a Skill. "Every time" becomes a hook. Isolated work goes to a subagent. A prompt you trigger by name becomes a slash command. Sharing becomes a plugin.
3. For configuration items, list every layer in play and apply precedence: managed settings first, then command-line arguments, then local project, then shared project, then user. Remember that a deny rule beats an allow rule.
4. For automation items, look for headless mode (`claude -p`), a machine-readable output format, and pre-approved tools, so nothing waits for a human.
5. Prefer the option that keeps personal settings out of shared files and shared settings under version control.

## Traps that cost points

- **Thinking a local allow overrides a managed deny.** Managed settings sit at the top, and deny rules are checked before allow rules. A developer cannot unlock what the organisation has blocked.
- **Putting everything in the root CLAUDE.md.** It loads into every session. Path-specific rules belong in Rules files, and long procedures belong in Skills that load only when relevant.
- **Using a prompt instruction where a hook is needed.** "Always run the formatter after edits" is a hook, because a hook runs every time, whatever the model decides.
- **Committing personal preferences.** Personal settings go in your user files or in local project files that are not checked in, not in the shared project files.
- **Running CI interactively.** A pipeline needs headless mode with JSON or stream-JSON output, and the tools it needs approved in advance.
- **Copying extensions by hand between repos.** Copies drift. Bundle them as a plugin and distribute it through a marketplace.
- **Reading `--allowedTools` as a restriction.** It pre-approves the listed tools so they run without a prompt. To block tools, use deny rules or `--disallowedTools`.

## Timing

Guidance only: the exam gives 120 minutes for 70 items, about 1.7 minutes per item. Domain 3 is about 5 items, so budget roughly 8 to 9 minutes. Most of these items are fast if you know the precedence order and the extension map. If a config item lists several files, write the layers down in order on your scratch paper before you read the options.

## Key ideas to know cold

| Idea | What to remember |
|---|---|
| Settings precedence | Managed settings, then command-line arguments, then `.claude/settings.local.json`, then `.claude/settings.json`, then `~/.claude/settings.json`. |
| Permission rules | Deny is checked before ask and allow. A deny at any level wins. |
| CLAUDE.md locations | Managed policy file, user `~/.claude/CLAUDE.md`, project `./CLAUDE.md` or `./.claude/CLAUDE.md`, and personal `CLAUDE.local.md` (not committed). Files in parent directories load at start. Files in subdirectories load when Claude works there. |
| Rules files | Markdown files in `.claude/rules/`, which can be scoped to file paths with a `paths` field in their frontmatter. |
| Permission modes | Include `default`, `acceptEdits`, `plan` (read and propose, no edits), `auto`, `dontAsk`, and `bypassPermissions`. Pick the least permissive one that does the job. |
| Headless mode | `claude -p "..."` with `--output-format text`, `json`, or `stream-json`. Pre-approve the tools the job needs. |
| Sessions | `claude --continue` reopens the latest session in the current directory. `claude --resume` lets you pick one. `/clear` starts fresh. `/compact` summarises the history to free context. |
| Skills | `.claude/skills/<name>/SKILL.md`. The description loads up front, and the body and resources load only when relevant. |
| Hooks | Run at fixed events such as `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`, and `SessionStart`. A `PreToolUse` hook that exits with code 2 blocks the call. |
| Subagents | Markdown files in `.claude/agents/`, each with its own context window and an optional tool list. |
| Plugins | Bundle commands, agents, Skills, hooks, and MCP servers behind a `.claude-plugin/plugin.json` manifest. Shared through marketplaces. |
| Built-in commands | Examples: `/init`, `/memory`, `/permissions`, `/agents`, `/hooks`, `/mcp`, `/compact`, `/clear`. |

## Practice items

These 10 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

A company's IT team deploys a managed settings file to every developer laptop. It contains a deny rule for shell commands that start with `curl`. A developer working on an API client adds an allow rule for `curl` commands to the project's `.claude/settings.local.json` and restarts Claude Code. Claude is still blocked from running `curl`. What explains this, and what should the developer do?

A. Managed settings take precedence over every other layer and deny rules win over allow rules, so the developer needs the IT team to change the managed policy or must use an approved alternative.

B. Local project settings are ignored until they are committed, so the developer should move the allow rule into `.claude/settings.json` and push it.

C. The allow rule needs to go in the user's `~/.claude/settings.json`, which outranks managed settings for that user.

D. Claude Code caches permissions for a day, so the rule will take effect automatically tomorrow.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** Managed settings sit at the top of the precedence order, and deny rules are evaluated before allow rules. No project or user setting can unlock a command the organisation's policy denies. The fix is a policy change or an approved route.

- **B** is wrong because shared project settings rank below managed settings too, and local settings do not need committing to take effect.
- **C** is wrong because user settings are the lowest layer, not a way around managed policy.
- **D** is wrong because nothing here is a caching problem. The deny is working as configured.

</details>

### Item 2

A team's monorepo has a committed root `CLAUDE.md` with build commands and code standards that everyone shares. One developer wants Claude to use her personal staging URL and her preferred test shortcuts whenever she works in this repo. These notes should not affect teammates or appear in the repository. Where should she put them?

A. In the committed root `CLAUDE.md`, under a heading with her name so teammates know to ignore it.

B. In a new subdirectory `CLAUDE.md` under `docs/`, so it loads only for her.

C. In a `CLAUDE.local.md` file in the project, kept out of version control.

D. In the managed policy `CLAUDE.md`, so it applies on her machine only.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** `CLAUDE.local.md` is the place for personal, project-specific instructions that are not committed. It applies to her sessions in this repo and does not reach teammates.

- **A** is wrong because the root file is shared and loads for everyone, so her notes would steer teammates' sessions too.
- **B** is wrong because a subdirectory file is shared like any other committed file, and it loads for anyone who works in that directory.
- **D** is wrong because the managed policy file is for organisation-wide rules set by administrators, not personal notes.

</details>

### Item 3

A team adds a GitHub Actions job that asks Claude Code to review each pull request's diff and post a structured list of findings. A later step in the job parses those findings. In the first trial, the job hangs until it times out, and the logs show Claude Code waiting at an approval prompt for a file-read tool. Choose two.

A. Start an interactive Claude Code session in the job and pipe pre-recorded keystrokes into it to answer prompts.

B. Run Claude Code in headless mode with `claude -p` and a machine-readable output format such as `--output-format json`, so the next step can parse the result.

C. Run the job in `bypassPermissions` mode on the shared runner, which also holds the production deploy credentials.

D. Pre-approve exactly the tools the review needs, for example through project permission rules or `--allowedTools`, so the run never stops at a prompt.

E. Parse the terminal's human-readable output with regular expressions in the next step.

<details>
<summary>Answer and explanation</summary>

**Answer: B and D.** Headless mode with JSON output is built for pipelines: no interactive session, and a result the next step can parse. Pre-approving just the tools the review uses removes the approval prompt that caused the hang, without granting anything extra.

- **A** is wrong because scripting keystrokes into an interactive session is brittle and breaks when a prompt changes.
- **C** is wrong because skipping every permission check on a runner with production credentials grants far more than a review needs.
- **E** is wrong because human-readable output is not a stable format. JSON output exists for this purpose.

</details>

### Item 4

A platform team writes release notes the same way every sprint: gather merged pull requests, group them by area using a house template, apply a style guide, and run a script that checks links. The procedure is about two pages long, with a template file and a script. It is needed a few times a month, and the team does not want its two pages loaded into every Claude Code session. Which mechanism fits best?

A. Paste the procedure, template, and script into the root `CLAUDE.md` so Claude always knows it.

B. A `PostToolUse` hook that runs the release-notes procedure after every file edit.

C. A subagent whose system prompt contains the full procedure, invoked for every task in the repo.

D. A Skill containing the procedure in `SKILL.md`, with the template and script bundled as resources and a description that says when to use it.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A Skill packages a repeatable procedure with its resources and loads only when it is relevant. Until then, only its short description takes up context. That matches an occasional, multi-step workflow with files attached.

- **A** is wrong because CLAUDE.md loads into every session, which is exactly what the team wants to avoid.
- **B** is wrong because hooks run automatically on events. Release notes are an occasional task, not something to run after every edit.
- **C** is wrong because sending every task through a release-notes subagent misuses it. The need is on-demand know-how, not isolation for all work.

</details>

### Item 5

A team requires that every file Claude Code edits is run through the project formatter straight away. Their CLAUDE.md says "Always run the formatter after editing a file", but reviewers keep finding unformatted files in Claude's changes. The team wants formatting to happen after every edit without relying on Claude to remember. What should they configure?

A. A Rules file that repeats the formatting instruction for every source directory.

B. A `PostToolUse` hook matching the file edit and write tools that runs the formatter on the changed file.

C. A slash command named `/format` that developers can run when they remember.

D. A Skill that explains the formatter's options in detail.

<details>
<summary>Answer and explanation</summary>

**Answer: B.** A hook runs at a fixed point in Claude Code's lifecycle every time, whatever the model decides. A `PostToolUse` hook on edit and write tools formats each file right after it changes. That makes the requirement deterministic.

- **A** is wrong because a Rules file is still guidance that the model may not follow every time.
- **C** is wrong because it depends on people remembering, which is the gap the team wants to close.
- **D** is wrong because explaining the formatter does not make anyone run it.

</details>

### Item 6

A backend repository has strict conventions for database migration files under `db/migrations/`: naming patterns, reversible steps, and a forbidden list of operations. These rules are long and matter only when Claude works on migration files. Right now they sit in the root `CLAUDE.md`, and developers complain that sessions about the frontend carry a page of irrelevant migration rules. Which change fits best?

A. Delete the migration rules and rely on code review to catch violations.

B. Move the rules into a subagent and tell developers to invoke it before touching migrations.

C. Move the rules into a Rules file in `.claude/rules/` scoped with a `paths` pattern that matches `db/migrations/`, so they apply when Claude works on those files.

D. Move the rules into each developer's `~/.claude/CLAUDE.md` so they load from the user level instead.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Rules files let you split instructions into focused files and scope them to file paths. The migration rules then apply when Claude works with matching files, and frontend sessions stay clean. It keeps the guidance in the repo under version control.

- **A** is wrong because it drops guidance that prevents real mistakes.
- **B** is wrong because it relies on developers remembering to invoke the subagent, so the rules would often be missed.
- **D** is wrong because user-level files load in every session in every project, which makes the clutter worse and takes the rules out of the repo.

</details>

### Item 7

An engineering org has built a useful Claude Code setup: a code-review Skill, two slash commands for ticket triage, and a hook that blocks edits to generated files. Teams copy these files by hand into 12 repositories, and the copies have drifted apart. Leadership wants one versioned source that every repo can install and update from. Choose two.

A. Bundle the Skill, commands, and hook as a plugin, with a `.claude-plugin/plugin.json` manifest.

B. Paste the content of all four extensions into each repository's root `CLAUDE.md`.

C. Ask each developer to copy the files into their personal `~/.claude` directory once a quarter.

D. Rewrite all four extensions as a single subagent that every session delegates to.

E. Publish the plugin through a plugin marketplace that the teams add, so they install it and receive updates from one place.

<details>
<summary>Answer and explanation</summary>

**Answer: A and E.** A plugin packages Skills, commands, and hooks into one versioned unit. A marketplace is how teams discover, install, and update plugins from a single source, which ends the drift across 12 repos.

- **B** is wrong because pasting into CLAUDE.md turns a hook and commands into plain text and keeps the drift problem.
- **C** is wrong because manual copying is what caused the drift.
- **D** is wrong because a subagent cannot replace a hook's deterministic block or the commands' triggers.

</details>

### Item 8

A developer needs to refactor the payment module, which has little test coverage. Before any file changes, he wants Claude Code to read the module, map its dependencies, and propose a step-by-step plan for him to review. He wants a guarantee that nothing is edited during this phase. Which approach fits best?

A. Run in `acceptEdits` mode and ask Claude in the prompt not to edit anything yet.

B. Run in `bypassPermissions` mode so that the analysis finishes without interruptions.

C. Use plan mode, which lets Claude read and analyse code and propose a plan without making edits.

D. Run in default mode and reject each edit prompt manually as it appears.

<details>
<summary>Answer and explanation</summary>

**Answer: C.** Plan mode is meant for this phase: Claude can explore and propose, and edits are not made. The guarantee comes from the mode, not from a request in the prompt.

- **A** is wrong because `acceptEdits` approves file edits automatically, so a prompt slip would change files.
- **B** is wrong because it removes every permission check, which is the opposite of a guarantee.
- **D** is wrong because it depends on him catching every prompt, and it interrupts the analysis.

</details>

### Item 9

A developer asks Claude Code to find every place in a large legacy codebase where a deprecated logging function is called, and to report the call sites grouped by module. The search reads hundreds of files. Afterwards, the main session is sluggish, has lost track of the feature she was building before the search, and soon needs compacting. She runs this kind of search every week. What should she use next time?

A. A larger CLAUDE.md that lists every module, so that the search needs fewer file reads.

B. A `Stop` hook that clears the conversation as soon as the search finishes.

C. A slash command that pastes the search instructions into the main session.

D. A subagent that runs the search in its own context window and returns only the grouped list of call sites.

<details>
<summary>Answer and explanation</summary>

**Answer: D.** A subagent does the bulky reading in a separate context and hands back a short result. The main session keeps its focus on the feature work. For a repeated, read-heavy search, that isolation is the point.

- **A** is wrong because the search still has to read the files to find call sites, and a bigger CLAUDE.md adds to every session.
- **B** is wrong because clearing the conversation throws away the feature work she wanted to keep.
- **C** is wrong because a slash command still runs the search in the main session, with the same bloat.

</details>

### Item 10

A developer spent yesterday afternoon in Claude Code working through a tricky bug in a service repository. This morning he opens a terminal in the same repository and wants to pick up exactly where that conversation left off, with its history intact. It was the last session he ran in this directory. Which command fits?

A. `claude --continue`, which reopens the most recent session in the current directory.

B. `/clear` in a new session, then a summary of yesterday's work typed from memory.

C. `/compact` in a new session, which reloads and summarises the previous conversation.

D. `/init`, which rebuilds the project context from yesterday's session.

<details>
<summary>Answer and explanation</summary>

**Answer: A.** `claude --continue` reopens the latest session in the current directory with its history. If he needed an older session, `claude --resume` would let him pick one.

- **B** is wrong because `/clear` starts an empty context, and retyping from memory loses detail.
- **C** is wrong because `/compact` summarises the current session's history. It does not load a previous session.
- **D** is wrong because `/init` creates a starter CLAUDE.md for the project. It does not restore any conversation.

</details>

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Check this domain against the rest of the exam with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer), which reports percent correct by domain.

---

<!-- nav:bottom -->← Previous: [Domain 2: Applications and Integration](02-applications-and-integration.md) | Next: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md) → | [Back to contents](../../README.md#contents)<!-- /nav:bottom -->
