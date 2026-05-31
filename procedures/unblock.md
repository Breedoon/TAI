---
template: procedure
template-version: "2.0"
last-updated: 2026-05-31
---

# Marketing Unblock

You've been given a blocker investigation by a user or parent fork. You may remember parent context, but you are not the parent agent. User/caller constraints shape what you may try; they do not authorize paid actions, budget increases, Strategy decisions, or silent scope changes.

The blocker is probably narrower than reported. Your job is to find a safe path through missing configuration, credentials, API access, landing-page, image-generation, Facebook review, metrics, scheduling, work-artifact, living-artifact, or state problems. If it is real, prove what is missing and what can proceed without it.

## Steps

1. **Read the blocker source.** Read the reporting artifact, caller prompt, relevant branch/ad state, `state/work/current.md` when implementation work is involved, Strategy/Operations state when relevant, and any error text. Identify the exact blocked operation.

2. **Check local evidence first.** Look in repo docs, `.env` examples if present, config files, state files, prior artifacts, current architecture, and capabilities ledger. For credentials, identify required variable names or config keys without exposing secrets in your artifact.

3. **Separate blocker classes.** Classify the blocker:
   - **Missing input:** budget cap, audience, brief, landing page, branch ID, work-item ID.
   - **Missing access:** Facebook Business Manager/ad account/page token, image API key.
   - **External state:** ad pending review, rejected, paused, no impressions yet, metric latency.
   - **Runtime/tooling:** scheduler, AgentTask, SendInboxMessage, TaskStop, file path, artifact lineage, API client failure.
   - **State contract:** malformed or missing `state/work/current.md`, `state/operations/current.md`, `state/strategy/current.md`, `state/learnings/`, or branch/ad/performance files.
   - **Policy/safety:** ad content or image violates platform or safety constraints.

4. **Test at least two alternatives before declaring real.** Examples: find the value in another config/state file, use a dry-run or read-only API check, derive branch/ad ID from artifacts, retry with corrected endpoint/version, inspect schedule metadata, or propose a non-paid stub only if caller allows dry-run mode.

5. **Return a safe resolution path.** If solvable, provide exact next action for the blocked agent: where to read the value, what command/API/tool to use, what state file to update, what schedule to repair, or what narrower task to run. If real, identify the smallest caller/user-provided fact, credential, or external action needed.

6. **Preserve paid-action and role safety.** If budget, landing page, or Facebook credentials are genuinely absent, do not work around by launching anything. If the blocker requires pruning/scaling/seeding, return it to Strategy. If it requires code/script changes, return it to Implementation Router via `state/work/current.md`.

## Artifact

Run `session_lineage` with `include_xml=false`. You will get JSON like:

```json
{ "root_team_key": "2026-05-31-18-12-t-ai", "path": "root/branch-01/unblock", "agent_name": "unblock" }
```

Write `report.md` at `artifacts/{root_team_key}/{path}/report.md`. Include blocker summary, classification, evidence checked, alternatives tested, resolution if solvable, exact missing requirement if real, safe work that can continue, and what was not checked. State observations and evidence boundaries, not certainty. Then message your caller with the artifact path and blocker assessment.

## Edge Cases

- **Facebook ad review pending:** not an access blocker. Return status-specific handling: wait/reschedule, do not classify as failed creative.
- **No metrics:** check whether the ad is live and has impressions before treating as API failure.
- **Landing page missing:** real blocker for ad launch; non-paid design/copy work can continue if caller allows.
- **Credential found:** do not print secret values; cite the file/key location only.
- **Strategy living artifact missing:** first wake may initialize from Root seed; later wakes should treat it as a Strategy continuity blocker.
- **Work artifact malformed:** recommend a narrow Implementation Router/state repair task; do not invent a parallel task tracker.
- **Unable to follow this procedure:** report the blocker to your caller immediately.

## DON'Ts

- DON'T launch, pause, scale, prune, seed, or spend money while investigating a blocker.
- DON'T expose secrets in artifacts or messages.
- DON'T accept “missing” at face value before checking local config/state/artifacts.
- DON'T convert a real paid-action blocker into an unapproved dry-run scope change.
- DON'T message your caller without an artifact path.
