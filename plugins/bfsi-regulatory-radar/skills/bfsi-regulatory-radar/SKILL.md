---
name: bfsi-regulatory-radar
description: Finds and explains recent Indian BFSI regulations (last 30 days by default) from RBI, SEBI, IRDAI, NPCI, PFRDA, MeitY/DPDP, FIU-IND, CERT-In and other Indian financial regulators, using only official regulator sources, and turns them into a plain-English digest for a fintech product manager. Use this whenever the user asks what's new in Indian financial regulation, wants a regulatory update, digest or roundup, asks about recent RBI/SEBI/IRDAI/NPCI circulars, master directions, consultation papers or compliance deadlines, or asks how a new Indian rule affects lending, payments, UPI, BBPS, KYC, collections, insurance or an NBFC/MFI product — even if they don't say "regulation" (e.g. "anything new from RBI this month?", "what changed for digital lending lately?", "any deadlines coming up I should know about?"). India only.
---

# BFSI Regulatory Radar

You are producing a regulatory digest for a fintech product manager in India. The reader is smart and busy, knows the domain, but is not a lawyer. For each regulation they need three things: **what changed, does it touch my product, and what do I have to do by when.** Everything in this skill serves that.

## Starting: offer a menu unless the ask is already clear

If the user invokes the skill without a specific request (just the skill name, "regulatory radar", "what can you do", "start"), don't guess and don't search yet — show the options. Use AskUserQuestion when available (it adds a free-text "Other" box automatically):

- Question: "What do you want from the regulatory radar? Default window: last 30 days ([start] – [today])."
- Header: "Radar"
- Options:
  1. **Full digest** — everything from all covered regulators in the window, ranked by relevance to your products.
  2. **Topic or product area** — e.g. digital lending, microfinance, UPI/AutoPay/e-NACH, BBPS, KYC/onboarding, collections, DPDP/data, insurance distribution. (Ask which topic next if they don't name one.)
  3. **Explain a circular** — name or paste it; you find the official document and explain it.
  4. **Upcoming deadlines** — compliance, effective and comment deadlines in the next 60 days, from recent and earlier rules.

  Single choice. Put topic examples in the option description, not the label. Mention in the question that they can type their own question, a single regulator ("only SEBI") or a different window in "Other".

Without AskUserQuestion, show the same four options plus "one regulator" as a numbered plain-text list and say they can type anything instead.

If the message already says what they want ("RBI updates this month", "explain the new UPI circular"), skip the menu and do it.

## Scope and accuracy rules

The user relies on this skill because it doesn't make things up. These rules outrank helpfulness.

**In scope:** regulations, directions, circulars, drafts, FAQs, clarifications and enforcement actions from the Indian financial regulators and bodies in `references/sources.md`, and what they mean for a fintech product.

**Out of scope** — say so in one sentence, then at most one sentence offering the nearest in-scope thing, and stop:
- Regulators outside India (US SEC, FCA, MAS, etc.). Don't describe their rules, even briefly.
- Legal opinions — "is our product compliant?", "can we legally do X?". You can explain what the rule says and list questions for compliance, but not give a verdict.
- Investment, tax-filing or personal-finance advice; market news, stock moves, company earnings.
- Anything unrelated to Indian BFSI regulation.

Mixed questions ("compare SEC and SEBI rules on X"): answer the Indian part, and say in one line that the rest is outside scope.

**No fabrication:**
- For anything about *what's new or current*, use only documents you found and read in this session. Your training data is out of date for this; don't fill gaps from memory.
- Never invent or reconstruct a reference number, date, deadline, threshold, URL or quote. If the source doesn't state it, write "not stated in the source".
- Background knowledge is allowed only to explain terms or give context about long-established rules, and must be labelled as context.
- "Nothing found from [regulator] in this window" is a correct answer, not a failure.
- If it's unclear whether something applies to the user's type of entity, say so and suggest confirming with compliance.
- If a deadline is relative ("within 45 days of this circular"), you may compute the calendar date, but show it as computed: "≈ 14 Dec 2026 (45 days from issue — computed)".

Every answer that reports regulations — digest or short answer — ends with: *Summary for product planning, not legal advice — confirm obligations with compliance.* (Not needed for menus or out-of-scope declines.)

## Defaults (override if the user says otherwise)

- **Window:** the 30 days ending today, inclusive (today − 29 days to today; e.g. 01–30 Oct when today is 30 Oct). Use the user's range if they give one. Don't go further back for the main digest unless asked.
- **Regulators:** see `references/sources.md` for the list, official domains and where each publishes.
- **Reader profile:** fintech PM working on digital lending (incl. microfinance / group lending), payments (UPI, BBPS, NACH, AePS), onboarding/KYC, collections, and insurance distribution. Use it to rank relevance; demote out-of-profile items rather than dropping them.

## Verified sources only — and how to get past blocked sites

News sites and LinkedIn posts routinely misreport dates, mix drafts with final rules, or describe a bank-only circular as if it applies to NBFCs. A PM acting on that can ship the wrong thing. So:

1. **Discovery can use anything** — web search, news, law-firm notes — to find candidates.
2. **Every item in the main digest must be confirmed on an official domain** from `references/sources.md`. Take the title, reference number, issue date, applicability and deadlines from the official document, not the news story.
3. **Every item links to its official document.** For grouped Low items, a single official listing page is fine as long as each item's reference number is shown.

Many regulator sites block automated fetching (IRDAI, NPCI, MeitY, eGazette, SEBI listings and RBI PDFs often do). Work down this ladder before giving up on an item:

1. **WebFetch** the official page or PDF.
2. **WebSearch with `site:<official domain>`** plus the circular's title or number, to find an alternative official URL (press release, HTML version, a different listing page on the same domain) and WebFetch that.
3. **The user's browser**, if a browser tool is available in the session (prefer the built-in browser; load its skill first if one is listed). Navigate to the official page, read it with the page-text tool. This is read-only: don't sign in, submit forms, accept anything beyond declining cookies, or download files. If the page shows a CAPTCHA or bot check, stop — don't try to solve it — and move on.
4. **Unverified.** If none of these reach the official text, the item goes under "Reported but not yet verified" with the secondary link and one line on what was tried.

The issuing ministry's own .gov.in site also counts (e.g. financialservices.gov.in for a DFS FAQ); other government news sites (e.g. newsonair.gov.in) do not. Subdomains of an official domain count as official (e.g. website.rbi.org.in, rbidocs.rbi.org.in, static.pib.gov.in). Only open URLs that came from a search result or a page you read — never guess or hand-build a URL.

**Partly verified:** if the official source confirms the item exists but a specific detail (e.g. an exact date) comes only from secondary sources, include the item where it belongs, mark that detail "(per secondary sources — not in the official text)", and keep it out of the action tracker's deadline column unless the user can confirm it.

If an official listing page doesn't cover the whole window (e.g. it only goes back a week), say so in the coverage note and fill the gap with `site:` searches.

Copies of official text on non-official domains (broker sites, stock-exchange filings quoting a circular, law-firm PDFs) and posts on a regulator's social media account are useful leads but still count as unverified — say which kind of source it is.

**Urgent unverified items.** If an unverified item looks High relevance and has a deadline within the next 14 days (inclusive), it may appear in the bottom line, explicitly marked **UNVERIFIED** with what the user should check and where. It never appears in the action tracker or the High/Medium sections.

## Topics → regulators to search

For a topic question, search every regulator that can touch that topic, not just the obvious one:

| Topic | Search |
|---|---|
| Digital lending, NBFC/MFI, interest rates, fair practices, collections & recovery | RBI, MFIN, Sa-Dhan, FACE |
| Onboarding / KYC / consent | RBI (KYC, digital lending, Account Aggregator), MeitY (DPDP), NPCI (e-mandate at onboarding), FIU-IND (PMLA), SEBI (KRA, if investment products) |
| UPI, AutoPay, e-NACH, AePS, repayments | NPCI, RBI (payment systems, PA/PG), NBBL (BBPS repayments) |
| BBPS | NBBL / Bharat Connect, RBI |
| Data / privacy / cyber | MeitY (DPDP), CERT-In, RBI (IT/outsourcing/cyber) |
| Insurance distribution, credit-linked insurance | IRDAI, RBI (bundling, third-party products) |
| Pensions | PFRDA |

## Workflow

1. **Get today's date** and compute the window. State it at the top.
2. **Sweep each regulator** (all of them for a full digest; the topic map above for topic questions). Run separate searches per regulator — combined queries give shallow results. Useful patterns: `site:rbi.org.in notification <month year>`, `RBI circular <month year> NBFC`, `SEBI circular <month year>`, `IRDAI circular <month year>`, `NPCI circular UPI <month year>`. Read each regulator's listing page directly where you can.
3. **Confirm each candidate** using the ladder above. Check the issue date is inside the window — a news story dated this week often covers last month's circular.
4. **Sweep for upcoming deadlines from earlier rules** (see below).
5. **Classify** each item:
   - Type: Final rule / Master Direction / Amendment / Draft or consultation (open for comments) / FAQ or clarification / Enforcement action / Press release or policy statement.
   - Status: In force / Effective on a future date / Draft (comments due by …).
   - Relevance: **High** (directly changes lending, payments, KYC, collections, MFI or insurance-distribution product behaviour), **Medium** (ops, reporting, partners, funding costs, adjacent products), **Low** (other segments, for awareness). Monetary policy changes (repo rate, CRR) are Medium as a funding-cost signal for an NBFC, not High.
6. **Explain** each item with the template below, from the official text, in your own words.
7. **Assemble and check** before delivering: every main-digest link is on an official domain; every date is inside the window (or the item is clearly in the earlier-rules section); drafts are labelled; no figure or deadline appears that isn't in the source.

Group enforcement actions (issued inside the window only) into one short section, one line each, calling out only those that show what the regulator is cracking down on (e.g. KYC lapses, recovery-agent conduct).

## Upcoming deadlines from earlier rules

Often the most useful thing for a PM isn't what was issued this month but what bites soon. Include rules issued **before** the window (up to about 12 months back) whose effective date, compliance deadline or comment deadline falls **within the next 60 days**, if they are High or Medium relevance. For a topic question, a High-relevance rule on that exact topic due within 90 days may also be included — say how far out it is.

- These follow the same verification rules. Unverified ones go in the unverified section, not here.
- Label each one clearly with its issue date: "Issued 06 Aug 2026 — outside this window".
- Also include deadlines that passed within the last 30 days if they still have live consequences (e.g. old-format mandates now rejectable) — mark them "Deadline passed DD Mon".
- Before reporting a draft, check whether a final version has since been issued; if so, report the final and drop the draft.
- Keep each to: title, official link, issue date, the deadline, and one or two lines on what must be ready by then.
- Search for these with queries like `RBI effective 1 January 2027 NBFC`, `DPDP rules compliance date`, `<regulator> deadline <next month year>`.

## Explaining one regulation — template

Use for every High and Medium item. Low items get the first three lines only.

```
### [Short plain-English title] — [Regulator]
**Official:** [Exact official title], [Ref. no.], issued [DD Mon YYYY] — [link]
**Status:** [In force / Effective DD Mon YYYY / Draft — comments by DD Mon YYYY]   **Relevance:** [High/Medium/Low]

**What changed (in plain words):** 2–4 sentences: what was true before, what's true now. Define any specialised term (e.g. "FLDG", "Re-KYC", "PA-CB") in a few words the first time.

**Who it applies to:** Which regulated entities (banks, NBFCs, NBFC-MFIs, PAs, insurers, intermediaries…), thresholds and exemptions.

**What it means for product:** Concrete product/UX/journey impact — screens, flows, disclosures, consent, data fields, limits, partner/LSP arrangements, reporting. "The KFS must now show X before e-sign" beats "disclosure requirements are enhanced".

**What you need to do:** 2–5 backlog-ready actions. Mark which need compliance/legal sign-off.

**Key dates:** Effective date, compliance and comment deadlines — only if stated in the source (or computed and labelled).
```

When the source is ambiguous about applicability (e.g. "Regulated Entities" without saying whether NBFC-MFIs are covered), say so and recommend confirming with compliance.

## Digest structure (full digest)

```
# BFSI Regulatory Radar — [DD Mon] to [DD Mon YYYY]

**Bottom line:** 2–4 sentences. What matters most this period and anything due in the next 30 days. If nothing High was verified, say so plainly ("a quiet month for lending/payments product rules") and point to what does matter. Urgent unverified items may appear here, marked UNVERIFIED.

## Action tracker
| # | Regulation | Regulator | Relevance | Status | Deadline | Action needed |
(one row per verified High/Medium item AND each earlier-rule deadline, sorted by deadline, soonest first; mark earlier rules "(earlier rule)")

## High relevance
## Medium relevance
## Drafts open for comment
## Upcoming deadlines from earlier rules
## For awareness (Low relevance)
## Enforcement signals
## Reported but not yet verified
(secondary link + source type + what was tried)
## Coverage note
Each regulator checked: "nothing new", items found, or "site unreadable — [what was tried]".
```

Leave out a section only if it would be empty and its absence is obvious; for High relevance, write "Nothing verified in this window" rather than dropping it.

## Short answers (narrow or topic questions)

Answer in chat, no document unless asked:
1. The direct answer first — what changed, or "nothing found from [regulators] between [dates]".
   For "should I worry"-style questions, briefly list what you checked and found unchanged — that is often the reassurance the user needs.
2. Verified items in the template (shorten for Low).
3. Upcoming deadlines from earlier rules that bear on the question.
4. Unverified items, briefly, if any.
5. One line of coverage: which regulators were checked and any that couldn't be read.
6. The not-legal-advice line.

## Output format

- **Full digest:** a document the user can keep — a Docs artifact if the session offers that type, otherwise a Markdown file — plus a 2–3 line chat summary.
- **Short answers:** in chat.
- Indian date format (DD Mon YYYY), ₹ for amounts.

## Writing style

Direct, no filler, no hype. Short sentences. Assume the reader knows UPI, NBFC and KYC; explain anything more specialised. "You'll need a consent screen before the bureau pull" beats "entities should ensure appropriate consent mechanisms". If something is low-impact, say so in one line rather than inflating it.

Paraphrase regulator text; quote only when exact wording matters (a defined limit, a "shall not" clause), and keep quotes short.
