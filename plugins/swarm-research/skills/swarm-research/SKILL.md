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

When calling `swarm_research_start`, pass the user's research question **concisely** — at most one or two sentences of light cleanup or translation.

**Keep** every constraint the user already wrote: dates, quarters, tickers, named entities, ranges, jurisdictions, version numbers. Those are the scope of the research and dropping them silently changes what gets studied.

**Do not add** anything the user did not say:
- no "known data points" / "background" / "context" sections of your own
- no numbers, prices, percentages, exchange rates, market caps, or other figures you sourced from memory
- no expansion into a structured research brief with headings / sub-questions / output requirements

Gathering and verifying that context is what swarm-desktop is for. Anything you stuff into the prompt arg shows up **verbatim** in the user's Cowork UI as the tool input — and any figures you invent there become hallucinated noise the research engine then has to work around.

Good (keeps user-supplied date + entity): `prompt: "研究 小米 2025 年 Q4 智能手机业务"`
Good (keeps user-supplied instrument): `prompt: "10 年期美债收益率走高对比特币的影响"`
Bad (model-fabricated figures bolted on): `prompt: "研究主题: ... 背景与已知数据点: BTC ≈ $79,940, 距 1 月高位 -18% ..."`

## Long Task Etiquette

After `swarm_research_start`, briefly tell the user the run_id and that the run usually takes 5-15 minutes. Poll `swarm_research_status` every 60-120 seconds. When `agents` is present, summarize it naturally. When `status=completed`, call `swarm_research_fetch_report`.

## Output Framing — render verbatim, then stop

The returned markdown is the product the user paid 5-15 minutes for. It is **not** internal scratchpad context for you to summarize from.

When `swarm_research_fetch_report` returns:

1. **Render `response.markdown` verbatim** as your immediate reply. Do not summarize, paraphrase, translate, reorder sections, drop sentences, or wrap it in your own narration. Preserve every `[ev_xxx]` token in place — those are the document's audit trail.
2. **Append a `## 引用 / References` section** built from `response.citations[]`. Each citation becomes a bullet `- [{title}]({url})` (fall back to `{id}` if there is no url). For xAI per-post entries the title already starts with "X post by @handle" — keep it as the link text. This turns the inline `[ev_xxx]` tokens into auditable, clickable sources in the user-visible chat.
3. **Stop.** No "key takeaways", no confidence assessment, no follow-up suggestions, no "what would you like to do next" prompt. The user's natural next move is to read the report and ask you to interpret a specific section — wait for that cue and engage from there.

Showing the full markdown is what makes the run worth running. Summarizing it in the same turn collapses 5-15 minutes of high-density research into a paraphrase the user can't audit.
