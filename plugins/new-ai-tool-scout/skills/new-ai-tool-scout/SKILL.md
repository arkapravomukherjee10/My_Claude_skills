---
name: new-ai-tool-scout
description: "Research and report on a newly-launched AI tool whenever the user writes \"#NewAITool\" (with or without a tool name attached, e.g. \"#NewAITool\" or \"#NewAITool Cursor Composer\"). Searches the web — prioritizing TechCrunch and Product Hunt, but pulling from other reputable outlets too — for launch coverage from the last 10 days, then produces a report covering what the tool does, concrete use cases, day-to-day relevance, and relevance tailored to who's asking. Adapts the lens across a range of professional archetypes (Product Manager, Fintech PM, AI researcher/builder, student/learner, content creator, developer, founder, designer, marketer, sales/BD, investor, educator, executive, freelancer, or general professional) — inferring the right one from context/memory before defaulting to a PM/Fintech PM angle. Always trigger on the literal \"#NewAITool\" tag even if the rest of the message is terse or just a product name — treat it as an explicit command, not a casual mention."
---

# New AI Tool Scout

The user relies on this to stay on top of the AI tool landscape. When they tag a message with `#NewAITool`, they want a fast, well-sourced briefing — not a generic product description pulled from memory. Model knowledge about tool launches goes stale almost immediately, so every invocation of this skill must search live rather than answering from training data.

## When this triggers

The literal tag `#NewAITool` anywhere in the message. It may appear:
- Alone ("#NewAITool") — meaning "find whatever AI tool launched recently and brief me"
- With a tool name ("#NewAITool Genspark AI") — meaning "brief me on this specific tool"
- With a link — fetch the link first, then search around it for corroborating coverage

Treat the tag as a command. Don't wait for more elaboration — proceed straight to research.

## Step 0: Figure out who's asking

Different users reach for this tag for different jobs-to-be-done, and the report should be shaped around that job rather than defaulting to one lens for everyone. Before searching, work out which archetype (or mix) fits:

- **Product Manager** (default lens) — evaluating whether/how to integrate the tool into their product workflow or their team's stack. Cares about integration effort, workflow fit, team adoption.
- **Fintech PM specifically** — the above, plus compliance, lending workflows, KYC, embedded finance, agentic transactions angle.
- **AI researcher / builder** — cares about underlying architecture, model/technique novelty, benchmarks, what's genuinely new vs. repackaged.
- **Student / learner** — cares about what the tool teaches them, learning curve, whether it's a good way to build skills or understand a concept, cost/free-tier for learning.
- **Content creator** — cares about output quality, speed/throughput, style control, cost per piece, how it fits a content pipeline.
- **Software developer / engineer** — cares about SDK/API quality, language/framework support, how it fits an existing dev workflow (IDE, CI/CD), self-hosting vs. hosted, licensing.
- **Founder / entrepreneur** — cares about whether it's a viable build-on-top-of or a competitive threat, speed to MVP, pricing at scale, defensibility of anything built with it.
- **Designer** — cares about output fidelity, control over style/brand consistency, handoff to code, how it fits a design tool stack (Figma etc.).
- **Marketer / growth professional** — cares about campaign/content throughput, personalization at scale, measurable funnel impact, cost per output vs. agency/freelancer.
- **Sales / BD professional** — cares about prospecting, outreach personalization, meeting prep, CRM integration, time saved per deal cycle.
- **Investor / analyst** — cares about market positioning, competitive landscape, funding/traction signals, whether this is a durable category or a feature that gets absorbed by incumbents.
- **Educator / teacher** — cares about classroom or curriculum use, academic integrity implications, accessibility, cost for institutional use.
- **Executive / leadership** — cares about strategic/organizational implications, risk (security, compliance, vendor lock-in), ROI case for team-wide rollout.
- **Freelancer / consultant** — cares about billable-time impact, client deliverable quality, cost relative to what they can charge, differentiation from what any client could get themselves.
- **Other / unspecified** — general working professional; keep the "who'd use this and why" framing broad rather than picking one lens.

**Determine this by inference first:**
- Check what's already in context this conversation (has the user said what they're working on, their role, or what they want the tool for?).
- Check memory/past-chat context for known role or current focus (e.g. their day job, job search, content work).
- If a tool name came with explicit intent ("#NewAITool Descript, thinking of using it for my LinkedIn videos") that alone tells you the lens — don't ask.

**Archetype isn't the whole picture — the specific job-to-be-done still matters, for every archetype.** Someone's professional role doesn't fully determine why they want to know about a given tool. A PM might be asking because they want to integrate it into their product workflow — or just as plausibly because they're curious about it for a side project, personal productivity, a hobby, or something entirely outside work. The same applies to every other archetype: a developer might want a tool purely for personal writing, an educator might be evaluating something for their own use rather than the classroom, a content creator might be asking as a researcher rather than a builder. Don't let "PM" (or any other role) auto-fill "this must be about their job" — check whether the message or context signals a different specific motivation, and if so, follow that instead of the archetype's default framing. This is exactly why Step 0 asks you to identify the *job-to-be-done*, not just tag a professional label; the label is a starting hint, not the final answer.

If the user's established default is Product Manager / Fintech PM (per memory or context), then unless a message signals something else — including a non-work reason even while staying in their usual PM identity (e.g. "is this any good for personal use" or "for a family member's blog") — proceed with the PM/Fintech PM job-to-be-done without asking.

**Only ask if genuinely unclear** — e.g. a bare "#NewAITool" from someone with no established context, or when it seems plausible the user wants a different job-to-be-done than their usual one this time (whether that's a different archetype entirely or the same archetype but a different reason). Keep the question short and skip it entirely once the lens is established for a session or is obvious from memory. Don't ask this every single time out of habit — the goal is fewer questions, not a mandatory checkpoint.

## Step 1: Search

Search the web for launch coverage from **the last 10 days** (check today's date and calculate the window before searching — don't guess).

- If a specific tool was named, search for that tool by name plus terms like "launch," "review," "TechCrunch," "Product Hunt."
- If no tool was named, search broadly for AI tool launches in the window (e.g. "new AI tool launch this week," "Product Hunt AI tool of the day," "TechCrunch AI launch") and pick the most significant / most-covered one. If several are roughly equally notable and it's not obvious which the user means, briefly list 2-3 candidates and ask which they want — don't silently pick one when there's real ambiguity.
- Prioritize TechCrunch and Product Hunt as sources, but don't stop there — cross-check against the tool's own launch page/blog and at least one other reputable outlet (e.g. The Verge, VentureBeat, Ars Technica, Hacker News discussion) where available. A single source is thin; use `web_fetch` on the actual articles rather than trusting search snippets alone.
- Scale the number of searches to how much is out there — a well-covered launch might need 3-4 searches/fetches to properly triangulate; an obscure one might only turn up one real source, which is fine, just note the thin coverage rather than padding it out.

## Step 2: Write the report

Adapt the structure to what's actually interesting about this particular tool — don't force a rigid template onto every tool. That said, the report should naturally cover these four things somewhere in it, because they're what the user is actually trying to learn each time:

1. **What it does** — a clear, concrete explanation of the tool's core function. Not marketing copy — what does it actually let someone do that they couldn't (as easily) before?
2. **Use cases** — a handful of specific, realistic scenarios where someone would reach for this tool.
3. **Day-to-day relevance** — is this something a working professional would plausibly use regularly? For whom, and why (or why not)?
4. **Relevance for the audience determined in Step 0** — this is the lens that matters most, and it should match who's actually asking rather than defaulting blindly. Use the "cares about" description for the relevant archetype from Step 0 to shape this section — e.g. a developer wants SDK/API and workflow-fit detail, an investor wants market positioning and durability, a student wants learning curve and whether it's a shortcut or a genuine skill-builder. But treat that "cares about" list as a starting point, not a script — if Step 0 surfaced a specific job-to-be-done that diverges from the archetype's usual framing (e.g. a PM asking for personal rather than product reasons), write to that actual motivation instead of the generic archetype description. If a tool has real relevance to the broader trend but nothing that hits the specific audience's lens, say that plainly — a manufactured stretch is less useful than an honest "no direct angle here for [audience]."
   
   If two or more archetypes or motivations are plausibly in play (e.g. the user asking on behalf of both their PM work and a content-creator side project), briefly cover both rather than forcing a single lens.

Keep the tone direct and analytical, like a briefing between colleagues — not promotional. If the tool is overhyped relative to what it actually does, say that. If pricing, access, or waitlist status is relevant and known, include it briefly.

Follow standard web-search sourcing practice: paraphrase rather than quote, cite sources briefly by name (e.g. "per TechCrunch's coverage"), and flag if coverage is thin or conflicting.

## Step 3: Ask for output format

Before finalizing, ask the user whether they want this as:
- A saved markdown file they can keep/reuse (use the docx or md file-creation flow depending on complexity — plain markdown file is usually sufficient here), or
- Just the report in chat

If they've already told you in a prior turn (e.g. they said "always give me a file" or similar), skip asking and just follow that preference. Otherwise ask once per invocation — it's a quick toggle, not worth assuming.

## Notes

- Don't reproduce large verbatim chunks of any article — paraphrase per standard copyright practice, one short quote max per source if a quote is genuinely needed.
- If the "last 10 days" window turns up nothing verifiable for a named tool (e.g. it actually launched months ago, or doesn't exist), say so directly rather than stretching older coverage to fit — the user is relying on this to stay current, so false freshness defeats the purpose.