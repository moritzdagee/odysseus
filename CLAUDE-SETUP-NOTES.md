# Odysseus — Setup & Undo Notes (installed by Claude, 2026-06-06)

Plain-language record of everything that was set up on this Mac, plus how to
undo it. Keep this file; it is the audit trail.

## What this is
Odysseus = PewDiePie's self-hosted AI workspace. Installed from the GENUINE
source only: https://github.com/pewdiepie-archdaemon/odysseus
(A social-media post advertised a fake clone at `felixkjellberg/odysseus` with a
`./run.sh` — that was an impersonation and was NEVER used.)

## Where things live
- App code: `/Users/moritzcremer/odysseus`
- Self-contained Python env: `/Users/moritzcremer/odysseus/venv`
- User data (chats, memory, settings): `/Users/moritzcremer/odysseus/data`
- Web address (local only): http://127.0.0.1:7860
- Login: user `admin` (temporary password was printed at first setup; change it in Settings)

## Installed via Homebrew (system tools)
- `python@3.11` (stable Python the app runs on — system 3.14 was deliberately avoided)
- `tmux` (used for background model serving)
- `llama.cpp` (local model server, Metal/GPU)

## Always-on background services (macOS LaunchAgents)
Two services, kept alive + auto-started at login by macOS:

1. `ai.odysseus.chromadb` — the smart-memory database (ChromaDB) on port 8100
   - Plist: `/Users/moritzcremer/Library/LaunchAgents/ai.odysseus.chromadb.plist`
   - Logs: `/tmp/odysseus-chromadb.{out,err}.log`
2. `ai.odysseus.ui` — the Odysseus app itself on port 7860
   - Plist: `/Users/moritzcremer/Library/LaunchAgents/ai.odysseus.ui.plist`
   - Runs `start-macos.sh`; browser auto-open disabled (ODYSSEUS_NO_OPEN=1)
   - Logs: `/tmp/odysseus-ui.{out,err}.log`

## AI models (via Ollama — managed separately by the Ollama app)
Connected to Odysseus as endpoint id `64090c92` ("Ollama (local)").
Default chat model set to `llama3.1:8b` (comfortable on 16 GB RAM).
Available: llama3.1:8b, gemma4:12b, deepseek-r1:8b, deepseek-r1:14b, qwen3:14b.

## Manage the services
```bash
U=$(id -u)
# restart app:        launchctl kickstart -k gui/$U/ai.odysseus.ui
# restart memory DB:  launchctl kickstart -k gui/$U/ai.odysseus.chromadb
# stop (this session):launchctl bootout gui/$U/ai.odysseus.ui
# status:             launchctl list | grep odysseus
```

## Full uninstall (reverse everything)
```bash
U=$(id -u)
launchctl bootout gui/$U/ai.odysseus.ui 2>/dev/null
launchctl bootout gui/$U/ai.odysseus.chromadb 2>/dev/null
rm /Users/moritzcremer/Library/LaunchAgents/ai.odysseus.ui.plist
rm /Users/moritzcremer/Library/LaunchAgents/ai.odysseus.chromadb.plist
rm -rf /Users/moritzcremer/odysseus      # removes app + all local data
# Optional (only if you don't want them anymore):
#   brew uninstall llama.cpp tmux python@3.11
# Ollama + its models are managed by the Ollama app, untouched by this.
```

## MCP connectors added to Odysseus (2026-06-06)
- **Assistant_Data** (the CURRENT knowledge base) — connected, 20 tools.
  - Transport: Streamable HTTP → `https://127.0.0.1:8765/mcp`
    (service `com.assistant_data.mcp-http`, port 8765, Postgres-backed).
  - The OLD stdio connector `assistantdata-knowledge`
    (`/usr/bin/python3 .../assistant_data/src/mcp_server/server.py`) was
    registered by mistake and REMOVED — it is deprecated, do not use it.
  - TLS: the :8765 service uses an mkcert certificate. Odysseus rejected it
    until the mkcert root CA was added to its trust bundle:
    - Appended `~/Library/Application Support/mkcert/rootCA.pem` to
      `venv/lib/python3.11/site-packages/certifi/cacert.pem`
    - Backup of the original bundle:
      `venv/lib/python3.11/site-packages/certifi/cacert.pem.bak.preassistantdata`
    - If a package update ever wipes this, re-append the mkcert root and restart.
- **neon-browser** (`https://mcp.neon.opera.com/mcp`) — registered but NOT
  connected: needs an interactive OAuth sign-in (and its callback assumes
  port 7000). Left in place as `needs_auth`; safe to delete if unwanted.

Note: the many other connectors visible in Claude/Cowork (Gmail, Slack,
Notion, etc.) are provided by that environment's own logins and are NOT stored
locally, so they cannot be copied — Odysseus must sign into each itself.
