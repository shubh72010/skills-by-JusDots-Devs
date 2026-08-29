# Architecture

Docs-only hub for reusable agent skills. No build system, no runtime, no package manager — all content is Markdown under `skills/`.

## Repository Layout

```
skills-by-JusDots-Devs/
├── README.md                 # Hub index — skill table + quick-start (kept short by design)
├── ARCHITECTURE.md           # This file
├── CLAUDE.md                 # Claude Code instructions
├── AGENTS.md                 # OpenCode/Codex/Claude instructions
├── CONTRIBUTING.md           # Authoring rules for new skills
├── CODE_OF_CONDUCT.md
├── LICENSE                   # GPL-3.0
├── .github/
│   ├── pull_request_template.md
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   ├── dependabot.yml        # (added by setup)
│   └── workflows/
│       └── validate.yml      # (added by setup) frontmatter + markdown lint
├── skills/
│   ├── crossthink/
│   │   ├── SKILL.md          # YAML frontmatter + 5-phase workflow
│   │   ├── README.md         # Extended docs
│   │   ├── examples/         # browser-security, music-player, startup-ideas
│   │   └── images/           # hero/workflow banners
│   ├── segmented-connect-list/
│   │   └── SKILL.md          # Jetpack Compose seam-gap + per-position corners
│   ├── centered-hero-carousel/
│   │   └── SKILL.md          # Pager + clip/translate hero morph (3 fidelity tiers)
│   └── setup/
│       └── SKILL.md          # Project bootstrap: Analyze→Plan→Generate→GitHub→Security→Git→CI→Validate→Report
├── .gitignore
└── skills-lock.json          # local install artifact (gitignored)
```

## Skill Contract

- **Single source of truth:** `skills/<kebab-name>/SKILL.md` with YAML frontmatter + body.
- **Folder name must equal `name` in frontmatter** (kebab-case, validated by CI).
- **Frontmatter required fields:** `name`, `description`, `version`, `author`, `domain`, `tags`. `description` must contain concrete trigger phrases (`Use when...`) — this is how agents auto-load the skill.
- **Body:** self-contained, reusable snippets; no references to private codebases.
- **Optional:** `README.md`, `examples/`, `images/` inside the skill directory only.
- **Root docs:** `README.md` is the hub index (skill table). Detailed docs live in skill folders. See `CONTRIBUTING.md` for the full checklist.

## Current Skills

| Skill | Domain | Trigger |
|---|---|---|
| `crossthink` | General-purpose creative reasoning | `brainstorm`, `explore this topic`, `think about`, multi-angle discussion |
| `segmented-connect-list` | Android UI (Jetpack Compose) | `segmented list`, `connected rows`, `split panel` |
| `centered-hero-carousel` | UI / interaction design | `hero carousel`, `morphing card`, `Quick Picks`, `centered pager` |
| `setup` | Development workflow / GitHub automation | `/setup`, `bootstrap this repo`, `setup GitHub releases` |

## Data Flow / Conventions

1. Author adds `skills/<name>/SKILL.md` → updates root `README.md` skill table → opens PR (template enforces checklist).
2. Consumers install via `npx skills add shubh72010/skills-by-JusDots-Devs --skill <name>` (verified by frontmatter YAML parse + folder/name match).
3. No build/test/lint locally — CI validates frontmatter and links instead.

## Design Decisions

- **No `opencode.json` / lockfiles / CI workflows originally** — intentional for a docs hub; CI is intentionally minimal (markdown + YAML validation).
- **Root `README.md` kept short** — per `AGENTS.md` and `CONTRIBUTING.md` conventions, hub index only.
- **SSH remote** (`git@github.com:shubh72010/skills-by-JusDots-Devs.git`) — standard for contributor push.
- **`skills-lock.json` + `.agents/` + `.claude/` are gitignored** — consumer-local install artifacts, not repo content.
