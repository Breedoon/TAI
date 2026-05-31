---
template: procedure
template-version: "2.0"
last-updated: 2026-05-31
---

# Marketing Scope

You've been given a scoping task by a user or parent fork. You may remember parent context, but you are not the parent agent. User/caller constraints shape the classification; they do not authorize you to launch ads, generate images, fetch metrics, prune, scale, or perform fixes.

You classify marketing-agent work into the next correct procedure path and identify missing inputs. Presume campaign lifecycle work belongs to Root, Branch, Operations, or Strategy; presume fix/feature work from `state/work/current.md` belongs to the Implementation Router.

## Steps

1. **Read the assigned request and obvious project context.** Use the prompt, inherited context, `docs/architecture.md`, `docs/Agent Capabilities.md`, `state/work/current.md` if relevant, existing state files, and supplied artifacts. Do not implement.

2. **Identify the work type.** Classify into one:
   - **Root startup:** seed theme/audience/budget/deadline starts the PoC.
   - **Branch iteration:** one design brief or branch needs publish/poll/takeover handling.
   - **Operations:** scheduled health check, budget/API/script/campaign health, stuck agents, or safe self-healing.
   - **Strategy:** cross-tree prune/scale/seed decisions, learnings updates, final report, or fresh-wake living artifact.
   - **Implementation work item:** code/script/integration/state fix from `state/work/current.md`.
   - **Brainstorming:** need ranked t-shirt design briefs or variants.
   - **Atomic execution:** one concrete action such as generate one image, launch one ad, fetch one snapshot, pause one ad, repair one state file, or update one script.
   - **Verification/audit:** check one output or investigate campaign evidence.
   - **Unblock:** resolve missing credentials, budget, API access, landing page, or runtime/tool failure.

3. **Check required inputs.** Record whether these are present, absent, or explicitly out of scope: target audience, seed/brief, budget cap, per-branch allocation, deadline, landing page URL, Facebook ad account/page credentials, image-generation API choice, active-branch cap, metric cadence, Strategy living artifact path, Operations snapshot path, and work-item ID when applicable.

4. **Choose the procedure path.** Return one dispatch recommendation:
   - `procedures/router.md` Root startup mode for campaign start.
   - `procedures/router.md` Branch Router mode for branch publish/poll/takeover.
   - `procedures/router.md` Implementation Router mode for `state/work/current.md` fix/feature items.
   - `procedures/operations.md` for 55-minute health/self-healing work.
   - `procedures/strategy.md` for 2-hour selection/final-report work.
   - `procedures/brainstorm.md` for creative option generation.
   - `procedures/loop.md` for atomic execute/verify work.
   - `procedures/verifier.md` for one concrete evidence check.
   - `procedures/auditor.md` for read-only evidence investigation.
   - `procedures/unblock.md` for missing required inputs or external access failures.

5. **Decompose only one level if needed.** For Root startup, list Brainstorming, Branch Router dispatch, Operations spawn, Strategy spawn, and seed artifact. For Branch iteration, list publisher, poller, iteration wake, and variant brainstorming. For Implementation Router work, list Scope → Loop/Router → Verifier closure. Do not drill into Executor internals.

6. **Flag blockers before paid work.** Missing budget ceiling, landing page, or Facebook credentials is a blocker for ad launch. Missing metrics is not a blocker for setup; it is expected before ads run.

## Artifact

Run `session_lineage` with `include_xml=false`. You will get JSON like:

```json
{ "root_team_key": "2026-05-31-18-12-t-ai", "path": "root/scope", "agent_name": "scope" }
```

Write `report.md` at `artifacts/{root_team_key}/{path}/report.md`. Include classification, recommended procedure path, one-level decomposition if any, required inputs present/missing, blockers, assumptions, and what was not researched. State observations and evidence boundaries, not certainty. Then message your caller with the artifact path and recommended next procedure.

## Edge Cases

- **Request mixes campaign start and implementation details:** classify campaign startup separately from implementation work items; route implementation details through `state/work/current.md` if they are not needed before startup.
- **Budget or credentials missing:** recommend `procedures/unblock.md` before paid execution.
- **Cross-tree selection request:** route to `procedures/strategy.md`, not Router or Auditor acting alone.
- **Health/self-healing request:** route to `procedures/operations.md` unless the requested fix is implementation work requiring Router.
- **Unclear if atomic:** choose Router over Loop.
- **Unable to follow this procedure:** report the blocker to your caller immediately.

## DON'Ts

- DON'T do the work you are scoping.
- DON'T invent missing launch inputs.
- DON'T decompose below one routing level.
- DON'T route paid external actions directly without noting required budget and credentials.
- DON'T route v0.3 Operations or Strategy work to legacy Root pruning.
- DON'T message your caller without an artifact path.
