<!-- nav:top -->
[Contents](../../README.md#contents) · Previous: [Domain 2: Applications and Integration](02-applications-and-integration.md) · Next: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md)
<!-- /nav:top -->

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

These 3 items are original, written for these notes. They are not taken from Anthropic's exam or exam guide.

### Item 1

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

### Item 2

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

### Item 3

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

Want more? The free timed Developer, Foundations diagnostic on PrepClubs has the full 25 questions, scored by domain: [Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer).

## Next step

1. Redo any item you missed, without opening the explanation, and write the trap you fell for in one line.
2. Spend 20 minutes doing one skill from "What this domain measures" for real, in Claude or your own project.
3. Take a timed run with the [free timed Developer, Foundations diagnostic on PrepClubs](https://prepclubs.com/free-practice-tests/claude-certified-developer?utm_source=github&utm_medium=referral&utm_campaign=study-notes&utm_content=claude-certified-developer) to practise pacing across the whole exam.

---

---

<!-- nav:bottom -->
← Previous: [Domain 2: Applications and Integration](02-applications-and-integration.md) | Next: [Domain 4: Eval, Testing, and Debugging](04-eval-testing-and-debugging.md) → | [Back to contents](../../README.md#contents)
<!-- /nav:bottom -->
