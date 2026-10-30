# Claude Skills

A collection of [Claude](https://claude.ai) skills I've built for product management, fintech and AI workflows.

Each skill is a self-contained folder with a `SKILL.md` (instructions Claude loads when the skill is relevant) and, where needed, a `references/` folder with supporting material.

## Skills

| Skill | What it does |
|---|---|
| [`bfsi-regulatory-radar`](./bfsi-regulatory-radar) | Finds and explains recent Indian BFSI regulations (RBI, SEBI, IRDAI, NPCI, DPDP and more) in plain English for a fintech PM, using only official regulator sources. Digests, topic deep-dives, circular explainers and upcoming deadlines. |
| [`assistant-pm`](./assistant-pm) | Acts as an Assistant PM, taking a product area from ambiguity to a design-backed PRD through six checkpointed stages: segmentation, JTBD, problem space, solution space, KANO/MoSCoW/RICE scoping, and a PRD with Figma designs. |
| [`job-finder`](./job-finder) | Runs an end-to-end job search: parses your resume, searches job boards and the web, scores each listing against your resume, then drafts an approval-gated application or tailors your resume without fabricating anything. |
| [`new-ai-tool-scout`](./new-ai-tool-scout) | Triggered by `#NewAITool`. Researches a newly launched AI tool from the last 10 days of coverage and writes a briefing on what it does, use cases, and relevance to whoever is asking (PM, developer, founder, student and more). |
| [`adaptive-model-router`](./adaptive-model-router) | Routes each subtask to the cheapest Claude tier (Haiku, Sonnet, Opus) that can do it correctly, re-routes mid-task, and when the budget runs low switches to progressive checkpointing and a self-contained handoff summary. |

## Installing a skill

**Claude app (web, desktop, mobile):** zip the skill's folder (for example `bfsi-regulatory-radar/`) and upload it as a custom skill in Settings.

**Claude Code:** copy the skill's folder into one of these:

- `~/.claude/skills/<skill-name>/` to use it across all your projects
- `.claude/skills/<skill-name>/` inside a project to use it only there

```bash
git clone https://github.com/arkapravomukherjee10/My_claude_skills.git
cp -r My_claude_skills/bfsi-regulatory-radar ~/.claude/skills/
```

Claude picks up a skill automatically when your request matches its description. You can also ask for it by name.

## Repo layout

```
<skill-name>/
├── SKILL.md          # frontmatter (name, description) + instructions
└── references/       # optional supporting files the skill reads when needed
```

## Notes

- Some skills default to a fintech/microfinance PM context (for example `assistant-pm` and `new-ai-tool-scout`). They work for anyone, but you may want to adjust those defaults for your own domain.
- The BFSI Regulatory Radar produces summaries for product planning, not legal advice.

## Author

Arkapravo Mukherjee — product manager working across digital lending, payments and AI tooling.
