---
name: crossthink
description: Forces exploration from multiple independent thinking directions before producing conclusions. Uses a five-phase workflow (primary exploration, independent domain exploration, cross-pollination, challenge, synthesis) to avoid tunnel vision, anchoring bias, and superficial ideation. Triggers on "brainstorm", "explore this topic", "think about", "discuss from multiple angles", or when the user explicitly requests divergent thinking.
version: 1.0.0
author: opencode
domain: General-purpose creative reasoning
tags: brainstorming, divergent-thinking, cross-domain, anti-anchoring, creative-exploration
related_skills:
  - idea-refine
  - doubt-driven-development
---

# Divergent Cross-Domain Brainstorming

A structured reasoning methodology that prevents tunnel vision by forcing independent exploration of the user's topic and distant domains before synthesizing conclusions.

## Purpose

Standard brainstorming tends to amplify the user's existing assumptions, anchors too quickly on the first framing, and produces variations on a single theme. This skill addresses those failures by:

- **Separating exploration from connection.** Ideas are generated independently before any forced cross-domain mapping.
- **Forcing cognitive detachment.** The agent must genuinely leave the original topic and explore unrelated domains with equal depth.
- **Challenging before concluding.** Every generated idea is stress-tested for realism, evidence, and hidden assumptions before the final synthesis.
- **Maximizing novelty.** Cross-domain concepts emerge from structural similarity, not superficial analogy.

This skill works for technical, scientific, creative, business, and philosophical topics without modification.

## Activation Criteria

Invoke this skill when:

- The user provides an open-ended topic and requests exploration or discussion
- The user says "brainstorm", "think about", "discuss", "explore", or "consider"
- The topic has significant ambiguity or multiple possible directions
- The user explicitly asks for "multiple perspectives" or "different angles"
- A problem seems stuck and needs fresh thinking
- The user asks "what if" or "is there another way"

Do NOT invoke for:
- Simple factual lookups
- Code reviews or debugging sessions
- Topics where the correct answer is well-established and narrow
- When the user explicitly asks for a single focused analysis

## Instructions

When this skill is active, follow the five-phase workflow below. Each phase has explicit entry criteria, internal process, and exit criteria. Do not skip or shortcut phases.

### Phase 1 — Primary Exploration

**Entry criteria:** User has provided a topic.

**Process:**

1. Restate the topic as a clear question or problem statement. Write it down explicitly.
2. Explore the topic with depth, generating ideas across these dimensions (not all of them every time — select what's relevant):
   - **Fundamentals:** What are the core principles, definitions, or first principles underlying this topic?
   - **Assumptions:** What assumptions are baked into how most people think about this? Which are worth questioning?
   - **Applications:** Where is this currently applied or could it be applied?
   - **Strengths:** What makes this topic powerful, effective, or valuable?
   - **Weaknesses:** Where does it fail, break down, or underperform?
   - **Opportunities:** What gaps, needs, or unmet demands exist?
   - **Limitations:** What are the hard constraints (physical, economic, regulatory, cultural)?
   - **Related technologies:** What adjacent technologies, methods, or fields exist?
   - **Alternative approaches:** What other ways could the same problem be solved?
   - **Unanswered questions:** What remains unknown or debated?
   - **Future possibilities:** What could change that would transform this topic?
3. Continue exploring until the agent determines that additional exploration produces diminishing returns. The stopping criterion is: *new ideas are becoming restatements or trivially related variations of prior ideas rather than genuinely new lines of thought.*
4. Explicitly record the stopping decision and the reason for it.

**Exit criteria:**
- The topic has been explored across at least 5 distinct dimensions
- A clear set of primary ideas has been generated
- The agent has explicitly identified when exploration is saturating
- Diminishing returns have been detected and the agent has reasoned about why

### Phase 2 — Independent Exploration

**Entry criteria:** Phase 1 is complete and primary ideas are recorded.

**Process:**

1. **Completely detach.** Do not look at the primary exploration notes from Phase 1 while selecting domains and exploring them. This is critical — the goal is genuine independence.
2. Select 2–4 distant domains that maximize cognitive diversity. The selection should prioritize *distance from the original topic*, not relevance. Good candidates include:
   - Reverse engineering
   - Biology / ecology / evolution
   - Operating systems and kernels
   - Economics and game theory
   - Psychology and cognitive science
   - Architecture and urban planning
   - Aviation and aerospace engineering
   - Manufacturing and supply chains
   - Distributed systems and consensus protocols
   - History and historiography
   - Mathematics and pure logic
   - Game design and ludology
   - Linguistics and semantics
   - Education and pedagogy
   - Music theory and composition
   - Cryptography and information theory
   - Agriculture and permaculture
   - Criminal justice and forensic analysis
   - Fashion design
3. For each selected domain:
   - Explore it naturally and deeply, generating ideas about how that domain operates, what principles it follows, what constraints it faces, and what solutions it has developed
   - Apply the same depth criteria as Phase 1 — continue until diminishing returns
   - Record all ideas without attempting to connect them to the original topic
4. The total number of domains explored should be proportional to the complexity of the original topic. Simple topics get 2 domains; complex topics get 3–4.

**Exit criteria:**
- At least 2 domains have been explored independently
- Ideas from each domain are recorded without cross-referencing Phase 1
- The agent has genuinely detached from the original topic's framing

### Phase 3 — Cross-Pollination

**Entry criteria:** Phases 1 and 2 are both complete with recorded ideas.

**Process:**

1. Now review both sets of ideas together.
2. Search for relationships using these lenses:
   - **Shared patterns:** Where do the structures, dynamics, or constraints look similar across domains?
   - **Transferable ideas:** Which solutions, methods, or principles from one domain could be applied to the other?
   - **Analogous systems:** Where do the underlying mechanics mirror each other? (Not surface-level similarity — structural similarity.)
   - **Hidden assumptions:** Where does the original topic carry an assumption that a distant domain doesn't share? What if that assumption were wrong?
   - **Cross-domain principles:** What principles appear to be universal across multiple domains?
   - **Surprising similarities:** What connections are unexpected but structurally sound?
   - **Unconventional applications:** How could a distant domain's approach be applied to the original topic in a way no one has tried?
3. For each cross-pollination insight found, record:
   - The source domain and idea
   - The target domain (original topic or another distant domain)
   - The type of relationship (pattern, analogy, transfer, challenge)
   - Why this connection is non-obvious
4. Resist the urge to force connections. If no genuine relationship exists between a distant domain and the original topic, note that and move on. Not every domain needs to connect.

**Exit criteria:**
- At least one non-obvious cross-domain connection has been identified
- Each connection is documented with its source, target, and type
- The agent has distinguished between structural analogies and superficial similarities

### Phase 4 — Challenge

**Entry criteria:** Cross-pollination insights are recorded.

**Process:**

1. Take every major idea generated across Phases 1–3 and subject it to the following challenges:
   - **Realism:** Is this actually achievable given known constraints (technological, economic, temporal, social)?
   - **Assumption validity:** What assumptions does this idea depend on? Are any of them likely wrong? How would you test them?
   - **Evidence:** Is there any evidence that supports or contradicts this idea? What would count as evidence?
   - **Prior art:** Has anything like this already been attempted? What happened? Why did it succeed or fail?
   - **Failure modes:** How could this idea fail? What are the specific failure scenarios?
   - **Opportunity cost:** What is this idea trading off against? What would we sacrifice to pursue it?
2. After challenging, classify each idea:
   - **Strong:** Survives all challenges, has supporting evidence or reasoning, and generates novel value
   - **Moderate:** Has merit but carries significant risk, unsupported assumptions, or limited novelty
   - **Weak:** Fails one or more challenges, rests on flawed assumptions, or is a trivial variation
3. Discard weak ideas explicitly. Do not carry them forward. Note why they were discarded.
4. For moderate and strong ideas, identify what would need to be true for each to work. These are the critical assumptions.

**Exit criteria:**
- Every major idea has been challenged against at least 4 of the 6 challenge dimensions
- Ideas are classified as Strong, Moderate, or Weak with written justification
- Weak ideas are explicitly discarded with documented reasons
- Critical assumptions for remaining ideas are identified

### Phase 5 — Final Synthesis

**Entry criteria:** Challenged and classified ideas from Phase 4.

**Process:**

Produce a structured output containing:

1. **Strongest insights:** The top 3–5 ideas that survived Phase 4, with explanation of why each survived.
2. **Unexpected cross-domain connections:** The most surprising and structurally sound analogies from Phase 3, explained in terms of *why* the connection works (not just that it does).
3. **Novel concepts:** Any ideas that are genuinely new — not restatements of the original topic or obvious extensions.
4. **Practical recommendations:** Actionable next steps for the strongest ideas. Be specific about what to do, not just "explore further."
5. **Open research questions:** Questions that remain unanswered and would need investigation to move forward.
6. **Promising future directions:** Where the topic could go if resources were unlimited and constraints were relaxed.
7. **Why each major insight emerged:** For each of the top insights, explain the reasoning chain that produced it — which phases contributed, which domains fed into it, and what challenge it survived.

**Exit criteria:**
- All 6 synthesis elements are present
- Each major insight has an explicit provenance explanation
- The synthesis is self-contained — someone reading it without prior context can understand the full reasoning

## Internal Reasoning Workflow

The agent should maintain a structured reasoning log throughout execution. This log is not part of the final output but guides the agent's decisions. At each phase transition, the agent should write a brief transition note:

```
[TRANSITION] Phase N → Phase N+1
Reason: <why this phase is complete>
Key findings: <summary of what was discovered>
Confidence: <high/medium/low that this phase is sufficiently complete>
```

This prevents premature phase transitions and makes the reasoning reproducible.

## Best Practices

- **Separate exploration from evaluation.** Never judge ideas during Phase 1 or Phase 2. Evaluation happens only in Phase 4.
- **Resist the urge to connect too early.** Premature cross-linking creates forced analogies that add no value.
- **Prioritize depth over breadth.** Deep exploration of 3 dimensions beats shallow coverage of 10.
- **Be honest about diminishing returns.** The stopping criterion is reasoning quality, not quantity. When new ideas are just variations, stop.
- **Choose domains for distance, not relevance.** The most productive cross-pollination comes from domains that are structurally different, not topically similar.
- **Question the question.** Before exploring the topic, consider whether the user's framing is the right one. Sometimes the most valuable insight is reframing the problem itself.
- **Document assumptions explicitly.** Unstated assumptions are the primary source of weak ideas.
- **Maintain a "not yet" list.** Ideas that are not ready to connect yet should be recorded so they can be revisited in Phase 3 rather than forgotten.
- **Work in phases, not loops.** Do not cycle back and forth between phases. Complete each phase fully before starting the next. This prevents the agent from collapsing the work into a single muddled pass.

## Failure Cases

| Failure | Symptom | Prevention |
|---------|---------|------------|
| Shallow exploration | Ideas are generic, surface-level, or obvious | Force the agent to go at least 3 levels deep on each dimension before stopping |
| Anchoring bias | All ideas trace back to the user's original framing | Phase 2 must be done without referencing Phase 1 notes |
| Forced cross-domain connections | Stage 3 produces obvious analogies (e.g., "biology is like a market") | Require structural similarity, not surface similarity. Reject analogies that work at only one level of abstraction |
| Premature synthesis | The agent starts connecting ideas before both explorations are complete | Enforce the phase order strictly. No skipping ahead. |
| Yes-machining | Weak ideas are not challenged and carry forward | Phase 4 must explicitly discard weak ideas with written justification |
| Domain selection bias | All chosen domains are related to the topic (e.g., "browser security" + "network security" + "cryptography") | Domains must be at least 2 cognitive hops away from the topic. If the topic is technical, pick a domain from humanities or natural science. |
| Quantity over quality | The agent generates 20+ ideas and tries to process them all | Set explicit limits per phase (5–8 primary ideas per domain). Quality over quantity is a hard constraint, not a guideline. |
| Missing provenance | Synthesis insights are presented without explaining where they came from | Every insight in Phase 5 must reference which phase(s) and domain(s) produced it |

## Guardrails

1. **Never force a connection.** If Phase 3 finds no genuine relationship between a distant domain and the original topic, that is a valid outcome. Not every domain needs to connect.
2. **Never evaluate during exploration.** Phases 1 and 2 are generative-only phases. Evaluation begins only in Phase 4.
3. **Never produce a synthesis without challenging.** Phase 4 is mandatory. Skipping it produces uncritical, low-quality output.
4. **Respect diminishing returns.** If the agent detects that additional exploration is producing trivially related variations, it must stop and record that decision.
5. **Maintain domain independence.** Phase 2 exploration must be genuinely independent. The agent should not carry Phase 1 assumptions into Phase 2.
6. **Be specific about reasoning.** Vague statements like "this is interesting" are not sufficient. Every claim must be supported by a reasoning chain.
7. **Don't over-extend.** If the topic is simple, fewer domains and fewer dimensions are appropriate. Three dimensions explored deeply is better than ten explored shallowly.

## Example Conversations

### Example 1: Technical topic

**User:** "Discuss browser security"

**Agent (Phase 1):** Explores browser security across fundamentals (same-origin policy, sandboxing, CSP), assumptions ("all XSS is bad"), strengths (auto-update, sandbox isolation), weaknesses (extension supply chain, cookie theft), opportunities (AI-based threat detection), limitations (backward compatibility, user behavior), related technologies (WebAssembly memory safety, Content Security Policy), alternative approaches (capability-based security, per-site containers), unanswered questions (can we trust rendered content?), future possibilities (verified execution environments, hardware-backed attestation). Stops when new ideas are only variations of existing lines.

**Agent (Phase 2 — selected domains: biology/immune system, aviation safety checklists, economics/mechanism design):** Explores each domain deeply without referencing browser security. Biology: immune system layers, innate vs adaptive response, autoimmunity as failure mode. Aviation: checklist culture, redundant verification, crew resource management. Economics: incentive alignment, principal-agent problems, information asymmetry.

**Agent (Phase 3):** Finds that browser security's layered defense mirrors the immune system's innate/adaptive split. That aviation's checklist culture maps to browser extension review processes. That mechanism design's incentive alignment could address the ad-supported browser business model where security conflicts with revenue.

**Agent (Phase 4):** Challenges each connection. The immune system analogy is strong structurally (layered, adaptive, memory) but weak in practice (browsers don't have "vaccination" mechanisms). The aviation checklist insight is strong and actionable — browser security audits could adopt structured checklists like FAA does. The mechanism design idea is weak because it conflates different incentive structures.

**Agent (Phase 5):** Synthesizes strongest insights: (1) Browser extension review should adopt aviation-style structured checklists, (2) The immune system model suggests a "security memory" layer that remembers and adapts to repeated attack patterns, (3) The mechanism design challenge reveals an unaddressed tension in the ad-supported browser model.

### Example 2: Creative topic

**User:** "Design a music player"

**Agent (Phase 1):** Explores fundamentals (audio playback, user experience), assumptions ("music players need playlists"), strengths (familiarity, large catalog), weaknesses (discovery fatigue, passive listening), applications (mobile, car, smart home), alternatives (voice-controlled, ambient, generative). Generates 7 primary ideas.

**Agent (Phase 2 — selected domains: architecture/spatial design, education/learning theory, manufacturing/assembly line):** Explores each independently. Architecture: flow, wayfinding, focal points, spatial hierarchy. Education: scaffolding, zone of proximal development, spaced repetition. Manufacturing: throughput, bottleneck identification, quality gates.

**Agent (Phase 3):** Architecture's spatial flow maps to playlist navigation — users lose their way in large libraries the same way they get lost in large buildings. Education's scaffolding maps to adaptive difficulty in listening — start with the familiar, gradually introduce novelty. Manufacturing's bottleneck analysis maps to the "skip" pattern — why do users skip tracks and where is the friction?

**Agent (Phase 4):** The architecture insight is strong — spatial wayfinding is a solved problem in UI design that music players ignore. The education insight is moderate — adaptive playlists exist but scaffolding is more about learning than listening. The manufacturing insight is weak — "bottleneck" is too abstract a concept to map to track skipping without more specificity.

**Agent (Phase 5):** Synthesis: A music player that uses spatial design principles for library navigation (visual map of the collection), with a "scaffolded discovery" mode that introduces new music gradually based on listening history, treating the collection as a place to navigate rather than a list to search.

## Example Outputs

### Full Output Template

```markdown
# Divergent Cross-Domain Brainstorm: [Topic]

## Topic Restatement
[Clear problem or question statement from Phase 1]

---

## Phase 1: Primary Exploration

### Dimensions Explored
- [List of dimensions covered]

### Key Ideas
1. [Idea with brief reasoning]
2. [Idea with brief reasoning]
...

### Exploration Saturation
[Why exploration stopped — diminishing returns detected at which dimension]

---

## Phase 2: Independent Domain Exploration

### Domains Selected
- [Domain 1] — rationale for selection
- [Domain 2] — rationale for selection
- [Domain N] — rationale for selection

### Domain 1: [Name]
[Key ideas from this domain, recorded independently]

### Domain 2: [Name]
[Key ideas from this domain]

### Domain N: [Name]
[Key ideas from this domain]

---

## Phase 3: Cross-Pollination

### Connections Found
1. **Source:** [Domain/idea] → **Target:** [Original topic/domain]
   **Type:** [Pattern/Analogy/Transfer/Challenge]
   **Why it works:** [Structural explanation of the connection]
   **Novelty:** [Why this connection is non-obvious]

2. [...]

### Connections Rejected
- [Idea: why it was rejected — superficial similarity, forcing, or no structural alignment]

---

## Phase 4: Challenge

### Strong Ideas
- [Idea] — survived challenges because [reasoning]
- [Idea] — survived challenges because [reasoning]

### Moderate Ideas
- [Idea] — carries risk because [assumption that may be wrong or challenge it faces]

### Weak Ideas (Discarded)
- [Idea] — discarded because [specific challenge it failed]

### Critical Assumptions
- [Assumption 1] — how to test it
- [Assumption 2] — how to test it

---

## Phase 5: Final Synthesis

### Strongest Insights
1. **[Insight title]** — [Description]
   *Why it emerged:* [Which phases and domains contributed, and what challenge it survived]

2. [...]

### Unexpected Cross-Domain Connections
1. [Connection] — [Why it's structurally sound, not just analogous]

### Novel Concepts
- [Concept that is genuinely new, not a restatement]

### Practical Recommendations
1. [Specific actionable step]
2. [Specific actionable step]

### Open Research Questions
- [Question 1]
- [Question 2]

### Promising Future Directions
- [Direction 1]
- [Direction 2]

---

## Reasoning Log

[TRANSITION] Phase 1 → Phase 2
Reason: [Why primary exploration was complete]
Key findings: [Summary]
Confidence: [high/medium/low]

[TRANSITION] Phase 2 → Phase 3
Reason: [...]
Key findings: [...]
Confidence: [...]

[TRANSITION] Phase 3 → Phase 4
Reason: [...]
Key findings: [...]
Confidence: [...]

[TRANSITION] Phase 4 → Phase 5
Reason: [...]
Key findings: [...]
Confidence: [...]
```

## Evaluation Criteria

Use these criteria to assess whether this skill was executed well:

### Completeness

- [ ] All 5 phases were executed in order, none skipped
- [ ] Phase 1 explored at least 5 dimensions
- [ ] Phase 2 used at least 2 distant domains (at least 1 cognitive hop from the topic)
- [ ] Phase 3 produced at least 1 non-obvious cross-domain connection
- [ ] Phase 4 evaluated every major idea against at least 4 challenge dimensions
- [ ] Phase 5 contains all 6 synthesis elements

### Quality

- [ ] No anchoring bias — ideas in Phase 2 are genuinely independent of Phase 1
- [ ] Cross-domain connections are structural, not superficial
- [ ] Weak ideas were explicitly discarded with written justification
- [ ] Diminishing returns were detected and reasoned about, not arbitrary
- [ ] Every major insight in Phase 5 has a provenance explanation

### Novelty

- [ ] At least one idea exists that would not appear from brainstorming only the original topic
- [ ] Cross-domain connections are non-obvious but structurally sound
- [ ] The synthesis contains concepts that are genuinely new, not remixes of the original idea

### Reproducibility

- [ ] The transition log is complete and documents reasoning at each step
- [ ] An independent agent reading the log could reconstruct the reasoning chain
- [ ] Stopping decisions are documented with explicit reasons

## Suggestions for Future Improvements

1. **Quantitative convergence tracking.** Track the rate of new idea generation per phase and automatically detect saturation with a confidence score rather than relying on agent judgment alone.

2. **Domain distance scoring.** Develop a systematic way to measure cognitive distance between domains (e.g., based on shared conceptual vocabulary) and use it to optimize domain selection in Phase 2 for maximum novelty.

3. **Iterative re-exploration.** Allow the agent to re-enter Phase 1 with the reframed topic from Phase 3 synthesis, creating a second pass that incorporates cross-domain insights into the primary exploration.

4. **Adversarial mode.** Add an optional mode where a second agent or simulated critic is explicitly assigned to find flaws in Phase 4 classifications, providing a double-blind challenge layer.

5. **Domain-specific lenses.** Pre-build domain-specific exploration templates for the most common distant domains (biology, economics, history, etc.) so Phase 2 exploration can leverage structured heuristics rather than relying entirely on the agent's general knowledge.

6. **Integration with codebase context.** When used inside a project, allow Phase 1 and Phase 3 to reference actual code, architecture, and prior art using file search tools, grounding the brainstorming in the project's constraints and opportunities.

7. **Temporal dimension.** Add a "future projection" step to Phase 1 where the agent explores how the topic might evolve over 5, 10, and 50 year horizons, forcing thinking beyond current constraints.

8. **Collaboration mode.** Adapt the workflow for multi-agent use where different agents explore different phases concurrently and then converge, reducing wall-clock time for complex brainstorming sessions.

9. **Confidence-weighted synthesis.** In Phase 5, weight insights by their Phase 4 classification and the number of independent domains that support them, producing a ranked output rather than a flat list.

10. **Persistent state.** Store reasoning logs and intermediate outputs so that long-running brainstorming sessions can be resumed, reviewed, or shared across sessions.