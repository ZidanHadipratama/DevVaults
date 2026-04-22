# MCP Setup — obsidian-mcp-server

## Status
Config written to `~/.claude.json`. **API key is a placeholder — fill it before running task 1.4.**

---

## Step 1: Install Obsidian Local REST API Plugin

1. Open Obsidian
2. Go to **Settings → Community plugins**
3. Toggle "Turn on community plugins" if not already on
4. Click **Browse**, search for **"Local REST API"** (by coddingtonbear)
5. Install and **Enable** it

---

## Step 2: Get Your API Key

1. In Obsidian: **Settings → Local REST API**
2. Copy the **API Key** shown there
3. Note the port (default: `27123`)

---

## Step 3: Fill the Placeholder in `~/.claude.json`

Find this section in `~/.claude.json`:

```json
"mcpServers": {
  "obsidian": {
    "command": "npx",
    "args": ["-y", "obsidian-mcp-server"],
    "env": {
      "OBSIDIAN_API_KEY": "REPLACE_WITH_YOUR_LOCAL_REST_API_KEY",
      "OBSIDIAN_BASE_URL": "http://127.0.0.1:27123"
    }
  }
}
```

Replace `REPLACE_WITH_YOUR_LOCAL_REST_API_KEY` with your actual key.

---

## Step 4: Verify MCP Works

1. **Keep Obsidian open** (plugin only works while Obsidian is running)
2. Restart Claude Code
3. Run `/mcp` in Claude Code — you should see `obsidian` listed as connected
4. Test: ask Claude to list vault contents

---

## Gotchas

- **Obsidian must be running** — REST API plugin is inactive when Obsidian is closed
- **HTTP only** — use `http://127.0.0.1:27123` (port 27123), not HTTPS
- **Vault path not needed** — Obsidian resolves vault location internally
- **Restart required** — changes to `~/.claude.json` need Claude Code restart to take effect
- **Large vaults** — if vault grows large (4000+ notes), context window limits may apply

---

## Quick Verification Command

After setup, ask Claude:
> "Use the obsidian MCP to list files in Projects/"

If it returns a file list, MCP is working.
