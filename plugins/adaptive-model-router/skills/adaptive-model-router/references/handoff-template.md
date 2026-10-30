# Handoff / Checkpoint Template

This template is used two ways, per Steps 4 and 5 of the skill:

- **Rolling checkpoint (Step 4, ~90%+ budget used):** the same file, opened once and then updated after every subtask from that point on — each update denser than the last, so it stays cheap to maintain as more accumulates. This is the safety net: if a cutoff happens unexpectedly, the last saved version is still recent and usable.
- **Final handoff (Step 5, margin nearly exhausted):** the same structure, but delivered as the response itself, proactively, without waiting for a user prompt — err toward completeness here rather than compression, since this copy needs to be sufficient on its own with zero shared history.

Either way, the goal is a document that a different model, with zero memory of this conversation, could read once and continue correctly — no guessing, no re-deriving decisions already made.

Fill in every section. If a section is genuinely empty (e.g. no open questions), write "None" rather than deleting it — an omitted section reads as "forgotten," not "not applicable."

```markdown
# Handoff: <short task name> — checkpoint at <date/time>

## Objective
What the user actually asked for, in one or two sentences. Include the definition
of "done" if one was established.

## Constraints & preferences established so far
Anything the user specified or corrected along the way: format, tone, scope
boundaries, things explicitly ruled out, deadlines, audience. A fresh session
should not have to relearn these by making the same mistake again.

## Completed work
For each finished piece: what was done, and where the artifact lives (exact
file path, project doc path, or URL). Be concrete — "wrote the analysis" is
useless without a path; "wrote analysis.md in the working directory, section
3 covers Q2 numbers" is usable.

## Key decisions and why
Not just what was decided, but the reasoning, especially where more than one
approach was viable. A resumed session that doesn't know *why* a path was
chosen will second-guess it and waste budget re-litigating a settled question.

## Open questions / not yet resolved
Anything still genuinely undecided or blocked, and on whom/what it's blocked.

## Next action
The single concrete next step, stated so specifically that the resuming
session could start executing immediately without re-reading the whole
conversation first — e.g. "Read analysis.md, then draft the executive
summary section using the Q2 numbers already computed there," not "continue
the analysis."

## Resume prompt
A one- or two-sentence prompt the user can literally paste into a new session
to kick this back off, e.g.:
"Continue the <task name> from the checkpoint at <path>. Read that file first,
then proceed with the Next Action listed in it."
```

## Notes on using this well

- **Write it before the budget is actually gone, not after.** If the checkpoint itself gets cut off mid-sentence because it was left too late, it's worthless. Trigger this at the ~90% mark discussed in the skill, with margin to spare.
- **Prefer a real file over a chat message.** A file (or project doc, if the session is attached to a project) persists independently of the conversation; a message in the transcript does not survive into a fresh session the way a linked artifact does.
- **Keep it dense, not exhaustive.** The point is to save the *next* session's tokens, not to prove how much work happened. A resuming session that has to read 3000 words to find the one path it needs has defeated the purpose.
