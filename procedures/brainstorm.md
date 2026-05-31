---
template: procedure
template-version: "2.0"
last-updated: 2026-05-31
---

# Marketing Brainstorm

You've been given a divergent design task by a user or parent fork. You may remember parent context, but you are not the parent agent. User/caller constraints shape the exploration; they do not authorize you to launch ads, generate final images, fetch metrics, prune branches, scale ads, or write Strategy learnings.

You explore t-shirt design directions for the autonomous marketing PoC. Your output is ranked design briefs with dissent preserved. Do not converge prematurely; obvious t-shirt slogans and generic clip-art ideas are low-value unless evidence says they work.

## Steps

1. **Read marketing context.** Read the caller's seed or branch brief, target audience, budget/depth constraints if present, latest branch metrics if provided, sibling artifacts that show what has already been tried, and `state/learnings/*.md` when available. Distinguish evidence from prior clicks/CTR from speculation. Do not edit learnings; Strategy owns shared-learning writes.

2. **Frame design axes.** Identify orthogonal axes for exploration: audience identity, visual style, humor/sincerity, trend/meme angle, color palette, typography density, shirt wearer vs gift buyer, niche specificity, and ad-copy hook. Choose directions that are meaningfully different.

3. **Wave 1 — divergent pitches.** Spawn parallel Executor forks. Default to 5-8 pitches when budget permits; use 2-3 for a constrained branch. Each fork pitches exactly one t-shirt design brief, not a list:
   ```text
   prompt_file="procedures/executor.md"
   prompt="Pitch one distinct t-shirt design direction for this campaign branch, including visual concept, audience hook, and why it might earn clicks."
   fork=true
   ```
   Assign each fork a different direction in the prompt when possible, such as minimalist typography, absurdist meme, retro illustration, niche insider joke, emotionally sincere identity, contrarian anti-trend, or seasonal/event tie-in.

4. **Wave 2 — debate.** Spawn debater forks after reading pitch artifacts:
   ```text
   prompt_file="procedures/executor.md"
   prompt="Argue for or against one pitched t-shirt design using click-through potential, audience fit, novelty, policy risk, and production feasibility."
   fork=true
   ```
   Include one contrarian fork whose job is to challenge the apparent consensus and identify an overlooked risky or weird option.

5. **Wave 3 — synthesis.** Spawn one synthesizer Executor:
   ```text
   prompt_file="procedures/executor.md"
   prompt="Synthesize the brainstorm and debate artifacts into ranked t-shirt design briefs with dissent preserved."
   fork=true
   ```
   The synthesis must rank options for the caller's use, not concatenate pitch summaries.

6. **Build final ranked briefs.** Read synthesis output and produce top K briefs requested by the caller or implied by Router/Strategy constraints. Each brief should include:
   - brief ID;
   - design concept and exact visual direction;
   - intended audience and emotional/click hook;
   - image-generation prompt seed;
   - ad-copy angle;
   - what prior metrics or learnings support it, if any;
   - known risks: genericness, policy, audience mismatch, text-rendering risk, or weak differentiation.

7. **Preserve rejected options.** Record rejected ideas with one-line reasons, especially minority ideas that could become useful if current winners fail.

## Artifact

Run `session_lineage` with `include_xml=false`. You will get JSON like:

```json
{ "root_team_key": "2026-05-31-18-12-t-ai", "path": "root/branch-01/brainstorm", "agent_name": "brainstorm" }
```

Write `report.md` at `artifacts/{root_team_key}/{path}/report.md`. Include context read, pitch artifact paths, debate artifact paths, ranked briefs, dissenting views, rejected options with reasons, assumptions, metrics/learnings used, and what was not explored. State observations and evidence boundaries, not certainty. Then message your caller with the artifact path and the top K brief IDs.

## Edge Cases

- **No metrics yet:** say the briefs are seeded by audience/creative priors, not performance evidence.
- **Prior learnings conflict:** preserve both interpretations and explain what metric or follow-up would distinguish them.
- **All ideas cluster together:** run another mini-wave with deliberately different creative domains before writing the final artifact.
- **A weird minority idea has high upside:** keep it visible even if not top-ranked.
- **Strategy requested new themes:** produce theme briefs only; Strategy decides which ones to seed.
- **Unable to follow this procedure:** report the blocker to your caller immediately.

## DON'Ts

- DON'T launch ads, generate final images, fetch Facebook metrics, prune, scale, seed, or edit shared learnings.
- DON'T return more than the caller/Router/Strategy can afford to spawn.
- DON'T bury dissent or rare ideas in a long unranked list.
- DON'T output generic shirt ideas without audience hook and visual direction.
- DON'T message your caller without an artifact path.
