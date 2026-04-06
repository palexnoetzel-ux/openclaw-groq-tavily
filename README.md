# OpenClaw + Groq + Tavily Web Search

Connect **Groq llama-3.3-70b-versatile** to **OpenClaw** with working **Tavily web search** — including tool routing, agent config templates, and common pitfall fixes.

This setup took several days to figure out. Sharing it so nobody else has to go through the same pain.

---

## What This Gives You

- Groq (llama-3.3-70b-versatile) running inside OpenClaw
- Tavily web search working on Groq via `tavily_search` and `tavily_extract`
- Local model (ollama/qwen2.5:7b) with its own separate tool routing
- TTS auto-off on Groq (prevents tool call failures)
- Clean agent MD files that keep both models behaved
- **VS Code integration via Continue.dev** — Groq for chat, local model for autocomplete

---

## Prerequisites

- [OpenClaw](https://openclaw.ai) installed and running
- [Ollama](https://ollama.ai) with a local model (e.g. qwen2.5:7b)
- A [Groq](https://console.groq.com) account and API key
- A [Tavily](https://app.tavily.com) account and API key

---

## Step 1 — Set Up Groq

```powershell
openclaw models auth paste-token --provider groq
```

Paste your Groq API key when prompted. Then enable the plugin:

```powershell
openclaw plugins enable groq
```

---

## Step 2 — Set Up Tavily

```powershell
openclaw models auth paste-token --provider tavily
```

Paste your `tvly-dev-...` key when prompted. Then enable the plugin:

```powershell
openclaw plugins enable tavily
```

> ⚠️ **Critical:** You must also add the key directly to the plugin config in `openclaw.json` (see Step 3). Storing via `paste-token` alone is not enough — the plugin config block also needs the key explicitly.

---

## Step 3 — Configure openclaw.json

Add the following to your `~/.openclaw/openclaw.json`:

### Tool routing (byProvider)

```json
"tools": {
  "deny": ["image_generate"],
  "alsoAllow": ["tavily_search", "tavily_extract"],
  "byProvider": {
    "system": {
      "allow": ["*"]
    },
    "groq": {
      "deny": [
        "ollama_web_search",
        "ollama_web_fetch",
        "web_search",
        "web_fetch"
      ],
      "allow": ["tavily_search", "tavily_extract"]
    },
    "ollama": {
      "allow": ["*", "ollama_web_search", "ollama_web_fetch"],
      "deny": ["web_search", "web_fetch", "tavily_search", "tavily_extract"]
    }
  },
  "web": {
    "search": {
      "enabled": true,
      "provider": "tavily"
    },
    "fetch": {
      "enabled": true
    }
  }
}
```

### Tavily plugin config (with API key)

```json
"plugins": {
  "entries": {
    "tavily": {
      "enabled": true,
      "config": {
        "webSearch": {
          "apiKey": "tvly-dev-YOURKEY",
          "baseUrl": "https://api.tavily.com"
        }
      }
    }
  }
}
```

### Model aliases

```json
"agents": {
  "defaults": {
    "model": {
      "primary": "ollama/qwen2.5:7b",
      "fallbacks": ["groq/llama-3.3-70b-versatile"]
    },
    "models": {
      "ollama/qwen2.5:7b": { "alias": "local" },
      "groq/llama-3.3-70b-versatile": { "alias": "pro" }
    }
  }
}
```

---

## Step 4 — Restart

```powershell
openclaw gateway restart
```

---

## Step 5 — Test

Switch to Groq:
```
/model groq/llama-3.3-70b-versatile
```

Test Tavily search:
```
What is the current price of Bitcoin?
```

You should see `tavily_search` fire in the tool call log with no validation errors.

---

## Agent MD Files

See the `/templates` folder for working `AGENTS.md` and `TOOLS.md` files.

Key rules that must be in your `AGENTS.md` for Groq to behave:

```markdown
## CRITICAL FOR GROQ
- MD files are already in your context — NEVER use read tool to fetch them
- NEVER use write, exec, read, file, memory or TTS tools
- NEVER use ollama_web_search, ollama_web_fetch, web_search, web_fetch
- Web search on Groq: use tavily_search or tavily_extract ONLY
- For coding: paste code directly in the response
- For file access: tell user to switch to /local
```

---

## Common Pitfalls

| Problem | Cause | Fix |
|---|---|---|
| `tool call validation failed: tavily_search not in request.tools` | Tavily not in `alsoAllow` or `byProvider.groq.allow` | Add to both in `openclaw.json` |
| `Tavily API key not found` | Key only in auth-profiles, not in plugin config | Add `apiKey` directly to plugin config block |
| `media.tts unknown tool warning` | `media.tts` used as tool identifier in byProvider | Remove `media.tts` from all allow/deny lists |
| Groq tries to call `read` tool on startup | No rule telling it MD files are already in context | Add rule to AGENTS.md: MD files already in context |
| Brave intercepting web_search before Tavily | Brave plugin enabled without API key | Set `"brave": { "enabled": false }` |
| TTS tool call failures on Groq | TTS being passed to Groq in request.tools | Deny `media.tts` for Groq in byProvider |

---

## VS Code Integration (Continue.dev)

Use Groq for chat and local model for autocomplete directly inside VS Code.

### Step 1 — Install Continue

Open VS Code Extensions, search for `Continue - Codestral, Claude, and more` by Continue.dev and install it.

### Step 2 — Configure

Open the Continue config file (`~/.continue/config.yaml` on Mac/Linux, `%USERPROFILE%\.continue\config.yaml` on Windows) and add:

```yaml
name: Local Config
version: 1.0.0
schema: v1
models:
  - name: OpenClaw Groq
    provider: openai
    model: groq/llama-3.3-70b-versatile
    apiBase: http://127.0.0.1:18789/v1
    apiKey: any
    roles:
      - chat
      - edit

  - name: OpenClaw Local
    provider: openai
    model: ollama/qwen2.5:7b
    apiBase: http://127.0.0.1:18789/v1
    apiKey: any
    roles:
      - autocomplete
```

See `continue-config.yaml` in this repo for the ready-to-use file.

### How it works

- **Groq** handles chat and code editing in the Continue panel
- **Local qwen** handles autocomplete suggestions as you type
- OpenClaw must be running at `http://127.0.0.1:18789`
- No extra API keys needed in VS Code — everything routes through OpenClaw

---



MIT — use freely, share improvements.

---

## Credits

Figured out by Alex ([@palexnoetzel-ux](https://github.com/palexnoetzel-ux)) through two days of debugging OpenClaw internals.
