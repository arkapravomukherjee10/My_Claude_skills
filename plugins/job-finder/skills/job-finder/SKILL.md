---
name: job-finder
description: Runs an end-to-end job search — parses the user's resume, gathers role/location/salary preferences, searches Indeed, Dice, and the open web for matching roles, scores each listing against the resume, and either drafts an approval-gated application or produces a tailored (non-fabricated) resume for lower-scoring but relevant roles. Trigger this whenever the user wants to find a job, search for open roles, look for their next position, do a job hunt, or asks things like "find me some PM jobs", "help me look for a new role", "what jobs match my resume", or "I want to switch jobs" — even if they don't use the words "job finder" explicitly. Also trigger if the user asks to tailor or customize their resume for a specific job description as part of a search.
---

# Job Finder

This skill runs a full job-search loop: understand the person's background, understand what they want next, find real openings that fit, tell them honestly how well each one matches, and then either help them apply or help them get closer to a match. Every step exists because skipping it produces bad matches or, worse, applications that misrepresent the person — so follow the sequence even when it's tempting to jump straight to search results.

## Why the order matters

Job search tools usually fail in one of two ways: they show a firehose of loosely-related listings, or they auto-apply to things that don't fit and waste the person's reputation with recruiters. This skill avoids both by grounding search terms in the actual resume (not just a job title the person types), and by never letting anything leave the person's hands — application or tailored resume — without their explicit review.

## Step 1: Get the resume

Ask the person to share their resume, and tell them PDF and Word (.doc/.docx) are supported. Don't proceed to preference-gathering until you have it — the resume is what everything downstream is grounded in.

Once uploaded, read it properly:
- For PDF: consult the `pdf-reading` skill for extraction guidance.
- For .doc/.docx: consult the `docx` skill for reading guidance.
- For any file only present as a path (not already in context), consult `file-reading` first to route correctly.

## Step 2: Understand their current position

From the resume, extract — semantically and via keywords, not just the most recent header line:
- Current (or most recent) role title
- Current/most recent company
- Current location
- Core skills and tools (explicit and implied — e.g. "led migration to microservices" implies system design experience even if "system design" never appears verbatim)
- Years of experience and seniority level

Briefly reflect this back to the person in a sentence or two ("Looks like you're currently a [role] at [company] in [location], with a background in [X, Y, Z]") so they can correct anything before you search on it. Don't belabor this — one confirmation pass is enough, not a full back-and-forth.

## Step 3: Get their target criteria

Ask three things, ideally in one batch rather than three separate turns:
1. What role(s) they want to apply for next
2. Location preference (a city, "remote", or "open")
3. Intended salary range

If the person has a documented career-transition goal or positioning strategy from earlier context, you can note it and ask if this search should target that, but don't assume — let them confirm the specific role framing for this search.

## Step 4: Search for matching roles

Search broadly, using both structured connectors and the open web — each surfaces listings the other misses. Don't stop at one or two sources; different boards have different employer relationships, so real coverage means checking all of them:
- Use `tool_search` to find and call the Indeed and Dice connector tools for the target role/location, if the person has them connected (these need the connector opt-in flow — see below).
- Run targeted web searches against LinkedIn Jobs, Naukri, and general Google search for the same role/location combination — these three alone catch a very different pool than Indeed/Dice, especially for the Indian market (Naukri in particular surfaces roles that rarely appear on Western-first boards).
- Also run open web searches without a site restriction to catch company career pages and smaller boards that a site-specific search would miss.

A reasonable search pass looks like: 2-3 queries against `site:linkedin.com/jobs`, 2-3 against `site:naukri.com`, 2-3 general Google queries for the role/location, plus the connector calls if available. Vary phrasing across queries (e.g. "AI Product Manager fintech Mumbai" vs "AI PM lending India") rather than repeating the same query per source — a repeated query returns near-duplicate results and wastes the search budget.

Query using the target role and location from Step 3, not just keywords lifted from the resume — the person is searching for what they want next, not a mirror of their past.

**On connectors:** Indeed and Dice are consumer connectors that need the person's explicit choice before every use — surface them via the connector picker rather than calling them directly, even if the person mentioned wanting to use connectors earlier in the conversation. If the person hasn't connected them yet, proceed with the web search sources above rather than blocking the search on that decision.

**Include listings where salary isn't stated.** A lot of good roles don't post salary, especially outside the US, and excluding them would silently cut out a large chunk of legitimate matches. Only filter out a listing on salary grounds if a stated range is clearly and substantially below the person's intended range.

Keep searching until you have a reasonably broad pool (aim for at least 15-20 candidate listings before scoring) so the top 5 shown aren't just the first few results that happened to come back.

## Step 5: Score each listing against the resume

For every candidate listing, compare its job description against the resume across three dimensions and produce a single match score (0-100%):
- **Semantic fit**: does the day-to-day scope of the role match what the person has actually done, not just what the title says?
- **Skills fit**: do the JD's required/preferred skills show up in the resume, directly or as reasonable equivalents?
- **Keyword fit**: do domain-specific terms in the JD (tools, frameworks, methodologies, industry terms) appear in the resume?

Be honest in scoring — inflating scores defeats the purpose of the 80% threshold in Step 7. A role that's a stretch should score like a stretch.

## Step 6: Display results

Show only 5 matches at a time, each with: job title, company, location, salary (or "not listed"), match score, and a one-line reason for the score — keep the score attached to its listing, not split into a separate table, so the person never has to cross-reference two things to read one result.

Showing everything at once defeats the purpose of scoring — it just recreates the firehose this skill exists to avoid. So reveal results in batches of 5: show the top 5, then stop. If the person wants more, reveal the next 5 (not the entire remaining list at once) — and keep going one batch of 5 at a time for as long as they keep asking. This keeps each screenful small enough to actually compare, rather than asking the person to hold 10+ scored listings in their head simultaneously.

Next to each listing, offer the action that matches its score so the person can act on a listing directly instead of having to ask for it:
- **Score > 80%:** offer to draft the application (an "Apply" action) — see Step 7.
- **Score ≤ 80%:** offer to tailor the resume to that specific JD (a "Tailor resume" action) — see Step 7. This isn't a dead end: once the tailored resume exists, the person can still apply through it if a connector covers that platform.

These are per-listing choices the person triggers individually, not something that fires automatically just because a listing appeared in the results — showing a match is not the same as acting on it.

## Step 7: Act on a listing

When the person picks a listing to act on, follow the path for its score:

**Score > 80% — draft and apply with approval.**
Prepare the application (cover letter if the platform wants one, confirm the resume version being sent) and show the person the complete package before anything is sent. Only after they explicitly approve, submit it through the relevant connector (Indeed/Dice). Never submit without that explicit approval step, even for a very high score — the person's name and reputation are on every application.

**Score ≤ 80% — produce a tailored resume, not an application.**
Rewrite the resume to foreground the experience, skills, and language most relevant to this specific JD — reordering, re-emphasizing, and rephrasing what's already there.

This is the one hard rule in this whole skill: **never add a skill, tool, responsibility, metric, or credential that isn't genuinely in the original resume.** Tailoring means better packaging of real experience, not new claims. If the JD wants something the resume doesn't support, leave it out rather than implying it — a tailored resume that oversells is worse than no application at all, because it surfaces in the interview. When in doubt about whether something is a legitimate rephrasing versus an invented claim, err toward leaving it out.

Produce the tailored resume as a .docx using the `docx` skill, and let the person review it before it's used anywhere.

A lower match score changes which resume gets used — it doesn't mean the person forfeits the ability to apply through a connector. So after the tailored resume is ready, check the same way as the >80% path: if the listing's platform has a connector available (e.g. it's an Indeed or Dice listing), offer to draft and submit the application with the tailored resume attached, gated on the person's approval exactly as in the >80% path. If no connector covers that platform, say so plainly and let the tailored resume stand as the deliverable — the person applies manually.

## A note on judgment

If a listing's JD is thin or vague, say so rather than forcing a confident score — a made-up-sounding score is worse than an honest "not enough detail here to score confidently." Same goes for salary: if a range is wildly inconsistent with market rate for the role/location, flag it rather than treating it as gospel.
