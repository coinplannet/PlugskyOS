# Plugsky Web — Self-Hosted AI Chat UI

> Run Open WebUI on your machine, fully configured to talk to Plugsky.
> One command to install, one command to start, one command to stop.

## What is it?

Plugsky Web is a thin shim around [Open WebUI](https://github.com/open-webui/open-webui) — the
most popular self-hosted AI chat interface (143k+ ⭐). The shim:

- Downloads/runs the **official, unmodified** Open WebUI build (Docker image or pip package)
- Configures it end-to-end to use Plugsky as the OpenAI-compatible backend
- Manages the server lifecycle (`start` / `stop` / `restart` / `status` / `logs` / `update` / `uninstall`)
- Exposes a single `plugsky-web` command on your `$PATH`

You get a beautiful, mature, feature-rich AI chat UI in your browser at
`http://localhost:8080` — with Plugsky as the backend, all 31 models pre-listed, and
your API key wired in.

## ⚖️ License note

Open WebUI is licensed under the [Open WebUI License](https://github.com/open-webui/open-webui/blob/main/LICENSE),
which **prohibits rebranding** of the "Open WebUI" name/logo beyond 50-user deployments.
This installer respects that — Open WebUI keeps its own branding. The "Plugsky" branding
lives in:

- The shim command name (`plugsky-web`)
- The default model and API URL (config in `~/.plugsky-web/.env`)
- The WELCOME.txt and docs

If you need a fully rebranded chat UI for >50 users, contact Open WebUI Inc. for an
enterprise license, or use the Jan-based [Plugsky Desktop](../install) instead.

## Quick start

```bash
# 1. Install
curl -fsSL https://plugsky.com/install-web | bash

# 2. Get a Plugsky API key at https://plugsky.com/dashboard, then:
export PLUGSKY_API_KEY=sk-live-...

# 3. Start
plugsky-web start

# → Opens http://localhost:8080 in your browser
# → First user to sign up becomes admin
```

Or one-shot:

```bash
curl -fsSL https://plugsky.com/install-web | bash -s -- --api-key sk-live-...
plugsky-web start
```

## How the shim works

```
┌──────────────────────────────────────────────────────────────────┐
│                        User's machine                            │
│                                                                  │
│   ┌────────────────┐    spawn     ┌─────────────────────────┐   │
│   │  plugsky-web   │ ───────────▶ │  Open WebUI              │   │
│   │   (shim)       │              │  (Docker or pip)         │   │
│   └────────────────┘              │                          │   │
│        │                         │  http://localhost:8080   │   │
│        │ reads                   └──────────┬───────────────┘   │
│        ▼                                    │                    │
│   ~/.plugsky-web/                          │ HTTPS               │
│   ├── .env              (PLUGSKY_API_KEY)   │ OpenAI-compat       │
│   ├── backend           (docker | pip)      ▼                    │
│   ├── bin/                                ┌─────────────────┐   │
│   │   └── plugsky-web                     │  api.plugsky.com│   │
│   ├── data/            (pip mode only)    │  /v1/chat/...   │   │
│   ├── logs/                              └─────────────────┘   │
│   └── plugsky-web.pid                                       │
└──────────────────────────────────────────────────────────────────┘
```

The shim is a single bash script (`~/.plugsky-web/bin/plugsky-web`, ~330 lines) that:

1. Loads your API key from `~/.plugsky-web/.env` (mode 600)
2. Spawns the Open WebUI runtime (Docker container or `open-webui serve` process) with the right env vars:
   - `OPENAI_API_BASE_URL=https://api.plugsky.com/v1`
   - `OPENAI_API_KEY=$PLUGSKY_API_KEY`
   - `ENABLE_OPENAI_API=True`
   - `DEFAULT_MODELS=plugsky-pro`
3. Waits for the server to respond on `http://localhost:8080` (up to 60s)
4. Opens your browser to the URL

## Backends

The installer auto-detects the best available backend. Override with `--backend`.

### Docker (preferred)

- **Pros:** Cleanest isolation, smallest host footprint, fastest start, easiest to nuke
- **Cons:** Requires Docker Desktop (macOS/Windows) or dockerd (Linux)
- **Install:** Comes with the [Docker Desktop installer](https://www.docker.com/products/docker-desktop) for macOS/Windows, or `curl -fsSL https://get.docker.com | sh` on Linux
- **What happens:** Pulls `ghcr.io/open-webui/open-webui:main` (~1.5 GB) into a Docker volume for persistence

### pip (fallback)

- **Pros:** No Docker required
- **Cons:** Slower first start (~10s to import), ~500 MB of Python deps in a venv
- **Requires:** Python **3.11** or **3.12** (3.13+ is NOT supported by open-webui as of 0.9.6)
- **Install:**
  - macOS:  `brew install python@3.12`
  - Linux:  `sudo apt install python3.12` (or your distro's equivalent)
  - Any:    `pyenv install 3.12 && pyenv global 3.12`
- **What happens:** Creates `~/.plugsky-web/venv/`, runs `pip install open-webui` there, then `open-webui serve` from that venv

If you have both, the installer picks Docker.

If you have neither:

```
✗ No compatible Python found (open-webui needs 3.11 or 3.12).
  Install one of:
    macOS:  brew install python@3.12
    Linux:  sudo apt install python3.12
    Any:    pyenv install 3.12 && pyenv global 3.12
  Or pass --python /path/to/python3.12 to use a specific binary.
```

## Commands

After install, `plugsky-web` is on your `$PATH`.

| Command | What it does |
|---|---|
| `plugsky-web start` | Start the server in the background, wait until ready, open browser |
| `plugsky-web stop` | Stop the server (kills the Docker container or the `open-webui serve` process) |
| `plugsky-web restart` | Stop + start (e.g. after editing `~/.plugsky-web/.env`) |
| `plugsky-web status` | Show whether it's running, on which port, with which backend |
| `plugsky-web logs [N]` | Tail the last N lines of logs (default: 50). Docker: `docker logs`. pip: `~/.plugsky-web/logs/open-webui.log` |
| `plugsky-web open` | Open `http://localhost:8080` in your default browser |
| `plugsky-web update` | Pull the latest Docker image (or `pip install --upgrade open-webui`) |
| `plugsky-web uninstall` | Stop + remove the container/volume (Docker), the venv (pip), the env file, and the shim |
| `plugsky-web help` | Show this command list |

## Configuration

All config lives in `~/.plugsky-web/.env` (mode 600):

```bash
PLUGSKY_API_KEY=sk-live-...
PLUGSKY_PORT=8080
API_BASE_URL=https://api.plugsky.com/v1
DEFAULT_MODEL=plugsky-pro
EMBED_MODEL=plugsky-embed
```

After editing, restart:

```bash
plugsky-web restart
```

### Change the port

The Open WebUI container's internal port is always 8080, but the **host** port is
configurable. The shim maps `$PLUGSKY_PORT:8080` for Docker. To change the host port:

```bash
# Option 1: re-run the installer
curl -fsSL https://plugsky.com/install-web | bash -s -- --port 9000

# Option 2: edit .env + restart
echo "PLUGSKY_PORT=9000" >> ~/.plugsky-web/.env
plugsky-web restart
```

### Change the default model

Edit `~/.plugsky-web/.env` and set `DEFAULT_MODEL=plugsky-max` (or any other model).
For a **multi-model picker** (recommended), set a comma-separated list:

```bash
DEFAULT_MODEL="plugsky-pro,plugsky-max,plugsky-frontier,plugsky-micro"
plugsky-web restart
```

All 31 Plugsky models are pre-listed in the model picker even without configuring
`DEFAULT_MODELS` — Open WebUI fetches them from `GET /v1/models` on first load.

### Override the API base URL

For self-hosted / private Plugsky deployments, set `API_BASE_URL`:

```bash
API_BASE_URL=https://plugsky.internal.example.com/v1
plugsky-web restart
```

## Data persistence

| Mode | Where your data lives | How to back up |
|---|---|---|
| Docker | Docker volume `open-webui-data` (visible via `docker volume ls`) | `docker run --rm -v open-webui-data:/data -v $(pwd):/backup alpine tar czf /backup/open-webui-data.tar.gz /data` |
| pip | `~/.plugsky-web/data/` (chats, accounts, settings, RAG vector DB) | `tar czf open-webui-backup.tgz ~/.plugsky-web/data` |

**Tip:** Back up before any `plugsky-web update` — a new version may include migrations.

## Troubleshooting

### "open-webui needs 3.11 or 3.12" on install

Your `python3` is 3.13+ (or 3.10 or older). The fix:

```bash
# macOS (Homebrew)
brew install python@3.12
# Then re-run the installer; it auto-detects /opt/homebrew/bin/python3.12

# Linux (apt)
sudo apt install python3.12 python3.12-venv

# Or pass a specific binary
curl -fsSL https://plugsky.com/install-web | bash -s -- --python /path/to/python3.12

# Or use Docker instead (no Python needed)
curl -fsSL https://plugsky.com/install-web | bash -s -- --backend docker
```

### "Docker is installed but the daemon is not running"

Start Docker Desktop (macOS/Windows) or `sudo systemctl start docker` (Linux), then re-run the installer.

### Server won't start / port already in use

```bash
plugsky-web status           # is it actually running?
lsof -i :8080                # what's on port 8080?
plugsky-web logs 200         # last 200 lines of server logs
```

If something else is on port 8080, change the port (see above).

### "Invalid API key" errors in the Web UI

The browser-side OpenAI config can sometimes cache an old key. Fix:

1. `plugsky-web stop`
2. `rm -rf ~/.plugsky-web/data` (or `docker volume rm open-webui-data` for Docker)
3. `plugsky-web start`
4. Re-enter the API key in the Web UI's "Settings → Account"

### Stuck on "Starting..." for >60s

```bash
plugsky-web logs 200
```

Common causes:
- DNS not resolving `api.plugsky.com` from inside the container (Docker: try `--network=host`)
- Your API key is invalid (check `~/.plugsky-web/.env` — no leading/trailing whitespace, starts with `sk-live-` or `sk-test-`)

### Reset everything

```bash
plugsky-web uninstall
curl -fsSL https://plugsky.com/install-web | bash -s -- --api-key sk-live-...
plugsky-web start
```

## What gets installed where

| File | Purpose |
|---|---|
| `~/.plugsky-web/bin/plugsky-web` | The shim (~330 lines, mode 755) |
| `~/.plugsky-web/.env` | API key + config (mode 600) |
| `~/.plugsky-web/backend` | One line: `docker` or `pip` |
| `~/.plugsky-web/data/` | Open WebUI data (pip mode only) |
| `~/.plugsky-web/logs/` | Server logs |
| `~/.plugsky-web/plugsky-web.pid` | PID file (pip mode) |
| `~/.plugsky-web/WELCOME.txt` | Quick-start banner (printed once on install) |
| `~/.zshrc` / `~/.bashrc` | Adds `~/.plugsky-web/bin` to `PATH` (unless `--no-modify-path`) |
| Docker volume `open-webui-data` | Open WebUI data (Docker mode only) |
| Docker image `ghcr.io/open-webui/open-webui:main` | Open WebUI runtime (Docker mode only) |
| `~/.plugsky-web/venv/` | Python venv with `open-webui` (pip mode only) |

## Advanced: what env vars does the shim pass to Open WebUI?

```
OPENAI_API_BASE_URL      = $API_BASE_URL
OPENAI_API_KEY           = $PLUGSKY_API_KEY
ENABLE_OPENAI_API        = True
DEFAULT_MODELS           = $DEFAULT_MODEL
WEBUI_SECRET_KEY         = plugsky-web-<timestamp>   (random, auto-regenerated on each start)
PORT                     = 8080  (inside the container/process; mapped to $PLUGSKY_PORT on host)
```

These are the same env vars documented in the
[Open WebUI docs](https://docs.openwebui.com/getting-started/env-configuration).

## Upgrading

```bash
plugsky-web update       # pull new image / upgrade pip package
plugsky-web restart      # restart to pick up the new version
```

## Uninstalling

```bash
plugsky-web uninstall
```

This:
- Stops the server
- Removes the Docker container + volume (Docker mode) OR the venv (pip mode)
- Removes `~/.plugsky-web/` (shim, config, data, logs)
- Removes the `PATH` line from `~/.zshrc` / `~/.bashrc`

Restart your terminal for the `PATH` change to take effect.

## Files in this directory

| File | Purpose |
|---|---|
| `install` | The installer (one bash script, ~770 lines, no external deps) |
| `README.md` | This file |

## License

- Installer — MIT
- Open WebUI — Open WebUI License (see [NOTICE](../../NOTICE))

## See also

- [Plugsky CLI](../../cli/install) — terminal coding agent
- [Plugsky Desktop (Jan)](../install) — native desktop chat app
- [Plugsky main README](../../README.md)
- [plugsky.com/docs/web](https://plugsky.com/docs/web) (live docs)
