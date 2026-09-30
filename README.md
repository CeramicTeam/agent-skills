# Ceramic Agent Skills

Agent Skills and Claude Code / Codex plugins for [Ceramic](https://docs.ceramic.ai) web search.

The `ceramic-search` skill teaches coding agents to turn questions into keyword queries, search the web with Ceramic, and answer with cited sources. It follows the open [Agent Skills](https://agentskills.io) standard, so the same `SKILL.md` works in Claude Code, Codex, Cursor, GitHub Copilot, Windsurf, Gemini CLI, and [other supported agents](https://github.com/vercel-labs/skills#supported-agents).

| Skill | Description |
|-------|-------------|
| [`ceramic-search`](skills/ceramic-search/SKILL.md) | Web search for current information — news, prices, recent events, documentation, and fact checking |

## Choose an install method

Pick **one** install method per agent. Installing both the plugin and the skill in Claude Code or Codex gives the agent two copies of the same skill.

| Method | Agents | Authentication | Calls Ceramic through |
|--------|--------|----------------|-----------------------|
| [Skill (`npx`)](#install-the-skill) | Any supported agent | API key | Ceramic Search API |
| [Claude Code plugin](#install-the-claude-code-plugin) | Claude Code | OAuth in the browser | Ceramic MCP server |
| [Codex plugin](#install-the-codex-plugin) | Codex | OAuth in the browser | Ceramic MCP server |

## Install the skill

Requires [Node.js](https://nodejs.org) and a Ceramic API key.

1. Create an API key at [platform.ceramic.ai/keys](https://platform.ceramic.ai/keys) and set it in your agent's environment:

   ```bash
   export CERAMIC_API_KEY="your_api_key"
   ```

2. Install the skill from your terminal:

   ```bash
   npx skills add CeramicTeam/agent-skills
   ```

   The installer detects the coding agents on your machine and asks where to install the skill.

3. Start a new agent session and ask for something that needs current information from the web.

If your agent also has the Ceramic MCP server configured, the skill uses the MCP tool instead of the API.

## Install the Claude Code plugin

1. Register the marketplace:

   ```bash
   claude plugin marketplace add CeramicTeam/agent-skills
   ```

2. Install the plugin:

   ```bash
   claude plugin install ceramic-search@ceramic-ai
   ```

3. Start a new Claude Code session. On first use, Claude Code opens a browser window to sign in with Ceramic.

If you installed the plugin from `CeramicTeam/ceramic-claude-code-plugins`, run step 1, then `claude plugin update ceramic-search@ceramic-ai` and restart Claude Code.

## Install the Codex plugin

1. Register the marketplace:

   ```bash
   codex plugin marketplace add CeramicTeam/agent-skills
   ```

2. In a Codex session, run `/plugins`, find `ceramic-search` under **Ceramic AI Plugins**, and select **Install plugin**. Codex opens a browser window to sign in with Ceramic.

   If the browser doesn't open, run this from a terminal outside Codex:

   ```bash
   codex mcp login ceramic-search
   ```

3. Start a new Codex session.

If you installed the plugin from `CeramicTeam/ceramic-codex-plugins`, remove that marketplace before step 1 with `codex plugin marketplace remove ceramic-ai`. Your installed plugin updates the next time you start Codex.

## Links

- [Ceramic documentation](https://docs.ceramic.ai)
- [Ceramic API reference](https://docs.ceramic.ai/api-reference/search)
- [Ceramic MCP server](https://docs.ceramic.ai/mcp/ceramic-mcp)
