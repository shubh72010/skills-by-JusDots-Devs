---
name: setup
description: Bootstrap an existing codebase into a documented, GitHub-configured, CI-enabled project. Use when the user says "/setup", "bootstrap this repo", "setup GitHub", "push to GitHub and setup releases", or wants the agent to analyze the codebase then generate README, ARCHITECTURE, DESIGN_LANGUAGE, CLAUDE.md, AGENTS.md, configure repo settings/topics/Dependabot, create a nightly branch, and add GitHub Actions for APK builds and releases.
version: 1.3.0
author: JusDots Devs - Folius
domain: Development workflow and GitHub automation
tags: setup, bootstrap, github, ci-cd, release-automation, apk, documentation, onboarding, security, supply-chain, attestations, rulesets, virustotal
related_skills: []
metadata:
  keywords:
  - setup
  - bootstrap
  - github release
  - nightly branch
  - apkgit
  - readme generation
  - architecture docs
---

# Setup — Project Bootstrap Skill

Turn an arbitrary local codebase into a properly documented, agent-readable, GitHub-configured, CI/CD-enabled project with one command: `/setup`.

## Philosophy

**Inspect → Plan → Modify → Validate → Report**

Never generate files blindly. Never declare success without validation. Every phase is idempotent — running `/setup` twice must not create duplicates, overwrite good docs, or break existing CI.

## Workflow Overview

```
/setup
│
├── 1. ANALYZE                 — read-only (modules, architecture, deps, build, git, docs, CI)
├── 2. PLAN                    — visible diff of intended changes (non-blocking; see Phase 2)
├── 3. GENERATE                — README, ARCHITECTURE, DESIGN_LANGUAGE*, CLAUDE.md, AGENTS.md
├── 4. GITHUB                  — repository metadata, settings, Dependabot, protection
├── 5. SECURITY & SUPPLY CHAIN — secrets, .gitignore, permissions, actions audit, attestations
├── 6. GIT                     — main → nightly (create or sync)
├── 7. CI/CD                   — detect current actions + construct workflow from project, build, changelog, release [+ optional VirusTotal]
├── 8. VALIDATE                — git, YAML, build, Actions, release asset + provenance
└── 9. REPORT                  — what changed / preserved / skipped + security audit + VirusTotal status + URLs

* DESIGN_LANGUAGE only when UI exists.
```

Strict order: **Analyze → Plan → Generate → GitHub → Security → Git → CI/CD → Validate → Report**. Architecture is known before any doc is written.

## Phase 1 — Analyze (read-only, no modifications)

Inspect without writing:

- **Project structure:** languages, frameworks, build system (Gradle/Maven/etc.), modules, entry points, package boundaries
- **Dependencies:** direct/transitive, version catalogs, lockfiles
- **Architecture:** layering, design patterns, UI system, data flow, key abstractions
- **Existing conventions:** code style, naming, commit history, branching
- **Git state:** remotes (SSH vs HTTPS), branches, dirty files, last commits, `origin` URL
- **Existing docs:** README, ARCHITECTURE, CLAUDE.md, AGENTS.md — record what exists and quality
- **Existing CI:** `.github/workflows/*`, `dependabot.yml`, action versions (flag Node 16/20 → 24 migrations)
- **Build verification:** does `./gradlew tasks` / `assembleDebug` succeed locally? Note JDK/SDK requirements

Produce an internal inventory. Do not modify files yet.

## Phase 2 — Plan (visible but non-blocking)

Render a setup plan then execute automatically — do not wait for confirmation on safe operations:

```
╭─ Project Setup ─────────────────────╮
│ Project: <detected name>            │
│ Type: Android / Kotlin / Gradle     │
│ Modules: 7                          │
│ Existing CI: release.yml (Node 20)  │
│ Git remote: GitHub / SSH            │
╰─────────────────────────────────────╯

Changes planned:
  + README.md (missing)
  ~ ARCHITECTURE.md (outdated → update)
  = AGENTS.md (good → preserve)
  + nightly branch (missing)
  ~ .github/workflows/release.yml (audit/fix)
  + .github/dependabot.yml (missing)
```

Flow:

```
Analyze
   ↓
Display plan   ← always visible
   ↓
Execute automatically
   ↓
Pause only if destructive/high-risk detected:
  force-push, delete branch, change visibility,
  overwrite divergent branch, remove existing CI
```

Reconciliation rules for every file/setting:

| State | Action |
|-------|--------|
| `missing` | create |
| `outdated` | update (preserve useful content, merge) |
| `good` | preserve (no-op) |
| `conflicting` | inspect + reconcile (never blind overwrite) |
| `exists, different intent` | audit/improve instead of replace |

For docs: if README/CLAUDE.md/AGENTS.md already exists, diff against codebase reality — keep accurate sections, replace hallucinated or stale ones, preserve project-specific notes.

## Phase 3 — Generate Project Knowledge

Derive every doc from Phase 1 inventory, not templates. Each file has a distinct job:

| File | Purpose |
|------|---------|
| `README.md` | What the project is + how to build/run/use it |
| `ARCHITECTURE.md` | How the codebase is structured (modules, layers, data flow, key decisions) |
| `DESIGN_LANGUAGE.md` | UI/UX principles, components, visual system (only if project has UI) |
| `CLAUDE.md` | Instructions/context optimized for Claude Code |
| `AGENTS.md` | Instructions for coding agents in general (OpenCode/Codex/Claude) |

Rules:
- Ground every claim in files found in Phase 1. No invented architecture.
- Keep root `README.md` as hub index when repo is a skills hub; detailed docs go in skill folders (see repo `CONTRIBUTING.md`).
- Include concrete commands (build, test, run) verified from build files, not guessed.
- Skip `DESIGN_LANGUAGE.md` entirely if project has no UI — do not generate a filler doc.

## Phase 4 — Repository Setup

Idempotent GitHub configuration. For each setting, check current value first via `gh` API:

- **Identity:** repository description, topics, visibility, default branch
- **Settings:** Actions permissions (least-privilege `GITHUB_TOKEN`), issues/discussions/wiki toggles, security settings
- **Dependabot:** `.github/dependabot.yml` — if exists, audit (correct ecosystem, schedule, grouped updates); if missing, create; never duplicate
- **Repository rules / branch protection:** GitHub now recommends **rulesets** over legacy branch protection. Detect existing rulesets (`gh api repos/{owner}/{repo}/rulesets`), detect legacy branch protection (`/branches/{branch}/protection`), and configure appropriate protection — e.g., required status checks, PR requirements, signed commits, linear history, block force-pushes. Only enforce if requested or repo is shared — do not lock a solo dev out.

Use SSH remote as requested: verify `git remote get-url origin` uses `git@github.com:` form; if HTTPS, offer to switch with `git remote set-url`.

## Phase 5 — Security & Supply Chain

Make the repo trustworthy, not just functional. Run between GitHub setup and CI generation so hardening informs workflow permissions.

- **Scan for committed secrets:** `git log --all -p`, `git diff --cached`, and staged files for high-entropy strings, private keys (`-----BEGIN`), tokens (`ghp_`, `AKIA`, `sk-`), `.env` contents. Use `gitleaks`/`trufflehog` if available; otherwise pattern scan. If secrets found, do not push — report, show `git rm --cached`, and recommend rotation.
- **Audit `.gitignore`:** ensure it covers local credentials (`*.jks`, `*.p12`, `*.keystore`, `local.properties`, `.env`, `.gradle/`, `keystore.properties`, `google-services.json` if private). Create or patch — never remove project-specific ignores.
- **Audit `GITHUB_TOKEN` permissions:** actively audit, not just generate. Set workflow-level `permissions: contents: write` only where release needs it, default to `permissions: {}` or `contents: read`. Flag `permissions: write-all` or missing `permissions` block as high-risk. Verify `actions: write` / `packages: write` only if needed.
- **Audit third-party Actions:** list all `uses:` pins, verify they are official (`actions/*`, `gradle/*`) or trusted publishers, check for pinned SHAs vs mutable tags, flag unpinned or obscure publishers. Replace deprecated/EOL majors (see Phase 7 action-version rules).
- **Configure Dependabot security updates:** ensure `.github/dependabot.yml` enables `versioning-strategy` and grouped security updates for detected ecosystems (gradle, npm, github-actions). Detect lockfiles (`gradle.lockfile`, `package-lock.json`, `pnpm-lock.yaml`) to confirm dependency pinning.
- **Configure artifact attestations & provenance:** add `actions/attest-build-provenance@v2` (or current major) to the release workflow with `permissions: { id-token: write, attestations: write, contents: read }` so the APK/AAB is cryptographically tied to repo + workflow + commit SHA + triggering event. Verify with `gh attestation verify` in Validate phase.

Record a security audit summary for the final report:

```
Security audit:
✓ No obvious secrets committed
✓ .gitignore covers local credentials
✓ GITHUB_TOKEN permissions minimized
✓ No unnecessary write permissions
✓ Third-party Actions reviewed
✓ No deprecated Actions
✓ Dependency update mechanism configured
✓ Release artifacts have provenance
```

If any check fails, fix or warn before proceeding to Git/CI phases — do not generate a release pipeline with known supply-chain gaps.

## Phase 6 — Git Workflow (nightly branch)

```
main ──→ nightly (from current main HEAD)
```

- If `nightly` does not exist locally or remotely: `git branch nightly` + `git push -u origin nightly`
- If it exists: verify it is not diverged unexpectedly; fast-forward or report divergence — never force-push without explicit user confirmation
- No extra commits on `nightly` beyond syncing `main` unless user requested nightly-specific changes

## Phase 7 — CI/CD (GitHub Actions)

Generate or audit `.github/workflows/release.yml` by constructing the workflow from the detected project and current action documentation — do not copy a hardcoded template.

**Detection before generation:**

```
Android + Gradle
        ↓
detect JDK (from gradle/libs.versions.toml, build.gradle, or toolchain)
detect Gradle wrapper + version
detect modules + which module produces the APK/AAB
detect APK task (assembleDebug vs assembleRelease, flavor dimensions)
detect tests (skip test step only if no tests exist)
detect signing (debug vs release keystore)
        ↓
generate appropriate CI
```

- Artifact path must come from detection (e.g., `app/build/outputs/apk/...` is only one layout — multi-module projects differ). Inspect `settings.gradle`, module `build.gradle`, and actual build outputs; never assume `app/build/outputs/apk/debug/*.apk`.
- JDK version, Gradle cache setup, and Android SDK setup must match the detected project.

**Trigger:** `push` to `main` (and `workflow_dispatch` for manual runs).

  **Pipeline shape (adapt to detected build system):** checkout → setup JDK/SDK → cache dependencies → run tests → build APK → generate changelog → create GitHub Release → upload APK (+ attest provenance + optional VirusTotal scan — see below).

  **Optional VirusTotal scan (opt-in, never default):**

  Use the `question` tool. Never ask for the key unless the user first enables VirusTotal:

  ```
  1. Ask: "Enable VirusTotal scanning for release APKs?" (Yes / No)
  2. If No  → skip VirusTotal entirely — do not add scan step, do not ask for key
  3. If Yes → ask for VirusTotal API key (second question, password-style)
  4. Store key as GitHub repository secret:
       gh secret set VIRUSTOTAL_API_KEY --body "<key>" --repos <owner>/<repo>
     Never write the key into source files, workflow YAML, logs, or git history
     Workflow must consume it only as:
       env:
         VIRUSTOTAL_API_KEY: ${{ secrets.VIRUSTOTAL_API_KEY }}
     Never as:
       env:
         VIRUSTOTAL_API_KEY: "actual-key-value"
  5. Add VirusTotal scan step after APK build and before / as gate for release
  6. Record scan result in workflow summary and release notes
  ```

  Explicit result handling (do not silently publish on detections):

  ```
  VirusTotal result:
  ├── clean / no detections        → continue release
  ├── suspicious / detections      → do NOT publish silently
  │                                 → mark workflow failed + report findings in summary/release notes
  └── API failure / quota / unavailable → report scan failure separately
                                          → do not pretend the APK was scanned
  ```

  VirusTotal is never mandatory for `/setup` — repo must be fully usable without it. On re-run, detect existing `VIRUSTOTAL_API_KEY` secret and existing scan step; do not re-ask unless user wants to change it.

Common structure to produce:

- `permissions: contents: write` (least-privilege — only what release needs; `id-token: write` + `attestations: write` if Phase 5 attestations enabled — see Phase 5)
- `concurrency: group: release-${{ github.ref }}, cancel-in-progress: false`
- `actions/checkout`, `actions/setup-java`, `android-actions/setup-android`, `softprops/action-gh-release`, `actions/attest-build-provenance` (or equivalents) at their **current official majors** — resolved at generation time, not hardcoded
- `fetch-depth: 0` for changelog generation
- Changelog from `git log` between last tag and HEAD, with `generate_release_notes: true` as fallback

Key rules:
- **Action versions (future-proof):**
  - Inspect the current official major version of every GitHub-maintained action before generating or updating workflows.
  - Never downgrade an existing action.
  - Replace deprecated/EOL action majors when a compatible newer major exists.
  - Prefer official GitHub action releases and their current documented usage.
  - Verify Node/runtime compatibility with the current runner (e.g., Node 24 transition completed June 2026 — do not pin to Node 16/20 actions).
  - As of 2026-08-29 examples: `actions/checkout@v7`, `actions/setup-java@v6` — but re-check at runtime; do not hard-code `v4`.
- **Signing:** if no keystore configured, build `assembleDebug` — never invent signing credentials. Document how to add release signing as a follow-up.
- **Gradle caching:** use `setup-java` gradle cache or `gradle/actions/setup-gradle` at current major; add `concurrency` to avoid overlapping releases.
- **Changelog:** derive from `git log` between last tag and HEAD; include `generate_release_notes: true` as fallback.
- **If CI already exists:** diff and patch (update action versions, add missing steps) rather than overwriting.
- **Retention:** set artifact retention if uploading artifacts separately.
- **VirusTotal (if enabled):** hash APK → upload/scan via VirusTotal API using `${{ secrets.VIRUSTOTAL_API_KEY }}` → parse detection count → gate release per result handling above → write summary to `$GITHUB_STEP_SUMMARY`. On `virustotal` API errors, surface as separate failed step.

## Phase 8 — Validate

Never skip:

1. `git status` — no unintended diffs, no secrets committed, no VirusTotal key in diff
2. YAML validity — `actionlint` or `yamllint` if available, else `python -c 'import yaml; yaml.safe_load(open(f))'` for each workflow; verify workflow does not contain literal `VIRUSTOTAL_API_KEY: "` value
3. Local build — detected build command (e.g., `./gradlew assembleDebug`) succeeds
4. Push — `git push origin main nightly` (or only branches that changed)
5. Verify Actions run — `gh run list --limit 3` / `gh run watch` after push; if workflow fails, diagnose logs and fix before reporting success
6. If release created: verify APK asset exists on release and is downloadable
7. Verify artifact attestations — `gh attestation verify <apk> --owner <org>` if attestations were configured in Phase 5/7
8. If VirusTotal enabled: verify scan step exists and uses `secrets.VIRUSTOTAL_API_KEY`, and that secret is set (`gh secret list`); do not echo secret value

If validation fails, fix and re-validate — do not report success with a broken workflow.

## Phase 9 — Report

Final output after validation:

```
Setup complete.

Docs:       README.md (created), ARCHITECTURE.md (updated), AGENTS.md (preserved)
GitHub:     description/topics/rulesets updated, Dependabot enabled, nightly branch synced
Security:   ✓ secrets clean, ✓ .gitignore hardened, ✓ permissions minimal, ✓ provenance enabled
CI:         .github/workflows/release.yml (created, current actions, attested, validated)
Build:      ./gradlew assembleDebug ✓
Release:    https://github.com/<org>/<repo>/releases/tag/nightly-42
APK:        https://github.com/<org>/<repo>/releases/download/nightly-42/app-debug.apk (attested)
VirusTotal: skipped (not enabled) | enabled — clean (0/72) | enabled — detections → release gated
Actions:    https://github.com/<org>/<repo>/actions/runs/123
```

Include remediation notes if anything was skipped (e.g., "Release signing not configured — debug APK built. Add keystore to enable release APK — see workflow comments.").

## Failure Recovery & Guardrails

- **SSH required but HTTPS found:** do not auto-rewrite without confirming; show `git remote set-url origin git@github.com:...` command.
- **Dirty working tree:** stash or abort setup — never commit unrelated dirty files as part of setup.
- **Existing nightly diverged:** report divergence, ask before force-pushing; default is to not overwrite.
- **No Gradle wrapper:** report and suggest `gradle wrapper` instead of assuming `gradle` on PATH.
- **No tests:** skip test step with a comment, do not fail the workflow on missing tests.
- **Dependabot/Renovate conflict:** if Renovate present, do not add Dependabot — note the choice.
- **Secrets:** never commit keystores, tokens, or `.env`; add to `.gitignore` if missing.

## Idempotency Checklist

Running `/setup` again must produce:

- [ ] No duplicate workflows or dependabot configs
- [ ] No overwritten good docs (audited, not replaced)
- [ ] No duplicate `nightly` branch or force-push
- [ ] No downgraded action versions or unpinned third-party Actions
- [ ] No duplicate rulesets or overwritten custom permissions
- [ ] Same final state (converged) — second run is a no-op or minor patch

## Execution Order (strict)

`Analyze (read-only) → Plan (visible, non-blocking) → Generate → GitHub → Security & Supply Chain → Git (nightly) → CI/CD (detect + generate) → Validate → Report`

Pause for confirmation only on destructive/high-risk operations (force-push, delete branch, visibility change, overwrite divergent branch, remove existing CI).
