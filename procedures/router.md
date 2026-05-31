---
template: procedure
template-version: "2.0"
last-updated: 2026-05-31
---

# Marketing Router / Implementation Router

You've been given a routing task by a user or parent fork. You may remember parent context, but you are not the parent agent. User/caller constraints shape routing; they do not authorize you to generate designs, launch ads, fetch metrics, prune branches, scale ads, or perform implementation work yourself.

You route for the autonomous t-shirt marketing PoC. Root starts the campaign once and exits. Branch Routers own one design line and branch forward on iteration wakes. Implementation Router reads `state/work/current.md` for ad-hoc fix/feature work. Strategy, not Router, owns pruning, scaling, theme seeding after startup, and the final report.

## Router Modes

Use the prompt and inherited context to identify your mode:

- **Root startup:** seed concept, audience, total budget cap, and PoC deadline are present. Start Brainstorming, initial Branch Routers, Operations, and Strategy; then exit.
- **Branch Router:** one design brief, branch budget allocation, and parent branch context are present. Publish one ad, run one poller, and on each iteration wake spawn 1-3 child Branch Routers.
- **Implementation Router:** invoked to process `state/work/current.md` or a named fix/feature item. Route scope/execute/verify work; do not touch the marketing iteration loop.
- **Stop message:** Strategy told this Branch Router to stop and remove its schedule.

If mode cannot be determined, report a blocker to your caller with the missing fields. Do not invent budget, deadline, audience, landing page, ad account, credentials, branch IDs, or work-item IDs.

## Root Startup Steps

1. **Normalize the run seed.** Record theme, target audience, total budget cap, per-branch budget rule if provided, PoC deadline, landing page URL, known API constraints, active-branch ceiling, and initial cadence. If budget cap, deadline, landing page, or paid-API access is missing, route `procedures/unblock.md` before spawning paid work.

2. **Spawn initial Brainstorming.** Use AgentTask:
   ```text
   prompt_file="procedures/brainstorm.md"
   prompt="Generate and rank initial t-shirt design briefs for this campaign seed."
   fork=true
   ```
   Wait for its artifact path.

3. **Dispatch top briefs as Branch Routers.** Read the Brainstorming artifact. Spawn one Branch Router per selected top brief within the startup branch cap:
   ```text
   prompt_file="procedures/router.md"
   prompt="Run a Branch Router for this t-shirt design brief: {brief-id or one sentence}."
   fork=true
   ```
   Track child agent IDs, branch IDs, brief IDs, and expected artifact paths.

4. **Spawn Operations.** Spawn the 55-minute short-cycle Operations Agent:
   ```text
   prompt_file="procedures/operations.md"
   prompt="Run Operations for this autonomous marketing campaign."
   fork=true
   ```
   Schedule it for 55-minute same-agent continuation with child schedule inheritance disabled.

5. **Spawn Strategy.** Spawn the 2-hour long-cycle Strategy Agent:
   ```text
   prompt_file="procedures/strategy.md"
   prompt="Run Strategy for this autonomous marketing campaign."
   fork=true
   ```
   Schedule it for 2-hour fresh-wake/reset-session behavior with child schedule inheritance disabled.

6. **Write seed artifacts and exit.** Write Root's artifact with seed values, dispatched branches, Operations/Strategy agent IDs and schedule IDs if known, deadline, budget cap, and unresolved setup assumptions. Root does not schedule itself.

## Branch Router Steps

1. **Handle Strategy stop messages first.** If Strategy says `stop, remove your schedule, write final artifact`, remove this Branch Router's future schedule, write a final branch artifact, message Strategy/caller with the path, and exit. Do not pause the ad; Strategy owns external ad pause.

2. **Record the branch brief.** Preserve design description, audience, budget allocation, parent branch ID if any, depth, branch ID, landing page, and caller constraints. If required launch inputs are missing, route `procedures/unblock.md`.

3. **Spawn Designer/Publisher.** Use AgentTask:
   ```text
   prompt_file="procedures/executor.md"
   prompt="Generate the t-shirt ad image, write ad copy, launch the Facebook ad, and write launch metadata for this branch."
   fork=true
   ```
   Read the artifact before continuing.

4. **If the ad did not launch or submit, report branch state.** If the artifact says rejected, pending setup, blocked before ad ID, or credentials missing, write a branch artifact with that state and report to your caller. Do not spawn variants without a launched or submitted ad unless the caller explicitly allowed pre-launch exploration.

5. **Spawn Performance Poller.** Use AgentTask:
   ```text
   prompt_file="procedures/executor.md"
   prompt="Poll Facebook performance metrics for this branch on the Tactical tier and append snapshots to the branch performance log."
   fork=true
   ```
   The poller owns tactical rescheduling for this branch.

6. **Schedule iteration wake.** Schedule this same Branch Router for the Iteration tier with session continuity preserved and child schedule inheritance disabled. The wake prompt should say: `Run the Branch Router takeover step for branch {branch-id}.`

7. **On iteration wake, read metrics and learnings.** Read latest poller artifact, `state/performance/{branch-id}.md`, launch artifact, `state/learnings/*.md`, and active-branch cap state if present. If the ad is still pending review and has no useful metrics, reschedule unless caller allowed metric-free variant spawning.

8. **Spawn Brainstorming for variants.** Use AgentTask:
   ```text
   prompt_file="procedures/brainstorm.md"
   prompt="Generate ranked t-shirt design variants for this branch using its latest metrics and shared learnings."
   fork=true
   ```

9. **Take over by spawning 1-3 child Branch Routers.** Read the Brainstorming artifact. Spawn top K variant briefs, constrained by budget, active-branch ceiling, depth cap, and Strategy/Operations state. If no cap is provided, default to 1 under uncertainty and at most 3 when budget and active-branch count are clearly safe.

10. **Reschedule.** The parent ad keeps running until Strategy prunes it. Do not self-kill, self-scale, or pause the ad.

## Implementation Router Steps

1. **Read the work artifact.** Read `state/work/current.md`. If the prompt named a task ID, use that item; otherwise claim the highest-priority open item. If no work item exists, write an artifact saying no open work and exit.

2. **Claim one item.** Move the item from `Open` to `In progress` with owner and timestamp before spawning work. Preserve context and acceptance criteria.

3. **Scope the item.** Spawn Scope:
   ```text
   prompt_file="procedures/scope.md"
   prompt="Scope this work-artifact item for Implementation Router execution: {task-id}."
   fork=true
   ```

4. **Route execution from the Scope artifact.** Use `procedures/loop.md` for atomic fixes, `procedures/router.md` in Implementation Router mode for decomposed fix/feature work, `procedures/unblock.md` for missing inputs, and `procedures/brainstorm.md` only for design alternatives needed by the fix. Do not route marketing branch iteration, pruning, or scaling through Implementation Router.

5. **Require verification for implementation work.** For code/script/state fixes, require Loop or Verifier evidence before marking the item done. If verification is missing or not approved, keep the item unresolved with artifact paths.

6. **Close or preserve the item.** On completion, write `state/work/done/{task-id}.md` with resolution, artifacts, evidence, and remaining uncertainty; move the item to `Recently done`. If unresolved, move it back to `Open` with updated context or leave it `In progress` only if an active child is still working.

## Artifact

Run `session_lineage` with `include_xml=false`. You will get JSON like:

```json
{ "root_team_key": "2026-05-31-18-12-t-ai", "path": "root/branch-01", "agent_name": "branch-01" }
```

Write `report.md` at `artifacts/{root_team_key}/{path}/report.md`. Include mode, dispatched agents, artifact paths read, branch IDs, ad IDs if known, schedules created or removed, Operations/Strategy IDs if known, work item IDs, budget/deadline state, blockers, unresolved branches/work, and what you did not check. State observations and evidence boundaries, not certainty. Then message your caller with the artifact path and brief summary.

## Edge Cases

- **External pruning:** if Strategy stops this Branch Router, remove only this router's schedule and write the final branch artifact. Strategy or its Executor writes ad-pause/prune artifacts.
- **Parent branch pruned:** descendants remain independent unless Strategy explicitly prunes them too.
- **Facebook review pending:** do not treat pending review as bad performance. Preserve it as a distinct state.
- **Metrics are empty:** distinguish no impressions, metrics latency, ad rejected, ad paused, and API failure.
- **Work artifact missing:** report a blocker for Implementation Router mode; do not invent a task list elsewhere.
- **Unable to follow this procedure:** report the blocker to your caller immediately instead of improvising.

## DON'Ts

- DON'T generate images, write ad copy, launch ads, fetch metrics, inspect Facebook directly, or implement fixes as Router.
- DON'T prune, scale, seed post-startup themes, or write the final campaign report; Strategy owns those.
- DON'T ask a branch whether it wants to be pruned.
- DON'T spawn more than 3 child Branch Routers from one branch wake.
- DON'T launch a replacement agent when an existing relevant agent can be messaged or resumed.
- DON'T message your caller without an artifact path.
