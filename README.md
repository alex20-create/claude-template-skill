# Claude Template Skill

*[Versión en español](README.es.md)*

Two Claude Skills that help decide which `CLAUDE.md` template(s) to apply to a project, based on a collection of `.md` templates the user maintains (base templates, a migration guide, a development-history template, platform-specific docs for things like Cloudflare or Convex).

Both skills ask the same core questions before recommending anything:

- **What kind of project is this?** Technical (pure code), non-technical (screenplay, film production, writing, research), or mixed (a tool whose own content/creative decisions are inseparable from the code, like a custom screenwriting app).
- **Does a `CLAUDE.md` already exist?** And if so, was it verified against the real codebase (has specific, unfakeable detail) or just handwritten/inherited (safe to migrate)?
- **Is this a short-lived/experimental project or something maintained for weeks/months?** This decides whether to pull in the compact template or the fuller one, and whether to add a development-history template.
- **Which platform-specific docs actually apply?** These are only ever copied for the services the project genuinely uses — a Cloudflare doc that covers Workers, D1, KV, R2, etc. gets trimmed to just the sections the project needs, not copied wholesale.

Neither skill invents missing information: unknown fields are marked `[PENDING: ...]` rather than guessed.

## The two skills

### [`skills/claude-ai/`](skills/claude-ai/SKILL.md) — for claude.ai Projects

Runs inside a claude.ai Project. It has no direct filesystem access, so it:
- Reads the template collection from the Project's knowledge files (via `project_knowledge_search`).
- Asks the user for anything it can't infer (project type, maintenance horizon, existing `CLAUDE.md` state).
- Generates the final `CLAUDE.md` (and any companion files like `CLAUDE-history.md`) with `create_file`.
- Hands them back to the user with `present_files` — the user then copies them into their project folder by hand.

### [`skills/claude-code/`](skills/claude-code/SKILL.md) — for Claude Code

Runs inside Claude Code, so it has real filesystem access and skips the manual-copy step. It:
- Lists and reads the local templates folder directly (default `C:\Claude Code\Plantillas`).
- Inspects the target project's actual files — reads `package.json`/`requirements.txt`/etc. to infer the tech stack, checks whether a `CLAUDE.md` already exists, compares an existing one against the real repo to tell "verified" from "handwritten."
- Writes the composed `CLAUDE.md` (and companion files) straight into the project, after confirming with the user.

## Why two separate skills instead of one

The decision logic (project type, template rules, how to handle mixed projects, how to preserve user-specific content) is identical. What differs is *how each environment gets its inputs and delivers its output* — one has to ask-and-hand-back, the other can inspect-and-write-directly. Keeping them as separate `SKILL.md` files avoids one skill needing conditional branches for "if I have filesystem access, do X, otherwise do Y" scattered throughout.
