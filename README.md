# AI Workflows

Reusable agent workflow assets, skills, and templates for coding assistants.

## Contents

- `skills/` - Agent skills that can be installed or referenced by compatible coding assistants.
- `skills/devlog/` - DevLog CLI skill for recording development work from an agent coding session.
- `skills/devlog/SKILL.md` - Skill instructions and workflow constraints.
- `skills/devlog/templates/` - Writing style templates for concise, detailed, formal, and impersonal DevLog entries.

## DevLog Skill

The DevLog skill guides an agent to record factual development notes with the local `devlog` CLI. It is designed for session wrap-up tasks such as:

- Logging completed code changes.
- Capturing debugging or review outcomes.
- Writing concise, detailed, formal, or impersonal daily entries.
- Verifying that entries were written with `devlog entry list`.

The skill intentionally relies on stable CLI commands and has the agent write styled entry text directly instead of depending on experimental `devlog --ai` or `--style` behavior.

## Skill Layout

Each skill should include:

- `SKILL.md` with frontmatter metadata and the operational workflow.
- Supporting templates or references in subdirectories when the workflow needs reusable examples.
- Clear failure handling so agents do not claim work was completed when a command fails.

## Maintenance

- Keep skill instructions grounded in commands that have been verified locally.
- Update bundled templates when the corresponding source application templates change.
- Avoid storing private logs, credentials, tokens, or machine-specific state in this repository.
