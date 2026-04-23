# DevVault — Setup Guide

Complete setup for a new machine, start to finish.

---

## 1. Install Obsidian

Download and install Obsidian from [obsidian.md](https://obsidian.md).

- **macOS/Windows:** Use the installer from the downloads page.
- **Linux:** AppImage, Flatpak, or Snap available on the downloads page.

---

## 2. Install Local REST API Plugin

1. Open Obsidian.
2. Go to **Settings** (gear icon, bottom-left).
3. Click **Community Plugins** in the left sidebar.
4. If prompted, click **Turn on community plugins**.
5. Click **Browse**.
6. Search for `Local REST API` → find the one by **coddingtonbear** → click **Install** → click **Enable**.
7. Go back to **Settings → Local REST API**.
8. Copy your **API Key** — you'll need it in step 5.
9. Confirm the plugin is listening on **HTTPS port 27124** (default). If you change the port, update `OBSIDIAN_BASE_URL` accordingly.

> Obsidian must be **running and the plugin enabled** whenever an agent performs vault operations.

---

## 3. Create the Vault

Create a new Obsidian vault at `~/Documents/DevVault` (or any path you prefer — update the MCP config in step 5 if using a different path).

Inside the vault, create this folder structure manually or let the skills create it on first use:

```
~/Documents/DevVault/
├── Projects/
│   ├── projects.md      ← create as empty file
│   └── status.md        ← create as empty file
└── Templates/
```

---

## 4. Clone This Repo

```bash
git clone git@github.com:ZidanHadipratama/DevVaults.git ~/app/DevVault
```

Or clone to any path — the repo contains only skills, docs, and config. The vault lives separately in `~/Documents/DevVault`.

---

## 5. Configure `~/.claude.json`

Add the `obsidian` MCP server entry. If `~/.claude.json` already exists with other MCP servers, merge the `obsidian` key into `mcpServers` — do not overwrite the file.

```json
{
  "mcpServers": {
    "obsidian": {
      "command": "npx",
      "args": ["-y", "@cyanheads/obsidian-mcp-server"],
      "env": {
        "OBSIDIAN_API_KEY": "your-api-key-here",
        "OBSIDIAN_BASE_URL": "https://127.0.0.1:27124/"
      }
    }
  }
}
```

Replace `your-api-key-here` with the key from step 2.

**Safe merge (preserves existing MCP servers):**

```bash
python3 - <<'EOF'
import json, os

path = os.path.expanduser("~/.claude.json")
config = json.load(open(path)) if os.path.exists(path) else {}
config.setdefault("mcpServers", {})["obsidian"] = {
    "command": "npx",
    "args": ["-y", "@cyanheads/obsidian-mcp-server"],
    "env": {
        "OBSIDIAN_API_KEY": "your-api-key-here",
        "OBSIDIAN_BASE_URL": "https://127.0.0.1:27124/"
    }
}
json.dump(config, open(path, "w"), indent=2)
print("Done.")
EOF
```

---

## 6. Install skill-creator Plugin

```bash
claude plugins install skill-creator
```

**Then restart Claude Code.** The `skill-creator` plugin does not appear in available skills until after a session restart.

To verify it's installed:

```bash
claude plugins list
```

You should see `skill-creator` in the output.

---

## 7. Verify Setup

Make sure Obsidian is running with the Local REST API plugin enabled, then open a Claude Code session in the repo directory:

```bash
cd ~/app/DevVault
claude
```

**Test MCP connection:**

```
/mcp
```

You should see `obsidian` listed and connected.

**Test skill retrieval (if IWantJob is already indexed):**

```
/retrieve-feature IWantJob llm-routing
```

Expected output: note summary + key components table + raw code excerpt + adaptation plan.

**If IWantJob is not indexed yet, test analyze instead:**

```
/analyze-project ~/app/DevVault
```

Expected output: project index draft + feature note drafts in conversation.

---

## 8. Troubleshooting

### MCP unreachable — `obsidian` shows disconnected or error

**Cause:** Obsidian is not running, or Local REST API plugin is disabled.

**Fix:**
1. Open Obsidian.
2. Go to Settings → Community Plugins → confirm Local REST API is enabled (toggle is blue).
3. Run `/mcp` in Claude Code again.

If still failing, check the plugin is on port 27124:
- Settings → Local REST API → confirm port matches `OBSIDIAN_BASE_URL` in `~/.claude.json`.

### API key rejected — 401 Unauthorized

**Cause:** `OBSIDIAN_API_KEY` in `~/.claude.json` doesn't match the key in Obsidian.

**Fix:**
1. Obsidian → Settings → Local REST API → copy the API Key.
2. Update `~/.claude.json` → `mcpServers.obsidian.env.OBSIDIAN_API_KEY`.
3. Restart Claude Code to reload MCP config.

### Port mismatch — connection refused or wrong endpoint

**Cause:** Plugin defaulted to port 27123 (HTTP) instead of 27124 (HTTPS), or you changed the port.

**Fix:**
1. Obsidian → Settings → Local REST API → note the actual port and protocol (HTTP vs HTTPS).
2. Update `OBSIDIAN_BASE_URL` in `~/.claude.json` to match exactly, e.g.:
   - HTTPS port 27124: `"https://127.0.0.1:27124/"`
   - HTTP port 27123: `"http://127.0.0.1:27123/"`
3. Restart Claude Code.
