---
name: devlog-cli
description: Use the DevLog CLI to record development work after an agent coding session, including agent-written concise, detailed, formal, or impersonal log entries without relying on DevLog's --ai flag. Use this skill whenever the user asks to log work, write a devlog entry, summarize what just changed, capture today’s coding session, record progress for a project, choose a DevLog style/template, or update their daily development notes from the current OpenCode/Codex/Claude Code context. This skill is especially relevant after making code changes, running tests, debugging issues, reviewing code, or finishing a task in a coding harness.
---

# DevLog CLI

Use DevLog to record useful, factual development notes from the current agent session. The CLI is the source of truth, so prefer calling `devlog` instead of editing `~/.devlog/` files directly. When style matters, the agent writes the styled entry text and passes it to `devlog entry add`; do not rely on `devlog summary create --ai` or `--style`.

## What DevLog Does

DevLog stores local daily work notes under `~/.devlog/`. Stable commands are:

```bash
devlog init
devlog entry add "Fixed pagination bug on transactions list" -p bitfinance -t frontend --date 2026-04-14
devlog entry list --date 2026-04-14
devlog summary create --date 2026-04-14
devlog summary show --date 2026-04-14
```

Avoid planned or scaffolded commands unless the user explicitly asks and you have verified they work in the installed CLI. In particular, do not rely on entry duration, edit/delete, week filters, config commands, AI summaries, `--style`, or summary range commands as stable behavior.

## Core Workflow

1. Confirm the CLI is available.
   Run `devlog --help` or `command -v devlog`. If unavailable but you are inside the DevLog source repository, use `go run .` as the command prefix for this session.

2. Initialize if needed.
   Run `devlog init` before the first entry if `~/.devlog/config.json` appears missing, or if DevLog reports that config cannot be read. `devlog init` is intended to be safe and not overwrite existing config.

3. Extract the entry from current context.
   Base the entry on work actually performed or discussed in the session: files changed, tests run, bugs diagnosed, implementation completed, review findings, or blockers uncovered. Do not invent outcomes, tests, or project names.

4. Choose the writing style.
   If the user requested `concise`, `detailed`, `formal`, or `impersonal`, use that style. If no style is requested, prefer `concise`. If you need the user’s configured default and it is safe to inspect local DevLog config, read `~/.devlog/config.json` and use `defaults.style` when it is one of the supported styles. Use the corresponding template in `templates/` to draft the entry text before calling the CLI.

5. Choose project and tags.
   Prefer the repository, package, app, or user-provided project name. If uncertain, use the current folder name or omit `-p` so DevLog can use its default project. Use short comma-separated tags such as `backend`, `frontend`, `tests`, `docs`, `debugging`, `refactor`, `release`, `cli`, or the main language/framework.

6. Add one or more entries.
   Use one entry for a small single task. Use multiple entries when the session included distinct pieces of work that a human would want separated later, such as implementation plus test repair plus documentation. Keep each description aligned to the selected style and suitable for `devlog entry add "..."`.

7. Verify the write.
   Run `devlog entry list` for today, or `devlog entry list --date YYYY-MM-DD` when a date was specified. Confirm the entry appears. If the user asked for a summary, run `devlog summary create` and then `devlog summary show` for the same date.

## Style Templates

DevLog has planned CLI-level AI/style support, but this skill intentionally handles style inside the coding harness. The agent should draft the final entry text directly, then pass that text to `devlog entry add`.

Use the CLI app templates as the source of truth when they are available in the current DevLog repository:

- `<repo>/templates/concise.md` for short, scan-friendly notes. This is the default.
- `<repo>/templates/detailed.md` for richer context, purpose, and verification state.
- `<repo>/templates/formal.md` for polished professional/timesheet wording.
- `<repo>/templates/impersonal.md` for neutral entries that avoid `I`/`we`.

If the skill is installed or invoked outside the DevLog repository and those app templates are unavailable, use the bundled fallback copies:

- `<skill>/templates/concise.md`
- `<skill>/templates/detailed.md`
- `<skill>/templates/formal.md`
- `<skill>/templates/impersonal.md`

Read only the relevant template unless comparing styles or the user asks to see all options. Treat these templates as guidance for drafting the entry text that will be passed to `devlog entry add`; they are not CLI flags.

If the user requests a summary in a specific style, prefer writing styled entries first and then use stable `devlog summary create/show`. Do not pass `--ai` or `--style` to the CLI unless the user explicitly asks to test unstable CLI behavior.

## Entry Writing Guidelines

Write entries that are useful to future-you scanning a daily log. Apply the selected style while preserving factual accuracy.

Good entries:

```text
Added validation coverage for DevLog summary frontmatter parsing
Diagnosed failing release workflow caused by missing GoReleaser token
Refactored checkout totals calculation to share tax and discount logic
Updated README with stable DevLog CLI commands and data storage notes
```

Weak entries:

```text
Worked on code
Fixed stuff
Made changes
Ran tests
```

Prefer specific verbs unless the impersonal style calls for a passive construction: `Implemented`, `Fixed`, `Diagnosed`, `Refactored`, `Reviewed`, `Documented`, `Verified`, `Updated`, `Removed`, `Added`.

Mention test commands only when they were actually run. If tests failed, log the verified state, for example `Investigated failing checkout tests and traced the regression to tax rounding`.

## Inferring From A Coding Harness

When the user says something like "log this", "write a DevLog entry", or "capture today’s work", use the context already available in the harness before running broad discovery. Useful signals include:

- The user’s original request and follow-up corrections.
- Files you edited or inspected.
- Commands you ran and their results.
- Final status: implemented, partially completed, blocked, verified, or not verified.

If the session context is thin, inspect `git status --short` and, when appropriate, `git diff --stat` to understand changed files. Do not read large diffs just to create a log entry unless the available context is insufficient.

If you are not confident what should be logged, ask one short clarification question instead of recording a vague or fabricated entry.

## Command Patterns

Today’s entry:

```bash
devlog entry add "Updated DevLog CLI skill draft and evaluation prompts" -p devlog -t cli,docs
devlog entry list
```

Styled formal entry:

```bash
devlog entry add "Prepared the DevLog CLI skill guidance for harness-based log creation, including style templates and stable command usage." -p devlog -t cli,docs,skills
devlog entry list
```

Styled impersonal entry:

```bash
devlog entry add "DevLog style templates were added for concise, detailed, formal, and impersonal agent-written entries." -p devlog -t cli,docs,templates
devlog entry list
```

Specific date:

```bash
devlog entry add "Fixed summary frontmatter parsing for generated Markdown" -p devlog -t cli,bugfix --date 2026-05-08
devlog entry list --date 2026-05-08
```

Multiple distinct entries:

```bash
devlog entry add "Implemented entry listing by date in the DevLog CLI" -p devlog -t cli
devlog entry add "Verified dated entry listing with Go tests" -p devlog -t tests
devlog entry list
```

Create and show a summary:

```bash
devlog summary create --date 2026-05-08
devlog summary show --date 2026-05-08
```

## Reporting Back

After logging, respond with:

- The entry or entries added.
- The project and tags used.
- Whether verification succeeded.
- Any summary created, if requested.

Keep the response brief. The important durable record is the DevLog entry itself.

## Failure Handling

If `devlog init` says the config already exists, continue; this usually means DevLog is ready.

If `devlog` is not installed and you are not in the source repository, tell the user that the CLI was not found and provide the exact command you would have run.

If the CLI returns an error, report the error and do not claim the entry was recorded. If the fix is obvious and safe, such as initializing DevLog first, do it and retry once.

If the user asks to edit, delete, or rewrite past entries, explain that those commands are not stable in the current documented CLI and ask whether they want manual file editing or a new corrective entry instead.
