# Scoping frameworks: KANO, MoSCoW, RICE

Used in Stage 5 of the assistant-pm pipeline to prioritize confirmed solution candidates. Run all three — they answer different questions, and disagreement between them is a useful signal, not a bug.

---

## KANO Model

**Question it answers**: What kind of satisfaction/dissatisfaction does this feature create?

Categories:
- **Basic / Threshold (Must-be)**: Expected. Absence causes strong dissatisfaction; presence causes no extra delight (e.g. "the app doesn't crash," "OTP works").
- **Performance (One-dimensional)**: Satisfaction scales linearly with how well it's done (e.g. "loan disbursal speed" — faster is better, slower is worse, roughly proportionally).
- **Delighter (Excitement/Attractive)**: Unexpected. Absence isn't noticed; presence creates disproportionate delight (e.g. proactive eligibility nudges the user didn't know to ask for).
- **Indifferent**: Users don't care either way — deprioritize.
- **Reverse**: Some users actively dislike it — flag for segment-specific rollout if found.

**Rigorous method** (if the user wants to survey real users): for each feature, ask a paired question —
- Functional: "How would you feel if this feature existed?"
- Dysfunctional: "How would you feel if this feature did NOT exist?"
Each answered on a 5-point scale (Like it / Expect it / Neutral / Live with it / Dislike it). Cross-reference the pair using the standard KANO evaluation table to get the category.

**Fast/inferred method** (when no survey is feasible): classify based on domain judgment and precedent, but say explicitly that it's inferred, not surveyed, and flag which classifications are highest-risk to be wrong (usually the Basic vs. Performance line).

---

## MoSCoW

**Question it answers**: What's in scope for *this* release/timebox?

- **Must have**: Non-negotiable for this release. Without it, the release doesn't ship or doesn't make sense (legal/compliance items often land here — e.g. DPDP consent flows).
- **Should have**: Important, painful to leave out, but the release can technically ship without it if forced.
- **Could have**: Desirable, low cost/impact if cut — nice-to-haves in this window.
- **Won't have (this time)**: Explicitly out of scope for this release, not forever. Naming this reduces scope creep arguments later.

Keep MoSCoW scoped to a *specific release/timebox* — the same feature can be a Must for v2 and a Won't for v1. Always state which release/timebox you're MoSCoW-ing against.

---

## RICE

**Question it answers**: What's the relative priority order, quantitatively?

`RICE score = (Reach × Impact × Confidence) / Effort`

- **Reach**: How many users/accounts does this affect in a given period? Use a real number or estimate (e.g. "~4,000 active JLG borrowers/quarter"). State the time window.
- **Impact**: Effect per user, typically scored on a scale: 3 = massive, 2 = high, 1 = medium, 0.5 = low, 0.25 = minimal. This is a judgment call — say so.
- **Confidence**: How sure are you about Reach/Impact/Effort estimates? 100% = high confidence, 80% = medium, 50% = low. Lower confidence should pull the score down, not just be a footnote.
- **Effort**: Person-time to build, in a consistent unit (e.g. person-weeks). Larger effort divides the score down.

**Always show your inputs, not just the final score** — a bare RICE number without the underlying Reach/Impact/Confidence/Effort is not auditable and the user can't sanity-check or correct a single wrong assumption.

---

## Presenting the consolidated view

Build one table: candidate rows × KANO category, MoSCoW bucket, RICE inputs + score. Sort by RICE score by default, but call out explicitly where MoSCoW's Must-have status overrides a low RICE score (compliance/legal items are the classic case) rather than letting the numeric sort silently bury them.
