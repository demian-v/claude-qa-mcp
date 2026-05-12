# claude-qa-mcp

<p align="center">
  <img src="https://img.shields.io/badge/Claude_Code-000?style=flat&logo=anthropic&logoColor=white" alt="Claude Code">
  <img src="https://img.shields.io/badge/MCP-Model_Context_Protocol-7C3AED?style=flat" alt="MCP">
  <img src="https://img.shields.io/badge/Config-JSON-F7DF1E?style=flat&logo=json&logoColor=black" alt="JSON">
  <img src="https://img.shields.io/badge/License-MIT-blue.svg" alt="MIT">
</p>

<p align="center">
  <em>Test documentation chases requirements across Miro, Jira, Confluence, Figma, and TestRail.</em><br>
  <strong>This config wires Claude Code into all of them — 2-3 days of docs land in 5-6 hours.</strong>
</p>

---

## What Is This

`claude-qa-mcp` is a drop-in MCP server config for Claude Code, tailored for QA engineers. It connects the five tools where QA context lives — requirements, designs, tickets, test cases, automation — so Claude Code can read and write across all of them in a single session.

What it does:

- **Wires up 4 MCPs in one file** — Miro, Atlassian (Jira + Confluence), TestRail, and Playwright
- **Triggers browser OAuth on first use** for Miro and Atlassian — no API keys to manage
- **Uses 3 env placeholders** for TestRail — URL, username, API key — that's it
- **Walks you through Figma** separately via Claude AI's MCP catalog (the 5th MCP, not a drop-in)
- **Includes example prompts** that exercise the full stack end-to-end

## What You Get

| MCP | What it does for QA | Auth |
|---|---|---|
| **Miro** | Read flow diagrams, process maps | Browser OAuth on first use |
| **Atlassian** (Jira + Confluence) | Pull tickets, specs, acceptance criteria | Browser OAuth on first use |
| **TestRail** | Read / create / update test cases and sections | API key + URL + username (env vars) |
| **Playwright** | Drive a real browser; draft test automation | None |

After install, `/mcp` inside Claude Code shows:

```
mcp servers
├── miro          ✓ ready    (browser OAuth on first call)
├── atlassian     ✓ ready    (browser OAuth on first call)
├── testrail      ✓ ready    (env credentials)
├── playwright    ✓ ready    (no auth)
└── figma         install separately — see below
```

## Quick Start

**1. Clone the repo**

```bash
git clone https://github.com/demian-v/claude-qa-mcp
cd claude-qa-mcp
```

**2. Drop the config — pick one**

- **Global**: merge the `mcpServers` block from `.mcp.json` into `~/.claude.json`
- **Per-project**: copy `.mcp.json` into your project root

**3. Fill in the TestRail placeholders** in your copy:

```json
"TESTRAIL_URL": "https://yourcompany.testrail.io",
"TESTRAIL_USERNAME": "you@yourcompany.com",
"TESTRAIL_API_KEY": "your-api-key-here"
```

**4. Restart Claude Code**, then run `/mcp` to confirm all 4 servers are listed.

## Figma (added separately)

Figma isn't in `.mcp.json` — there's no public HTTPS endpoint to drop in. Install it through Claude AI's MCP catalog:

1. Open https://claude.ai/mcp
2. Find Figma in the catalog, click **Connect**
3. Approve the OAuth scopes in your browser
4. Restart Claude Code; `/mcp` should now list `figma`

This adds Figma to Claude's hosted MCP layer — it works in Claude Code automatically alongside the servers in `.mcp.json`.

## Common Workflows

Prompts that exercise the full stack:

```
"Read Jira ticket QA-123, pull the linked Figma frame, and draft TestRail cases
for the new checkout flow under section 'Payments / Checkout'."
```

```
"Look at this Miro board, find every payment branch, and create a TestRail
test case for each one in the 'Payment flows' section."
```

```
"Pull the acceptance criteria from this Confluence page, draft Playwright tests
covering the happy path, and save them under tests/checkout/."
```

The pattern: pull context from Miro/Atlassian/Figma → AI drafts → TestRail or Playwright lands the output.

<details>
<summary><strong>How to get your TestRail API key</strong></summary>

1. In TestRail, click your avatar → **My Settings**
2. Open the **API Keys** tab
3. Click **Add Key**, give it a name (e.g. `claude-code`)
4. Copy the generated key — it's only shown once
5. Paste it into `.mcp.json` as `TESTRAIL_API_KEY`

`TESTRAIL_URL` is your TestRail instance URL (e.g. `https://yourcompany.testrail.io`). `TESTRAIL_USERNAME` is the email you log in with.

</details>

<details>
<summary><strong>Prerequisites</strong></summary>

- [Claude Code](https://claude.com/claude-code)
- `uvx` for the TestRail MCP — install via `pip install uv` or `brew install uv`
- `node` + `npx` for the Playwright MCP — comes with Node.js
- A modern browser (for the Miro and Atlassian OAuth flows)

</details>

<details>
<summary><strong>Atlassian Cloud vs Server</strong></summary>

The official Atlassian Remote MCP works with Atlassian Cloud instances (`*.atlassian.net`). Self-hosted Atlassian Server / Data Center isn't supported by this remote endpoint — those need a different setup.

</details>

## Honest Scope

- This file gets you 4 of 5 MCPs working. Figma needs the catalog step above.
- AI alone won't write the test cases you need. The MCP layer plus project-specific context (skills, memory plugins, memory tools) is where the time savings come from. More on that layer in the LinkedIn series.

## Security

- The `.mcp.json` in this repo contains placeholders only — no real credentials
- Never commit your filled-in `.mcp.json` to a public repo
- Add `.mcp.json` to your project's `.gitignore` if you drop it into a project root
- TestRail API keys carry the same blast radius as your account — create a dedicated key, not a personal one

## Project Structure

```
claude-qa-mcp/
├── .mcp.json     # 4 MCP servers — drop into ~/.claude.json or project root
├── README.md     # this file
├── .gitignore
└── LICENSE       # MIT
```

## Tech Stack

- **Agent**: Claude Code with MCP servers (`/mcp`)
- **Config format**: JSON — mergeable into `~/.claude.json` or per-project `.mcp.json`
- **Transports**: HTTP (Miro, Atlassian) + stdio (TestRail via `uvx`, Playwright via `npx`)
- **Authentication**: Browser OAuth (Miro, Atlassian, Figma) + API key in env (TestRail)

## About the Author

I'm Demian — Senior QA Engineer with 5+ years in e-commerce SaaS, test automation, and AI-assisted workflows. I built this config from my own QA workflow and packaged the methodology so others can drop it in.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/demian-vyrozub)

## License

[MIT](LICENSE) — use freely, no warranty.
