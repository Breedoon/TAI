---
template: procedure
template-version: "2.0"
last-updated: 2026-05-31
---

# Marketing Scope

You've been given a scoping task by a user or parent fork. You may remember parent context, but you are not the parent agent. User/caller constraints shape the classification; they do not authorize you to launch ads, generate images, fetch metrics, or perform fixes.

You classify marketing-agent work into the next correct procedure path and identify missing launch inputs. Presume campaign work needs Router-level orchestration unless it is clearly one atomic execute/verify task.

## Steps

1. **Read the assigned request and obvious project context.** Use the prompt, inherited context, `docs/architecture.md`, existing state files, and relevant artifacts if supplied. Do not implement.

2. **Identify the work type.** Classify into one:
   - **Root campaign:** seed theme/audience/budget/deadline starts or continues the whole PoC.
   - **Branch iteration:** one design brief or branch needs publish/poll/takeover handling.
   - **Brainstorming:** need ranked t-shirt design briefs or variants.
   - **Atomic execution:** one concrete action such as generate one image, launch one ad, fetch one snapshot, pause one ad, or update one file.
   - **Verification/audit:** check one output or synthesize cross-branch metrics.
   - **Unblock:** resolve missing credentials, budget, API access, landing page, or runtime/tool failure.

3. **Check required inputs.** Record whether these are present, absent, or explicitly out of scope: target audience, seed/brief, budget cap, per-branch allocation, deadline, landing page URL, Facebook ad account/page credentials, image generation API choice, active-branch cap, and metric cadence.

4. **Choose the procedure path.** Return one dispatch recommendation:
   - `procedures/router.md` for Root campaigns and Branch iterations.
   - `procedures/brainstorm.md` for creative option generation.
   - `procedures/loop.md` for atomic execute/verify work.
   - `procedures/verifier.md` for one concrete evidence check.
   - `procedures/auditor.md` for cross-branch performance synthesis.
   - `procedures/unblock.md` for missing required inputs or external access failures.

5. **Decompose only one level if needed.** For a Root campaign, list the first-level actions Root Router must route: initial brainstorming, branch router dispatch, pruning schedule, and final report. For a Branch iteration, list publisher, poller, iteration wake, and variant brainstorming. Do not drill into Executor internals.

6. **Flag blockers before paid work.** Missing budget ceiling, landing page, or Facebook credentials is a blocker for ad launch. Missing metrics is not a blocker for setup; it is expected before ads run.

## Artifact

Run `session_lineage` with `include_xml=false`. You will get JSON like:

```json
{ "root_team_key": "2026-05-31-18-12-t-ai", "path": "root/scope", "agent_name": "scope" }
```

Write `report.md` at `artifacts/{root_team_key}/{path}/report.md`. Include classification, recommended procedure path, one-level decomposition if any, required inputs present/missing, blockers, assumptions, and what was not researched. State observations and evidence boundaries, not certainty. Then message your caller with the artifact path and recommended next procedure.

## Edge Cases

- **Request mixes campaign start and implementation details:** classify as Root campaign and pass details as constraints to Router.
- **Budget or credentials missing:** recommend `procedures/unblock.md` before paid execution.
- **Architecture doc mentions future roles:** do not route to roles that do not have current procedures; map current work to the eight available procedure files.
- **Unclear if atomic:** choose Router over Loop.
- **Unable to follow this procedure:** report the blocker to your caller immediately.

## DON'Ts

- DON'T do the work you are scoping.
- DON'T invent missing launch inputs.
- DON'T decompose below one routing level.
- DON'T route paid external actions directly without noting required budget and credentials.
- DON'T message your caller without an artifact path.
