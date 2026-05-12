# claude-qa-mcp

**A Claude Code MCP config for QA engineers.** Drop the file in, fill in your TestRail credentials, and you're running with Miro, Atlassian (Jira + Confluence), TestRail, and Playwright in one shot. Figma walkthrough included below.

> 𝟮-𝟯 𝗱𝗮𝘆𝘀 → 𝟱-𝟲 𝗵𝗼𝘂𝗿𝘀. That's the workflow this config came out of. Full story on LinkedIn: [LINKEDIN_POST_URL]

## Quick start

```bash
git clone https://github.com/demian-v/claude-qa-mcp
cd claude-qa-mcp
```

Then pick one of:

- **Global**: merge the `mcpServers` block from `.mcp.json` into `~/.claude.json`
- **Per-project**: copy `.mcp.json` into your project root

Fill in the 3 TestRail placeholders, restart Claude Code, run `/mcp` to verify all 4 servers are listed.

## After install

Running `/mcp` inside Claude Code shows:

```
mcp servers
├── miro          ✓ ready    (browser OAuth on first call)
├── atlassian     ✓ ready    (browser OAuth on first call)
├── testrail      ✓ ready    (env credentials)
├── playwright    ✓ ready    (no auth)
└── figma         install separately — see below
```

## What's in the file

| MCP | What it does for QA | Auth |
|---|---|---|
| **Miro** | Read flow diagrams, process maps | Browser OAuth on first use |
| **Atlassian** (Jira + Confluence) | Pull tickets, specs, acceptance criteria | Browser OAuth on first use |
| **TestRail** | Read / create / update test cases and sections | API key + URL + username (env vars) |
| **Playwright** | Drive a real browser; draft test automation | None |

## Figma (added separately)

Figma isn't in `.mcp.json` — there's no public HTTPS endpoint to drop in. Install it through Claude AI's MCP catalog:

1. Open https://claude.ai/mcp
2. Find Figma in the catalog, click **Connect**
3. Approve the OAuth scopes in your browser
4. Restart Claude Code; `/mcp` should now list `figma`

This adds Figma to Claude's hosted MCP layer — it works in Claude Code automatically alongside the servers in `.mcp.json`.

## Common workflows

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

## What you get

- Every requirement (Miro + Atlassian + Figma) in one AI session instead of four tabs
- TestRail MCP writes drafts directly into the right section
- Playwright automation drafts refined through manual review

## Honest scope

- This file gets you 4 of 5 MCPs working. Figma needs the catalog step above.
- AI alone won't write the test cases you need. The MCP layer plus project-specific context (skills, memory plugins, memory tools) is where the time savings come from. More on that layer in the LinkedIn series.

## Security

- The `.mcp.json` in this repo contains placeholders only — no real credentials
- Never commit your filled-in `.mcp.json` to a public repo
- Add `.mcp.json` to your project's `.gitignore` if you drop it into a project root
- TestRail API keys carry the same blast radius as your account — create a dedicated key, not a personal one

## License

[MIT](LICENSE) — use freely, no warranty.

## Found this useful?

LinkedIn post: [LINKEDIN_POST_URL] · Built by [Demian Vyrozub](https://github.com/demian-v)
