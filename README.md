# Claude Skills

A collection of [Claude](https://claude.ai) skills I've built for product management, fintech and AI workflows, packaged as a Claude Code plugin marketplace so you can install any of them with one command.

## Skills

| Plugin | What it does |
|---|---|
| [`bfsi-regulatory-radar`](./plugins/bfsi-regulatory-radar) | Finds and explains recent Indian BFSI regulations (RBI, SEBI, IRDAI, NPCI, DPDP and more) in plain English for a fintech PM, using only official regulator sources. Digests, topic deep-dives, circular explainers and upcoming deadlines. |
| [`assistant-pm`](./plugins/assistant-pm) | Acts as an Assistant PM, taking a product area from ambiguity to a design-backed PRD through six checkpointed stages: segmentation, JTBD, problem space, solution space, KANO/MoSCoW/RICE scoping, and a PRD with Figma designs. |
| [`job-finder`](./plugins/job-finder) | Runs an end-to-end job search: parses your resume, searches job boards and the web, scores each listing against your resume, then drafts an approval-gated application or tailors your resume without fabricating anything. |
| [`new-ai-tool-scout`](./plugins/new-ai-tool-scout) | Triggered by `#NewAITool`. Researches a newly launched AI tool from the last 10 days of coverage and writes a briefing on what it does, use cases, and relevance to whoever is asking (PM, developer, founder, student and more). |
| [`adaptive-model-router`](./plugins/adaptive-model-router) | Routes each subtask to the cheapest Claude tier (Haiku, Sonnet, Opus) that can do it correctly, re-routes mid-task, and when the budget runs low switches to progressive checkpointing and a self-contained handoff summary. |

## Install

### Claude Code (recommended)

Add the marketplace once:

```bash
claude plugin marketplace add arkapravomukherjee10/My_Claude_skills
```

Then install whichever skills you want:

```bash
claude plugin install bfsi-regulatory-radar@arkapravo-skills
claude plugin install assistant-pm@arkapravo-skills
claude plugin install job-finder@arkapravo-skills
claude plugin install new-ai-tool-scout@arkapravo-skills
claude plugin install adaptive-model-router@arkapravo-skills
```

You can also run `/plugin` inside a Claude Code session to browse and install from the marketplace. To pick up new versions later, run `claude plugin marketplace update arkapravo-skills`.

### Claude app (web, desktop, mobile)

1. Download this repo (**Code → Download ZIP**) and unzip it.
2. Find the skill folder at `plugins/<name>/skills/<name>/`, for example `plugins/bfsi-regulatory-radar/skills/bfsi-regulatory-radar/`.
3. Zip that inner folder and upload it in Claude under **Customize → Skills → + → Create skill → Upload a skill**.

You need code execution turned on (**Settings → Capabilities → Create and edit files**).

### Manual copy (Claude Code, no marketplace)

```bash
git clone https://github.com/arkapravomukherjee10/My_Claude_skills.git
cp -r My_Claude_skills/plugins/bfsi-regulatory-radar/skills/bfsi-regulatory-radar ~/.claude/skills/
```

Claude picks up a skill automatically when your request matches its description. You can also ask for it by name.

## Repo layout

```
.claude-plugin/
└── marketplace.json              # catalogue of the plugins below
plugins/
└── <name>/
    ├── .claude-plugin/
    │   └── plugin.json           # name, description, version, author
    └── skills/
        └── <name>/
            ├── SKILL.md          # frontmatter (name, description) + instructions
            └── references/       # optional supporting files the skill reads when needed
```

## Notes

- Some skills default to a fintech/microfinance PM context (for example `assistant-pm` and `new-ai-tool-scout`). They work for anyone, but you may want to adjust those defaults for your own domain.
- The BFSI Regulatory Radar produces summaries for product planning, not legal advice.

## Author

Arkapravo Mukherjee — product manager working across digital lending, payments and AI tooling.
