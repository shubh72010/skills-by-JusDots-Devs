# skills-by-JusDots-Devs

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](LICENSE)
[![Skills Hub](https://img.shields.io/badge/Hub-skills--by--JusDots--Devs-purple)](https://github.com/shubh72010/skills-by-JusDots-Devs)

A hub of reusable agent skills for **OpenCode**, **Claude Code**, and **Codex** —
covering specialized reasoning, development workflows, and UI/UX building blocks.

## Available Skills

| Skill | Category | Description | Docs |
|---|---|---|---|
| **crossthink** | Reasoning | Divergent cross-domain brainstorming: a 5-phase workflow that separates exploration, independent domain analysis, synthesis, and critical evaluation to avoid anchoring bias. | [README](skills/crossthink/README.md) · [SKILL](skills/crossthink/SKILL.md) |
| **segmented-connect-list** | UI building blocks | Jetpack Compose rows that look "separate but connected" — a single rounded panel split into segments via per-position corner radii and a 2dp seam gap. | [SKILL](skills/segmented-connect-list/SKILL.md) |
| **centered-hero-carousel** | UI building blocks | Morphing hero carousel — a big center card that continuously changes width into different-size rectangles as it scrolls (Material 3 / Quick Picks effect). Pager + clip + translate, with a no-experimental-API fallback and cross-framework porting math. | [SKILL](skills/centered-hero-carousel/SKILL.md) |
| **setup** | Workflow | Project bootstrap: analyze codebase → generate README/ARCHITECTURE/DESIGN_LANGUAGE/CLAUDE/AGENTS → configure GitHub settings/topics/Dependabot → create nightly branch → add Actions for APK build + changelog + release. | [SKILL](skills/setup/SKILL.md) |

## Quick Start

### Install all skills

```bash
npx skills add shubh72010/skills-by-JusDots-Devs
```

### Install a specific skill

```bash
npx skills add shubh72010/skills-by-JusDots-Devs --skill crossthink
npx skills add shubh72010/skills-by-JusDots-Devs --skill segmented-connect-list
```

### List available skills

```bash
npx skills add shubh72010/skills-by-JusDots-Devs --list
```

### Use a skill

```text
crossthink: Brainstorm browser security
segmented-connect-list: Add a segmented connected list for the queue screen
```

---

## Repository Structure

```text
skills-by-JusDots-Devs/
├── README.md                  ← Hub index (you are here)
├── LICENSE                    ← GPL-3.0
├── CONTRIBUTING.md            ← How to add a new skill
├── CODE_OF_CONDUCT.md
└── skills/
    ├── crossthink/            ← Cross-domain brainstorming methodology
    │   ├── SKILL.md           ← Skill definition
    │   ├── README.md          ← Skill documentation + examples
    │   ├── examples/          ← Worked examples
    │   └── images/            ← Banners and diagrams
    ├── segmented-connect-list/← Compose "separate but connected" list rows
    │   └── SKILL.md
    ├── centered-hero-carousel/← Morphing hero carousel (pager + clip/translate)
    │   └── SKILL.md
    └── setup/                 ← Project bootstrap (/setup → docs + GitHub + CI)
        └── SKILL.md
```

## Adding a Skill

Each skill lives in `skills/<name>/` as a `SKILL.md` with YAML metadata. See
[CONTRIBUTING.md](CONTRIBUTING.md) for the full guide.

## License

[GPL-3.0](LICENSE)