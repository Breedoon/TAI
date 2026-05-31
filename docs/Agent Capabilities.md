# Agent Capabilities

A practical ledger of what AI agents (currently Claude/Opus 4.6) can and cannot do reliably. Use this when designing workflows, procedures, and enforcement mechanisms.

This is a living document. Observations are sourced from direct user experience across dozens of sessions and marked accordingly. Inferred items are flagged — the user should review and correct them.

---

## Core Strengths

**Reading and understanding files.** Agents reliably parse markdown, code, configs, and follow cross-references. They understand context, nuance, and can synthesize across multiple files in a single session. Per user observation: "agents are good at reading files, understanding the caveats of what is confirmed and what is not, and understanding references to which files they need to read."

**Surgical single-file edits.** When the scope is one file with a clear objective, agents perform well. Per user observation: agents handle "implementing a feature, like knowing what files to edit" and executing edits reliably.

**Following clear step-by-step procedures.** Agents follow numbered steps with concrete actions (read this file, run this command, spawn this agent). Per user observation: "agents are really good at working on one specific task." Vague instructions fail; specific ones succeed.

**Writing code (especially TypeScript, Python).** Agents produce functional code in well-documented languages. Per user observation (inferred from discussion of coding workflows): TypeScript and Python are strengths; less common languages may be weaker.

**Explaining reasoning when asked.** If prompted to explain WHY they did something, agents give detailed, accurate explanations. Per user observation: "agents are actually quite good at explaining what they did if asked — they default to not explaining unless prompted." This is distinct from proactively explaining, which they don't do.

**Creative brainstorming when explicitly prompted.** Per user observation: "if you ask it to think how to solve this task out of the box, it can come up with something actually. And if you ask a bunch of agents especially of different models to do that, they will very likely come up with a good idea."

**Re-deriving missed requirements on re-read.** Per user observation: "I can just tell it to read the spec again and see what you didn't implement and implement it, and it actually figures out new things." Agents can self-correct when pointed back at source material.

**Infinite parallelism.** Can spawn many agents cheaply for parallel investigation. A problem that takes one agent an hour can often be split across 5 agents working simultaneously. Per user observation (confirmed across dozens of sessions): the user routinely dispatches 4-8 parallel research agents with good results.

**Fresh perspective per agent.** Each newly spawned agent brings unbiased analysis — no anchoring from prior failed approaches or accumulated assumptions. This is a structural advantage of ephemeral agent design.

**Patient exhaustive search.** Agents don't get bored, don't skip files, and can systematically check every path when instructed to. They excel at "check all 26 repos for pattern X" style tasks where a human would lose patience.

**Cross-referencing at scale.** Agents can compare many documents simultaneously and find patterns, contradictions, or gaps across large document sets. Per user observation: this is especially useful for synthesis tasks (reading 5 framework reports and identifying convergent patterns).

---

## Core Weaknesses

**Premature victory declaration.** The #1 observed failure mode. Agents say "done" when work is incomplete or broken. Per user observation: "the biggest thing is agents declaring victory prematurely — they say it's done but it's not actually done, and then I run it myself and it doesn't work." This affects code, documents, reviews, and task completion universally.

- *Practical implication:* Never trust an agent's self-report of completion. Always require external verification (tests passing, user confirmation, adversarial review).

**Default certainty bias.** Agents present inferences as established facts. Per user observation: "the agent's default is to be sure, which is really bad for our purposes." They write "X is the case" when the truth is "X appears to be the case based on one source."

- *Practical implication:* Source citation and uncertainty conventions are the primary defense. Source-grounding rule: every factual claim must cite a source or be flagged as agent inference.

**Dropping side-constraints when focused on main task.** When given a primary task plus side-requirements (cite sources, update changelog, check backlinks), agents do the primary task well and partially or fully drop 2-3 side-constraints. Per user observation: "if an agent needs to follow a lot of rules for how to organize a vault while also performing a task, then it's going to slack on the organization part."

- *Practical implication:* Separate side-constraints into a janitor agent. The main agent does its job; a separate agent handles citations, commits, cross-links. Or enforce via hooks that fire at the point of action.

**Poor architectural decisions in complex codebases.** Agents implementing features make good local decisions but poor system-wide ones. Per user observation: "they're not good at implementing a feature while also not making bad architectural decisions, especially with a more complex codebase."

- *Practical implication:* Multi-agent architecture review before implementation. Separate the "what files to edit" decision from the "how to edit them" execution.

**Poor recall (precision vs recall asymmetry).** Agents are good at precision (identifying whether something they found IS relevant) but bad at recall (thinking of what ELSE might be relevant that they haven't found yet). They're stateless and don't know what they don't know. A human might think "this happened to me two years ago" — an agent has no such memory. Per user observation: "agents are stateless and they wouldn't know... a human would have encountered that and they would have like a glimpse of memory from all this happened to me two years ago but an agent wouldn't."

- *Example:* A customer complaint about being "misled" and another about a "misunderstanding" are conceptually related, but an agent searching for one won't find the other because the terminology differs. A human would connect these — an agent won't unless both show up in the same search.
- *Example:* An agent encountering an error won't recall that a similar error was encountered 6 months ago in a different context, stored under different terms. It keeps rediscovering the same problems.
- *Practical implication:* Neither storage-side nor retrieval-side alone is sufficient. At **storage time**, over-index knowledge broadly (tag with related concepts, link to adjacent topics) so it shows up in more searches. At **retrieval time**, use recursive multi-agent search (more agents looking from more angles = better recall). Both are needed.

**Not checking history/precedent before decisions.** Agents don't naturally search for "have we tried this before?" or "what happened last time?" Per user observation (inferred from discussion of failed-approach records and the need for mandatory git log checks): agents treat absence of information as "never tried" rather than "might have been tried and failed."

- *Practical implication:* Mandatory procedure step: "check git log and KB for prior work on this topic before proceeding." Failed approach records as a convention.

**Declaring things "impossible" too easily.** When stuck, agents conclude "this can't be done" when the truth is "I can't figure out how to do this." Per user observation (inferred from the discussion of adversarial "prove you wrong" steps): agents conflate their own limitations with actual impossibility.

- *Practical implication:* Procedure step: "before declaring impossible, spawn an adversarial agent whose job is to find a solution you missed."

**Testing their own work inadequately.** Agents writing tests tend to write tests that pass rather than tests that verify meaningful behavior. Per user observation: "they're not very good at testing it and making sure it's good."

- *Practical implication:* Separate test-writing from implementation. The implementing agent should not write its own tests. A separate agent writes tests from the original requirements, not from the implementation.

**Coordination is cognitively expensive.** Any agent managing multiple sub-agents is too busy coordinating to do other meaningful work. Reading inboxes, tracking progress, deciding what to do next, synthesizing reports — this IS the job. Per user observation: "any agent that basically is dealing with multiple agents under it can't have any other job. Because that's their job."

- *Practical implication:* Coordinators should be SEPARATE from implementers/verifiers. A coordinator agent's only job is orchestration. If a coordinator also needs to do analysis, fork itself first and delegate the analysis to the fork.

**Naive trust of other agents' output.** Agents are sycophantic by default and will accept other agents' claims of completion without verification. One agent says "I tested it" — the parent agent believes it, even if the testing was superficial or mocked. Per user observation: "agents are very naive and they believe everyone's work."

- *Practical implication:* Adversarial prompting is required — assume the worst, verify independently. Write verification prompts starting from the presumption that the work is NOT good, because "that's most likely going to be the case most of the time."

**Lost intent details during summarization.** When summarizing user requests, agents lose specific details mentioned in passing. A user mentions a performance constraint or a specific testing approach, it doesn't make it into the plan, and it never gets implemented. Per user observation: "I often would say something, like, some detail about the implementation, and then it would just... never makes it and then I realized, like, oh, I said it, but it like never was implemented."

- *Practical implication:* Raw transcript preservation + cross-checking against verbatim user request. Don't rely on agent summaries as the source of truth for user intent.

**Improvisation is unreliable under ambiguity.** Agents can improvise when stuck, but the quality drops sharply when they lack context about how things are expected to be done. Per user observation: "agents are good at improvising, but not great. And ideally they keep that improvisation skill for when it's actually needed." When a procedure doesn't cover a situation, agents guess — and guesses compound when they don't know the full system expectations.

- *Practical implication:* Good procedures should enumerate edge cases and failure modes explicitly: "if X happens, do Y. If this procedure doesn't make sense in context, message your parent or spawn a fork to ask before proceeding." The goal is to reserve improvisation for genuinely novel situations, not routine branching that could have been predicted. This is core engineering: predict failure modes and plan for them.

**Scope underestimation.** Agents assume projects are simpler than they are. Edge cases not considered, questions not asked, external dependencies not identified, performance constraints silently dropped. Per user observation: "so many edge cases were not considered, questions to me were not asked, and the whole project gets, like, not done because planning assumes scope is smaller than it was."

- *Practical implication:* Use concrete scope thresholds (2-3 tasks per plan, 5-8 files per plan). Mandatory scoping step that asks about performance requirements, external dependencies, and risk areas. Adversarial scope review that asks "what did you miss?"

**Defaults to doing work themselves instead of delegating.** When given a complex task, agents default to attempting the work directly rather than decomposing and delegating to sub-agents. Even when a task clearly should be broken down, agents will search, read files, and reason in their own context — cluttering it with intermediate results and degrading their decision-making performance. Per user observation, validated by Claude Code source analysis: CC's "coordinator mode" (pure orchestrator that can ONLY spawn agents) exists specifically to address this pattern.

- *Practical implication:* For governance, consider structurally restricting complex-task agents to ONLY spawning sub-agents — remove execution tools from the API request rather than just prompting "you should delegate." Structural enforcement beats prompt enforcement (confirmed by CC source analysis pattern).

**Giving up before reaching deep information.** When information is several hops away (follow link → read file → follow another link → read section), agents sometimes stop early. Inferred from discussion of agents not tracing through git history, session cards, and JSON-Ls to find root causes.

- *Practical implication:* Procedures should be explicit: "read file X, then follow the link to Y, then check the git history of Y." Don't assume agents will naturally go deep.

**Cannot meaningfully self-evaluate output quality.** Agents cannot assess whether their own work is "substantive," "complete," or "good." When asked "is your output substantive?" they will always say yes — they lack the external perspective needed for genuine self-evaluation. This is distinct from premature victory (which is about declaring "done" too early) — this is about the fundamental inability to judge their own work's quality, even when they genuinely try. Per user observation: self-check steps like "does it contain substantive content?" are "words that don't mean anything — the agents will not follow it."

- *Practical implication:* Never ask an agent to evaluate the quality of its own output. If evaluation matters, spawn a separate agent to do it. The only self-checks that work are concrete and countable: "did I address each item?" (count rows), "did I include what was NOT checked?" (presence/absence). Anything requiring judgment ("is it substantive?") must be delegated.

**Agentic orchestration is not native behavior.** Agents are not trained to spawn sub-agents, follow multi-agent procedures, or manage agent hierarchies. They default to attempting work directly rather than delegating (see "defaults to doing work themselves"). Writing procedures, evaluating procedure quality, and designing agent workflows are especially non-native — agents need explicit, detailed instructions for these tasks. Per user observation: agents writing procedures produce "slop" that sounds procedural but doesn't actually change agent behavior, because they don't have good judgment about what matters in a procedure.

- *Practical implication:* Procedure-writing and agentic orchestration require especially explicit instructions and human review. Agents can draft procedures but cannot evaluate whether they're effective — that requires testing (spawning a fresh agent to follow the procedure and observing behavior) or human judgment. See [[Projects/Personal Projects/Agentic/Agentic Fractals/docs/Design Philosophy/Procedure Writing Guide|Procedure Writing Guide]].

---

## Context & Session Behavior

**Context degradation at high token counts.** Agents at 500K+ tokens of context are measurably less reliable — they miss details, repeat themselves, and make more errors. Inferred from the user's design principle that agents should be ephemeral and the concern about trunk agents accumulating context from sub-agent reports.

- *Practical implication:* Summarize and offload context after receiving each sub-agent report. Don't let trunk agents accumulate raw data.

**Compaction loses nuance.** When context is compacted (approaching the window limit), the compacted version may retain facts but lose uncertainty markers and source citations. A hedged "X might be the case (source: session Z, unverified)" becomes "X is the case" after compaction. Inferred from user's concern about compaction handling.

- *Practical implication:* Persist knowledge to files BEFORE compaction happens. The file is the source of truth, not the agent's in-context memory. Schedule summarization before the compaction threshold.

**Agents cache knowledge in-context and don't re-read.** Once an agent has read a file, it "knows" the content and won't re-read even if the file changed. Per user observation (discussed in context of the read-side problem): the stale-knowledge problem is real but mitigated by keeping agents ephemeral.

- *Practical implication:* Pre-edit hooks that check if referenced files were modified since the agent last read them. Design agents to be short-lived — spawn fresh for each task, read everything fresh.

**Ephemeral agents work better than long-lived ones.** Per user observation: "most agents will be quite ephemeral and they will be spawned for one specific task, maybe they will spawn some sub-agents of theirs, and then will just summarize and perish." Long-lived agents accumulate stale context and degrade.

- *Practical implication:* Default to single-task agents. A complex task becomes many short-lived agents, not one long-running agent.

**Focused executor model.** Agents are good at one task and bad at balancing many concerns simultaneously. Per user observation: "an agent is really good at working on one specific task and not good at doing things out of scope."

- *Practical implication:* Design each agent with a single clear objective. Side-concerns (formatting, compliance, cross-linking) go to separate agents.

---

## Tool-Specific Observations

**Obsidian CLI limitations.** The Obsidian CLI requires ~5-second intervals between commands within the same vault. This is unacceptably slow for multi-agent concurrent work. Per user observation: "it doesn't handle concurrency very well — you have to wait like five seconds or so before running another obsidian command in the same vault."

- *Practical implication:* The agent knowledge base should NOT rely on Obsidian for concurrent operations. Use plain files + lightweight CLI tooling (backlinking, indexing) instead.

**Git operations.** Agents handle git well — committing, branching, blaming, logging. Per user observation (inferred from extensive discussion of git as backbone): git is a reliable tool for agents. The main risk is operating in the wrong repo context (code repo vs. KB repo).

- *Practical implication:* Always use absolute paths for git operations. Enforce commit message format via pre-commit hooks.

**Agent spawning (AgentTask/ForkTask).** Agents do not default to effectively use the platform's agent spawning tools like AgentTask.

- *Practical implication:* Agents need to be told explicitly to spawn sub-agents, they won't do it out of their own initiative.

---

## Workflow Patterns

**Adversarial sub-agents improve quality.** Spawning a second agent specifically to critique the first agent's work catches real issues. Per user observation: "the go-to thing that makes agents perform better is having sub-agents and sub-agents having sub-sub-agents." The key is adversarial framing — "find problems" rather than "review this."

- *Practical implication:* Adversarial agents should be framed negatively: "find at least N issues," "argue why this is wrong," "try to break it."

**"Report at least N issues" creates a ceiling.** When told to find at least 3 issues, agents find exactly 3 — including easy ones — and stop. They treat the minimum as a target. Inferred from the behavior analysis discussion.

- *Practical implication:* Use "report ALL issues you find" with a minimum ("you must find at least 3, but don't stop there"). Or use multiple specialist agents with different angles rather than one generalist.

**Agents respond to in-context reminders (hooks) but develop fatigue with generic ones.** Specific hooks ("you just wrote '24.8% efficiency' — where did you get this number?") are much more effective than generic ones ("remember to cite your sources"). Per user observation: "agents are surprisingly responsive to in-context reminders even if they ignore rules they read 50,000 tokens ago."

- *Practical implication:* Hooks that fire at the moment of action are more effective than rules read at session start. Content-specific hooks > generic reminders. But overuse causes agents to treat them as noise.

**Agents can re-derive requirements from specs.** Per user observation: "I can just tell it to read the spec again and see what you didn't implement and implement it." Agents respond well to being pointed back at source material with "what did you miss?"

- *Practical implication:* When an agent's work is incomplete, don't enumerate what's missing — tell it to re-read the requirements and find the gaps itself. This is more reliable than listing specific issues (which the agent may misinterpret).

**Multi-agent brainstorming with voting works.** Per user observation: when you need a creative decision, "have a bunch of agents especially of different models brainstorm and then pretty much deterministically report their decision — here's our recommendation, this is our voting result."

- *Practical implication:* For significant decisions, spawn 3+ agents with different perspectives, have them propose independently, then synthesize. Don't rely on a single agent's judgment.

**Agents improve procedures narrowly (domain over-fitting).** When agents improve a procedure based on one failure, they tend to over-fit to that specific failure rather than generalizing. Inferred from user's concern about procedures being improved after every report.

- *Practical implication:* Don't update procedures after every data point. Collect data over multiple sessions, then evaluate trends before changing procedures. The self-improvement loop should be periodic, not reactive.

---

## Verification & Trust

**Agents should never be the sole source of truth.** External verification is always required — code running, tests passing, user feedback, system behavior. Per user observation: "the only reliable truth is external — you run it yourself and see if it works."

- *Practical implication:* Every workflow should have at least one external verification step. For code: tests must pass. For knowledge: sources must be cited and independently verifiable.

**High-stakes + hard-to-verify = must escalate.** Per user observation: "if it's a large irreversible damage, that's when it reaches the user to make that call." The escalation threshold is proportional to both risk and verifiability.

| Risk Level | Easy to Verify | Hard to Verify |
|-----------|---------------|----------------|
| Low | Agent decides autonomously | Agent decides, flags uncertainty |
| Medium | Agent decides with adversarial review | Escalate to user with recommendation |
| High | Agent decides with multi-agent verification | Must escalate to user |

**Tasks with delayed feedback are hardest.** Strategic decisions, shipped code with latent bugs, contract terms with future implications — agents can't get feedback until much later. Inferred from the business operations scenario and the discussion of verification timing.

- *Practical implication:* For delayed-feedback tasks, over-invest in upfront verification. Multiple adversarial agents, broader research, explicit assumption-listing.

**The "more agents = more likely correct" bet.** The fundamental assumption: consensus from multiple agents is more reliable than a single agent's judgment. Per user observation: "the bet is that more agents is better than one." This is the system's primary defense against poisoning and error.

- *Practical implication:* Important decisions should involve multiple agents. The cost is tokens; the benefit is correctness. Budget accordingly.

---

## Known Model-Specific Notes (Opus 4.6)

**1M context window available.** Can be used with `context_window_estimate_tokens=1_000_000`. Compaction threshold at ~920K by default, pushable to ~990K with `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=99`. Per user observation: "1M context window is enabled."

**Same fundamental limitations as earlier models.** Better than Opus 4.5/4.0 at implementation quality and following complex instructions, but the core behavioral patterns (premature victory, certainty bias, dropping side-constraints) persist. Per user observation: "it's going to be for the foreseeable future the same thing of limitations."

**Language-specific coding performance.** Per user observation (inferred): very good at TypeScript, Python. Performance on less common languages is likely lower but not specifically measured. The capability ledger should be populated with concrete examples from future sessions.

---

## Foundational Design Principle

**The knowledge base is cached agent work.** The fundamental purpose of the KB is to store previous agents' reasoning so future agents don't redo it. Every agent's processing is expensive (tokens + time). Once an agent does research, synthesis, or search, that work should be stored and indexed so it's findable later. Without this, agents keep rediscovering the same things — encountering the same errors, re-researching the same topics, re-deriving the same conclusions. Per user observation: this is the primary justification for aggressive knowledge persistence and broad indexing.

---

## Procedure Design Implications

Every procedure instruction must first pass a gate: **would the task outcome be meaningfully different without this instruction?** If not, remove it — it consumes attention without changing behavior.

Instructions that pass the gate fall into three levels:
- **Level 1 (simple nudge):** things agents do well natively. One line pointing the direction. Don't over-specify.
- **Level 2 (guided execution):** things agents CAN do but will improvise wrong without examples and edge cases. This is most procedure content.
- **Level 3 (delegate):** things agents structurally can't do (self-evaluation, quality judgment). Spawn a separate agent.

The most common mistake: writing a Level 1 instruction for a Level 3 problem. "Verify your output is substantive" sounds like an instruction but doesn't change behavior — agents can't self-evaluate quality. If you care, spawn a verifier. If not, the instruction fails the gate.

Full guide: [[Projects/Personal Projects/Agentic/Agentic Fractals/docs/Design Philosophy/Procedure Writing Guide|Procedure Writing Guide]].

---

## How to Use This Document

When designing a workflow or procedure, check:

1. **Is the core task a strength?** If yes (e.g., "read this file and extract information"), delegate fully.
2. **Does the task involve a known weakness?** If yes (e.g., "verify your own work"), add a mitigation (adversarial agent, external test, hook reminder).
3. **What's the risk level?** Match verification depth to risk (see trust table above).
4. **How long will the agent live?** Prefer ephemeral. If long-lived, add context offloading steps.
5. **Are there side-constraints?** If more than 2, consider a janitor agent or hook-based enforcement.

This document should be updated as we accumulate more data from actual workflow execution. Each observation should eventually link to the sessions that confirmed it.
