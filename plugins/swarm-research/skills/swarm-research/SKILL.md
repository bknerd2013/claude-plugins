---
name: swarm-research
description: Trigger high-density multi-agent research via the swarm-desktop connector. Use when the user starts a work session that needs deep research on a company, industry, investment thesis, regulatory area, or any open-ended evidence-heavy question. Especially trigger when the user message begins with "研究" or "research" or explicitly asks for a research report.
---

# swarm-research

## Trigger Rules

- User message begins with `研究`: treat the rest as the research question and invoke `swarm_research_start`.
- User message begins with `research:` or `do research on`: same.
- User explicitly asks for a report, 深度分析, 调研, or 尽调 on a named entity: use `swarm_research_start`.
- User asks 上次研究的 X or the X report we did: use `swarm_research_list_archive`, then `swarm_research_get_archived`.

## Do Not Trigger

- Simple factual lookup. Use ordinary web search instead.
- Refining or following up on a previous report. Use the report already in context and reason directly in the conversation.

## Prompt Shape (important — affects what the user sees)

When calling `swarm_research_start`, pass the user's research question **concisely** — at most one or two sentences of light cleanup or translation. Do **not**:

- pre-load background, context, or "known data points" sections of your own
- quote specific numbers, prices, dates, percentages, or any other figures
- expand the question into a structured research brief

Gathering and verifying that context is what swarm-desktop is for. Anything you stuff into the prompt arg shows up **verbatim** in the user's Cowork UI as the tool input — and any figures you invent there become hallucinated noise the research engine then has to work around.

Good: `prompt: "10 年期美债收益率走高对比特币的影响"`
Bad: `prompt: "研究主题: ... 背景与已知数据点: BTC ≈ $79,940, 距 1 月高位 -18% ..."`

## Long Task Etiquette

After `swarm_research_start`, briefly tell the user the run_id and that the run usually takes 5-15 minutes. Poll `swarm_research_status` every 60-120 seconds. When `agents` is present, summarize it naturally. When `status=completed`, call `swarm_research_fetch_report`.

## Output Framing

The returned markdown is high-density research context. Treat it as foundational input for downstream reasoning, writing, and decision support.
