# CLAUDE.md — skills-by-JusDots-Devs

Docs-only skills hub. No build, test, or package manager — all content is Markdown under `skills/`.

## Structure

- `skills/<kebab-name>/SKILL.md` — single source of truth per skill (YAML frontmatter + body).
- `skills/<kebab-name>/README.md`, `examples/`, `images/` — optional; keep inside the skill directory.
- `README.md` (root) is the hub index (skill table). `CONTRIBUTING.md` has authoring rules. No `opencode.json`, no workflows originally.

## Adding or Updating a Skill

1. Folder name must exactly match `name` in `SKILL.md` frontmatter (kebab-case).
2. Frontmatter required: `name`, `description`, `version`, `author`, `domain`, `tags`. `description` must include trigger phrases (`Use when...`) — agents use this to auto-load the skill.
3. Body: self-contained, reusable snippets. No private repo references.
4. Update root `README.md` skill table when adding/renaming.
5. Keep root `README.md` short; detailed docs go in the skill folder.

## Verification (no build to run)

- Validate frontmatter parses as YAML and `skills/<name>` matches `name` field.
- Consumer check: `npx skills add shubh72010/skills-by-JusDots-Devs --list` and `--skill <name>`.

## Conventions

- One directory per skill; never add skill files to repo root.
- GPL-3.0. PR template at `.github/pull_request_template.md` — requires `SKILL.md` format, examples for new skills, no breaking changes.
- Current skills: `crossthink`, `segmented-connect-list`, `centered-hero-carousel`, `setup` — read one `SKILL.md` for style before adding a new one.

## When Using Skills

- `crossthink` — 5-phase divergent brainstorming (explore → detached domains → cross-pollinate → challenge → synthesize).
- `segmented-connect-list` — Compose rows as separate Cards with 2dp seam + per-position corner radii (28dp outer / 6dp inner).
- `centered-hero-carousel` — pager + clip/translate hero morph (3 fidelity tiers, keyline math inside SKILL.md).
- `setup` — `/setup` bootstrap: docs → GitHub settings → nightly branch → CI.
