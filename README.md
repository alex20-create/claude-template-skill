# Claude Template Skill

Skills to pick which `CLAUDE.md` template(s) to apply to a project, based on a local or Project-hosted collection of `.md` templates.

- [`skills/claude-ai/`](skills/claude-ai/SKILL.md) — version for claude.ai Projects (reads the template collection from Project knowledge, hands generated files back via `create_file`/`present_files`).
- [`skills/claude-code/`](skills/claude-code/SKILL.md) — version for Claude Code (direct filesystem access: reads the local templates folder, inspects the target project, and writes the final files itself).

Both decide template(s) based on project type (technical / non-technical / mixed), maintenance horizon, and existing `CLAUDE.md` state, then compose the final file(s).
