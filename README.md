# crossthink

A structured reasoning methodology that reduces anchoring bias by separating exploration, independent domain analysis, synthesis, and critical evaluation into distinct phases.

> **Not just a brainstorming prompt.** This defines a reasoning process with phases, failure modes, guardrails, evaluation criteria, and worked examples — something other agents can actually use or build on.

## The Problem

Standard brainstorming tends to amplify the user's existing assumptions, anchors too quickly on the first framing, and produces variations on a single theme. This methodology addresses those failures by:

- **Separating exploration from connection.** Ideas are generated independently before any mapping between domains.
- **Forcing cognitive detachment.** The agent must genuinely leave the original topic and explore unrelated domains with equal depth.
- **Challenging before concluding.** Every generated idea is stress-tested for realism, evidence, and hidden assumptions before the final synthesis.
- **Maximizing novelty.** Cross-domain concepts emerge from structural similarity, not superficial analogy.

## The Five-Phase Workflow

```
Topic
  │
  ▼
┌─────────────────────────┐
│  1. Primary Exploration │   Explore the topic deeply — fundamentals,
│                         │    assumptions, strengths, weaknesses,
│                         │    opportunities, limitations, future.
│                         │    Stop when diminishing returns hit.
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│ 2. Independent Domains  │   Detach completely. Pick 2–4 distant
│                         │    domains (biology, aviation, economics,
│                         │    architecture, etc.). Explore each
│                         │    with equal depth — no forced connections.
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│    3. Cross-Pollination │   Now search for relationships.
│                         │    Shared patterns, transferable ideas,
│                         │    analogous systems, hidden assumptions,
│                         │    surprising similarities.
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│      4. Challenge       │   Subject every idea to scrutiny.
│                         │    Is it realistic? What assumptions
│                         │    are wrong? Evidence for/against?
│                         │    Has it been tried? Why might it fail?
└────────────┬────────────┘
             │
             ▼
┌─────────────────────────┐
│   5. Final Synthesis    │   Strongest insights, unexpected
│                         │    connections, novel concepts,
│                         │    practical recommendations,
│                         │    open questions, future directions.
│                         │    Explain why each insight emerged.
└─────────────────────────┘
```

## Quick Start

### With OpenCode

```
/opencode: crossthink brainstorm browser security
```

### With Claude Code

```
Use the crossthink skill to explore: browser security
```

### With Codex

```
/run crossthink Topic: browser security
```

### General usage

Invoke the skill with any open-ended topic. The skill handles the rest.

## Example Prompts

- "Brainstorm future directions for batteries"
- "Discuss browser security from multiple angles"
- "Design a music player using divergent thinking"
- "Explore what if education worked like manufacturing"
- "Think about the future of work"

## Examples

| Topic | Output |
|-------|--------|
| Browser security | [examples/browser-security.md](examples/browser-security.md) |
| Music player | [examples/music-player.md](examples/music-player.md) |
| Startup ideas | [examples/startup-ideas.md](examples/startup-ideas.md) |

## Before vs. After

**Before (anchored brainstorming):**
> "Browser security: use sandboxing, add CSP headers, implement extensions permissions…"
> *(All ideas are extensions of the user's existing framing.)*

**After (divergent cross-domain):**
> Browser security + aviation checklists → structured audit protocols for extensions
> Browser security + immunology → adaptive "security memory" that learns attack patterns
> Browser security + mechanism design → resolving the tension between ad revenue and security
> *(Novel connections that wouldn't emerge from topic-only exploration.)*

## Project Structure

```
crossthink/
├── README.md
├── LICENSE
├── SKILL.md
├── examples/
│   ├── browser-security.md
│   ├── music-player.md
│   └── startup-ideas.md
└── images/
    └── workflow.png
```

## License

This project is licensed under the GNU General Public License v3.0. See [LICENSE](LICENSE) for details.

## Contributing

Contributions welcome. To add a new example, place it in `examples/` with a descriptive filename. To improve the methodology, edit `SKILL.md`.