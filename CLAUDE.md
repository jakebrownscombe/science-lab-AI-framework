# CLAUDE.md: Navigation and operating instructions

> **This file is the single source of truth for any AI instance using the `science-lab-AI-framework`.** Anthropic's Claude Code reads it automatically when the folder is in scope. Other model harnesses can be configured to load it at session start.

## What this is

A vendor-neutral reference framework for building a hybrid digital lab. The companion paper (Brownscombe et al., in preparation, *Beyond the AI Scientist: An architecture for human-AI scientific practice*) describes the architecture in §2-§4.

## How to use

Before starting a substantive task, read the relevant SKILL.md file(s) and the relevant convention files listed below. Skills contain detailed instructions, sub-agent architectures, and templates that improve output quality. Conventions encode voice, format, research-integrity, and methodological protocols that apply across tasks.

The rule is: match the task to its skill, read the skill, follow its instructions. Always load the relevant conventions.

## Always-load conventions

When the adopter has populated the templates, these files take the non-template name. Until then, the template stands in.

| Task type | Required conventions files |
|-----------|---------------------------|
| Any writing | `conventions/voice.md` (falls back to `voice.template.md` if not populated) |
| Manuscript drafting or revising | + `conventions/manuscript-format.md` |
| Reviewer reply | + `conventions/reply-format.md` and `conventions/manuscript-format.md` |
| Producing figures or tables | + `conventions/figure-format.md` |
| Citation, literature search, source handling | + `conventions/research.md` (ships opinionated, no template) |
| R or Python coding | + `conventions/code-format.md` |
| Iterative refinement of an analysis | + `conventions/iteration-workflow.md`, `conventions/research-quality-gates.md`, `conventions/visual-review-protocol.md` (all ship opinionated) |
| Updating the framework itself | + `conventions/system-improvement-protocol.md` (ships opinionated) |

These contracts apply to all skills, all agents, all workflows.

## Skill routing

### Simple skills (single-job)

| Skill | Path | Use when |
|-------|------|----------|
| analysis-planning | `skills/simple/analysis-planning/SKILL.md` | designing the statistical or computational approach |
| code-writing | `skills/simple/code-writing/SKILL.md` | writing the code from a plan |
| code-review | `skills/simple/code-review/SKILL.md` | reviewing code for correctness, style, and diagnostics |
| manuscript-writing | `skills/simple/manuscript-writing/SKILL.md` | ad-hoc daily writing without the full pipeline |
| manuscript-builder | `skills/simple/manuscript-builder/SKILL.md` | rendering a Markdown manuscript to journal-ready .docx |
| reply-writing | `skills/simple/reply-writing/SKILL.md` | ad-hoc single-comment reply work |
| reviewer-reply-planning | `skills/simple/reviewer-reply-planning/SKILL.md` | triaging reviewer comments and planning the revision |
| reviewer-reply-drafting | `skills/simple/reviewer-reply-drafting/SKILL.md` | drafting the reply and rendering the tracked-changes manuscript |
| topic-writing | `skills/simple/topic-writing/SKILL.md` | synthesis, perspective, brief, blog post |

### Workflows (multi-phase orchestrators)

| Skill | Path | Use when |
|-------|------|----------|
| analysis-pipeline | `skills/workflows/analysis-pipeline/SKILL.md` | orchestrating the full plan-implement-review loop |
| research-iterate | `skills/workflows/research-iterate/SKILL.md` | iterating a research analysis through quality gates until publication-ready |
| paper-research | `skills/workflows/paper-research/SKILL.md` | full multi-phase pipeline from findings to manuscript draft |
| expert-review | `skills/workflows/expert-review/SKILL.md` | simulated peer-review panel for a draft |
| manuscript-pipeline | `skills/workflows/manuscript-pipeline/SKILL.md` | orchestrating the full manuscript lifecycle |
| reviewer-reply-pipeline | `skills/workflows/reviewer-reply-pipeline/SKILL.md` | orchestrating the full reviewer-reply cycle |

## Agent roster

| Agent | Path | Expertise |
|-------|------|-----------|
| Lab Director | `agents/lab-director.md` | task routing, cross-domain integration, quality assurance |
| Quantitative Scientist | `agents/quantitative-scientist.md` | statistical modelling, ML, R/Python coding, diagnostics |
| Science Writer | `agents/science-writer.md` | literature research, manuscript drafting, expert review |
| Literature Extractor | `agents/literature-extractor.md` | verbatim quantitative extraction from sources with full provenance |
| Extraction Validator | `agents/extraction-validator.md` | source-faithfulness verification |

Add domain-specialist agents under `agents/` as your lab's work requires. Use `agents/_domain-specialist.template.md` as the starting skeleton.

## Knowledge base

The `knowledge_base/` folder is the lab's accumulated thinking, organised by topic. The framework ships with the scaffolding (`SKILL.md`, `GLOBAL-CONCEPTS.template.md`, `_topic.template/`). Populate with topics relevant to your lab.

Use `knowledge_base/SKILL.md` to ingest, compile, query, and maintain the knowledge base. Every ingest operation MUST load `conventions/research.md` and use `agents/literature-extractor.md` plus `agents/extraction-validator.md` for any quantitative extraction. Source faithfulness is non-negotiable.

## Setup and onboarding

If the adopter has not yet populated the templates, point them to `setup/SKILL.md` and `setup/lab-onboarding.html`. The setup skill walks them through an interview that generates lab-specific versions of every `*.template.md` file in `conventions/`, plus stubs for any domain-specialist agents and the first knowledge-base topics.

## Dashboard

A system-state dashboard can be generated via `tools/generate-state.js` and viewed in any browser:

```bash
node tools/generate-state.js
open tools/system-dashboard.html
```

The dashboard reports skill inventory, agent roster, knowledge-base coverage, and convention-population status. Useful for tracking what is set up and what remains.

## Always-load contracts

These files MUST be loaded before relevant tasks, regardless of which workflow or skill is invoked:

- Any writing → `conventions/voice.md` (or template)
- Manuscript work → `conventions/manuscript-format.md` (or template)
- Reviewer reply → `conventions/reply-format.md` (or template)
- Figures or tables → `conventions/figure-format.md` (or template)
- Citations or lit search → `conventions/research.md` (ships opinionated)
- R or Python coding → `conventions/code-format.md` (or template)
- Iteration of an analysis → `conventions/iteration-workflow.md`, `conventions/research-quality-gates.md`, `conventions/visual-review-protocol.md` (all ship opinionated)

If a skill produces written output without loading the relevant conventions, that is a routing failure to flag.

## Skill-firing declaration

At the start of every substantive response, include a single tight line in this format, before any other prose:

```
**Using:** <comma-separated list>
```

List what is actually being invoked for the turn, drawn from:

- Skills (e.g., `manuscript-writing`, `code-review`, `analysis-pipeline`)
- Agents invoked (e.g., `quantitative-scientist`, `lab-director`, `literature-extractor`)
- Conventions loaded (e.g., `voice.md`, `research.md`, `code-format.md`) — always-load contracts plus any others read for this turn
- Knowledge-base articles consulted (e.g., `acoustic-telemetry/detection-efficiency`)
- Workflow phases when a multi-phase skill is active (e.g., `paper-research Phase 2`)

Rules:

- One line, comma-separated. Do not break out into bullets.
- List only what is genuinely in use for this turn, not the full catalogue.
- For trivial replies (one-line clarifications, acknowledgements), still emit the line. At minimum the always-load conventions, or `**Using:** none` if no skill / agent / convention / KB is in play.
- Place the line at the very top of the response.
- Do not mention this declaration mechanism inside the response itself.

The purpose is transparency. The user sees which parts of the framework are being applied to their request, which builds trust, makes it easy to spot when the wrong skill has fired, and helps newcomers learn what the framework is doing under the hood.

## Operating principles

These come from the paper's §3 and apply to every interaction:

1. **The scientist holds source authority.** The model proposes; the scientist disposes. Interpretations, hypotheses, judgments about quality belong to the scientist.
2. **Verify, do not trust.** Treat model outputs as draft-quality input. Reference accuracy, code correctness, and conceptual claims all require verification proportional to stakes.
3. **Externalise what the AI cannot internalise.** Conventions, defaults, voice, and methodological heuristics belong in the framework files rather than in repeated prompting.
4. **Use specialist sub-agents for division of cognitive labour.** Invoke specialists explicitly. Parallel critique catches more errors than serial review.
5. **Treat the knowledge base as compounding infrastructure.** Curate, do not auto-generate. Source faithfulness is non-negotiable.
6. **The framework is non-stationary.** Models and conventions change. Update the files, not the architecture.

## Status

This is v0.2 of the framework. See `README.md` for the roadmap.
