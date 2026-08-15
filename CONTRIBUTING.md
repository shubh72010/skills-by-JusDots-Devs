# Contributing

Thanks for wanting to add a skill to the hub. Each skill is a self-contained
directory under `skills/`.

## Adding a New Skill

1. **Create the directory** `skills/<skill-name>/` — the folder name must match
   the `name` field in the SKILL.md frontmatter (kebab-case).

2. **Write `SKILL.md`** with YAML frontmatter:

   ```yaml
   ---
   name: my-skill
   description: One paragraph, written so an agent auto-triggers it. Include "Use
     when..." triggers.
   version: 1.0.0
   author: <you>
   domain: <e.g. Android UI development>
   tags: comma, separated, keywords
   related_skills:
     - other-skill-name
   metadata:
     keywords:
     - keyword
   ---
   ```

   - The `description` is what matters most — it is what determines when an agent
     loads the skill. Be specific, and name concrete trigger phrases.
   - Body: explain the **why** and the **how**. Include self-contained code
     snippets. Avoid references to proprietary codebases — skills should be
     reusable anywhere.

3. **(Optional)** Add a `README.md` for longer docs, plus `examples/` and
   `images/` folders for worked examples and assets. Keep everything inside the
   skill's own directory.

4. **Register it** in the root `README.md` skill table.

## Checklist

- [ ] `skills/<name>/SKILL.md` exists and folder name matches the frontmatter `name`
- [ ] `description` includes concrete trigger phrases
- [ ] Self-contained, no references to private projects
- [ ] Root `README.md` table updated
- [ ] No breaking changes to existing skills

## Conventions

- One directory per skill, everything (docs, examples, images) inside it.
- Reuse the `skills/` layout — do not add skill files to the repository root.
- Keep the root `README.md` a short hub index; detailed docs live in each skill folder.