# swarm-desktop Claude Plugins

Install the swarm-research connector into Claude Desktop, Cowork, or claude.ai.

## One-Time Setup

```text
/plugin marketplace add github:bknerd2013/claude-plugins
/plugin install swarm-research@swarm-desktop
```

On first use, Claude opens an OAuth login with your swarm-desktop account. Approve once and then run research from Claude.

## Custom Connector

Without the plugin, add this connector URL manually:

```text
https://mcp.go2look.cn/mcp
```

The bare connector exposes the same tools and resources; the plugin adds trigger rules for prompts like `研究 X`.

## Contents

- `.claude-plugin/marketplace.json`
- `plugins/swarm-research/.claude-plugin/plugin.json`
- `plugins/swarm-research/.mcp.json`
- `plugins/swarm-research/skills/swarm-research/SKILL.md`
