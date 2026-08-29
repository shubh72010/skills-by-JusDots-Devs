# AGENTS.md

Docs-only skills hub — no build, test, lint, or package manager. All content is Markdown under `skills/`.

## Structure

- `skills/<kebab-name>/SKILL.md` — single source of truth per skill (YAML frontmatter + body).
- `skills/<kebab-name>/README.md`, `examples/`, `images/` — optional extended docs; keep everything inside the skill's own directory.
- `README.md` (root) — hub index with skill table. `CONTRIBUTING.md` — authoring rules. No `opencode.json`, no CI workflows, no lockfiles.

## Adding or Updating a Skill

1. Folder name must exactly match `name` in `SKILL.md` frontmatter (kebab-case).
2. Frontmatter required fields: `name`, `description`, `version`, `author`, `domain`, `tags`. `description` must include concrete trigger phrases (`Use when...`) — this is how agents auto-load the skill.
3. Body: self-contained, reusable snippets. No references to private/proprietary codebases.
4. Update root `README.md` skill table when adding/renaming a skill.
5. Keep root `README.md` as short hub index; detailed docs go in the skill folder.

## Install Verification (consumer-side, not repo build)

```bash
npx skills add shubh72010/skills-by-JusDots-Devs --list
npx skills add shubh72010/skills-by-JusDots-Devs --skill <name>
```

No repo-local command to run — verify by checking `SKILL.md` frontmatter parses as valid YAML and folder/name match.

## Conventions

- One directory per skill; never add skill files to repo root.
- GPL-3.0 (`LICENSE`). PR template at `.github/pull_request_template.md` — checklist requires `SKILL.md` format, examples for new skills, no breaking changes.
- Current skills: `crossthink`, `segmented-connect-list`, `centered-hero-carousel`, `setup` — read one `SKILL.md` for style reference before adding a new one.
