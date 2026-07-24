# Contributing to CrossThink

## Adding a New Skill

1. Create a new directory in `.opencode/skills/`.
2. Add a `SKILL.md` with proper metadata (name, description, version, domain, tags).
3. Include examples in `examples/` if applicable.
4. Add a workflow diagram in `images/`.
5. Follow the existing format: phases, failure modes, guardrails, evaluation criteria.

## Skill Format

Each skill must include:
- Metadata (YAML frontmatter)
- Purpose and activation criteria
- Instructions with phases
- Internal reasoning workflow
- Best practices
- Failure cases and guardrails
- Example conversations and outputs
- Evaluation criteria

## Pull Request

- Keep changes focused
- Include examples for new skills
- Update README if adding a new top-level skill
