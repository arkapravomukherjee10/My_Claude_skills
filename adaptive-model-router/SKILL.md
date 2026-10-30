---
name: adaptive-model-router
description: Optimizes cost-vs-quality (ROI) on tasks with multiple subtasks, delegated subagent work, or long research/analysis chains that risk straining the token budget. Automatically routes each subtask to the cheapest Claude tier (haiku/sonnet/opus) that can do it correctly, re-routes mid-task as difficulty becomes clearer, and once budget runs low switches to progressive summarization — a continuously-updated, increasingly-compressed checkpoint — so that if the quota is about to run out, it proactively delivers a complete, self-contained handoff summary as its own response, without waiting for a further user prompt, sufficient to continue the work in a fresh session on any other LLM. Use whenever a task has several distinct subtasks, spawns subagents, is open-ended/long-running (deep research, large codebase work, multi-document synthesis), or conversation/tool-output volume is large enough that running out of budget mid-task is a real risk — even if the user never says "model" or "tokens."
---

# Adaptive Model Router

## The problem this solves

Not every subtask in a job deserves the same model. Reformatting a table, extracting fields from text, or applying a fixed template is wasted spend on a top-tier model. A subtle architectural decision, an ambiguous debugging session, or a high-stakes financial/legal calculation is a false economy on a cheap one. Left alone, a single conversation tends to run everything at one uniform tier — usually whatever tier started the conversation — which is rarely the ROI-optimal choice for every step inside it.

This skill gives a repeatable way to: (1) size up a task and automatically pick the right tier for each piece of it, no permission needed, (2) notice mid-task when a piece turns out to be easier or harder than expected and re-route it, also automatically, and (3) protect the work itself when the budget for the conversation is running low — first by shifting into a progressively-compressed running checkpoint, then, if the quota is genuinely about to run out, by proactively delivering a complete handoff summary on its own initiative, since at that point there may be no user turns left to wait for.

## What is actually controllable, and what isn't

Be precise with the user about this — overpromising erodes trust fast:

- **The model powering the main conversation is fixed by session configuration.** There is no tool call that hot-swaps it mid-turn. If a genuine upgrade/downgrade of the *main thread* seems warranted, say so plainly and let the user make that switch through their client (model picker), rather than implying it happens automatically.
- **What actually IS controllable, and where the real leverage is:** the `model` parameter on the Agent tool, which sets the tier for delegated subagent work (`haiku`, `sonnet`, `opus`). Most multi-subtask jobs are dominated by delegatable work, so routing subtasks to the right tier via subagents captures most of the achievable ROI.
- **Scheduled/background triggers** (`mcp__claude-code-remote__update_trigger`) also carry a `model` field, but it must never be changed autonomously — only when a human explicitly asks, in the moment, in their own words. Don't fold trigger-model changes into this skill's automatic logic.
- **Budget awareness** is approximate, not exact. There is no live "% of context window used" readout. Use the proxies in the Budget section below, and say "this is an estimate" rather than a precise number.

## Step 1 — Classify the task before doing it

Before starting multi-step work, sketch the subtasks and assign each a tier using this rubric. Err toward `sonnet` when a subtask doesn't clearly belong at the extremes — it's the generalist default and the safe middle ground.

| Tier | Use for | Signals |
|---|---|---|
| **haiku** | High-volume, low-ambiguity, mechanical work | Fixed-format extraction, reformatting, classification against clear categories, boilerplate generation, simple lookups, repeating the same transform across many items |
| **sonnet** (default) | Everyday reasoning, writing, and coding | Most research, drafting, standard coding tasks, tool orchestration, synthesis of a moderate amount of material |
| **opus** | Hard, ambiguous, or high-stakes reasoning | Multi-step architectural or strategic decisions, subtle debugging after a simpler tier struggled, financial/legal/medical logic where a wrong answer is costly, genuinely novel problems with no clear template |

This selection happens **automatically, by default, with no approval gate** — the definition of "best" here is fixed (ROI: cheapest tier that can do the subtask correctly), so there's nothing subjective to confirm before acting on it. For a task with N subtasks, this classification becomes N `Agent` calls with different `model` values rather than one uniform pass. It's still worth naming the plan out loud briefly ("I'll pull raw data with haiku subagents, then have sonnet write the analysis") so the user has visibility — but this is an FYI, not a question to wait on. The one place approval genuinely gates a model decision is the budget-driven downgrade in Step 3, which is a different kind of decision (a cost-cutting compromise under pressure, not a routine ROI choice) — don't confuse the two.

## Step 2 — Re-route mid-task, not just at the start

Treat the initial classification as a hypothesis, not a commitment. Re-check it at natural phase boundaries (after each major subtask completes, or when something surprising happens):

**Escalate a subtask's tier when:** a lower tier's output is wrong, contradictory, or had to be redone; the task reveals hidden complexity once you're inside it (e.g. "simple lookup" turns into disambiguating conflicting sources); the user pushes back on quality; correctness stakes turn out higher than first assumed.

**De-escalate when:** a task decomposes into many small mechanical repeats once you see its actual shape; the user signals cost sensitivity; a piece turns out simpler than scoped (e.g. "analyze this" turns out to just be "extract these five fields").

This re-routing only applies to *delegated* subagent work (Step 1's mechanism). It does not mean silently changing how much effort the main thread itself puts in — if the main thread's own reasoning needs to get more careful, just do that; if it seems like the main thread itself would benefit from a different underlying model tier, that's the main-thread case from the "what isn't controllable" section — surface it to the user, don't act on it unilaterally.

## Step 3 — Watch the budget, and ask before downgrading main-thread scope

There's no exact live meter, so use proxies together rather than any single one:

- A `<total_tokens>` figure surfaces periodically in system reminders — it's account/session budget, not context window, but a sharp drop between readings is a signal.
- Conversation length: many tool calls, large file reads, or big pasted outputs accumulate context fast even if the visible conversation looks short.
- Task shape: research that keeps turning up more sources, or a debugging loop that keeps not resolving, are the patterns most likely to run long.

When it looks like the *main thread* is approaching its practical limit for the session (a rough rule of thumb: once it feels like real spend is being burned on cheaper, batchable work), do not silently switch to a lower-effort mode. Ask the user first, with concrete options, e.g. via a quick multiple-choice check-in:

- Continue as-is (accept the cost/quality tradeoff of staying the course)
- Shift remaining subtasks to leaner subagent delegation to conserve budget
- Checkpoint now and hand off to a fresh session (see Step 4)

Only proceed with a downgrade path once the user picks it. This mirrors why the trigger-model field can't be changed autonomously: model/effort tier is a cost decision, and cost decisions belong to the user.

## Step 4 — At ~90% budget: stop scoping new work, start progressive summarization

Crossing ~90% changes the goal. It's no longer "finish everything," it's "make sure nothing gets lost." From here, keep working through the remaining task list in priority order, but change *how* progress is tracked while doing it:

- **Open (or continue) the same live checkpoint file** described in `references/handoff-template.md`, and update it after *every* subtask completes from this point forward — not just once at the end. Treat it as the running source of truth, not a final deliverable written once.
- **Compress as you go.** Once a piece of work is safely captured in the checkpoint (the fact, the decision, the artifact path), let its verbose form drop out of active attention — keep the one- or two-line distilled version in the checkpoint, not the full working-through. This is what makes it "progressive": each update should be denser than free-form conversation, so the checkpoint stays cheap to maintain even as more accumulates.
- **Triage the remaining task list.** With less runway left than originally assumed, do the highest-value/most-critical remaining subtasks first, and say so — don't spend the shrinking budget on nice-to-haves while a critical item is still undone.
- This phase does **not** pause for approval before each update — constantly checking in would itself burn the budget it's trying to protect. It's also distinct from Step 3: Step 3 is "should we cut scope to save cost," decided while there's still time to discuss it; this phase is "budget is nearly gone, keep the record current by default," because there may not be time left to discuss anything.
- **Shrink the size of each remaining subtask/delegated call as the risk zone gets closer, don't hold chunk size constant.** Checkpointing can only happen at boundaries between calls — between one `Agent` call returning and the next starting, or between turns — never mid-call, because a subagent runs to completion as one opaque unit and a message being generated can't pause itself. That means the most work that can ever be lost to an unlucky cutoff is exactly the size of whatever's inside the call currently in flight. Early in a task, when budget is comfortable, it's fine (better, even) to bundle several pieces into one call to save overhead — e.g. one subagent researching five competitors at once. Once in the ~90%+ zone, deliberately split further: one call per competitor instead of one call for five, and if a single piece is itself heavy, split that too (pricing separately from product line separately from news), even though the extra calls cost a bit more in repeated setup overhead. The overhead is the price of insurance: it shrinks the size of the single worst-case loss to match how little runway is actually left. Don't keep dispatching big bundled calls late in the task just because that's how the task started.

## Step 5 — Before the quota is actually gone, deliver the final handoff proactively

Here's the hard constraint worth being honest about: once tokens are truly at zero, no further output is possible at all — not even a warning. So "wait until the quota is fully exhausted, then summarize" can't be taken literally; the summary has to go out *with margin still in hand*, sized so it can finish completely before the hard cutoff. Treat "the entire quota is over" as "the last safe moment to still produce a complete message," not the literal last token.

When the proxies from Step 3 indicate that margin is nearly gone (a rough marker: only enough runway left for one more substantial response, after the progressive checkpoint has already been running for a while):

1. **Stop task work immediately.** Don't finish "just one more" subtask first — that's exactly the miscalculation that leads to a cutoff mid-sentence.
2. **Do not wait for a user turn and do not ask permission.** This is the one point in the whole skill where acting without checking in is correct, and deliberately so: the user may have no further turns/budget left either, so a question here can strand the handoff unsent. Deliver the summary as the response itself.
3. **Produce the fullest version of the handoff**, using `references/handoff-template.md` as the skeleton but erring toward completeness over brevity this one time — this is explicitly the copy meant to seed a brand-new session in *any* other LLM with zero shared history, so it needs to be self-sufficient: the objective, every completed piece of work with exact artifact locations, every critical fact/figure/decision gathered along the way (not just pointers to files that might not travel with the summary — inline the actual critical values), what's still open, prioritized next actions, and a ready-to-paste resume prompt.
4. **Save it** to a file in the working directory and, if this session is attached to a project, to a project doc — belt-and-suspenders in case the chat transcript itself becomes hard to scroll back to.
5. **State plainly at the top of the message** that this is a proactive handoff triggered by the budget running out, so the user isn't confused by an unprompted large summary landing without a question preceding it.

## Quick reference

- Multi-subtask job → classify each subtask automatically, no approval needed (Step 1) → delegate via `Agent` with the right `model` → re-route up or down as reality reveals itself, still automatic (Step 2).
- Budget getting tight on the main thread, and a scope/cost tradeoff is genuinely optional → ask, don't silently downgrade (Step 3).
- ~90% budget gone → switch to progressive summarization: keep working, but keep the checkpoint file continuously current and increasingly compressed (Step 4).
- Margin nearly exhausted (last-safe-moment) → stop task work, skip the check-in, and proactively deliver the fullest possible handoff as the response itself (Step 5).
- Never assume this skill can change the main session's own model, or a scheduled trigger's model, without the user explicitly asking in that moment.
