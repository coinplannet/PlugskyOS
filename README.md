# Plugsky OS

The official client apps for [Plugsky](https://plugsky.com) — a branded AI client family for your computer.

## Apps

| App | What it is | Install |
|---|---|---|
| **[Plugsky CLI](cli/)** | AI coding agent for your terminal | `curl -fsSL https://plugsky.com/install \| bash` |
| **[Plugsky Desktop — Jan](desktop/)** | Native AI chat client for macOS / Linux / Windows (Jan-based) | `curl -fsSL https://plugsky.com/install-desktop \| bash` |
| **[Plugsky Web — Open WebUI](desktop/openwebui/)** | Self-hosted AI chat UI in your browser (Open WebUI-based) | `curl -fsSL https://plugsky.com/install-web \| bash` |

All three are 100% Plugsky-branded, pre-configured for the Plugsky API, and work
out of the box once you set `PLUGSKY_API_KEY`.

## What you get (common to all apps)

- A single command on your `PATH`
- Pre-configured `plugsky-pro` as the default model
- Plugsky API endpoint (`https://api.plugsky.com/v1`) baked in
- All 31 Plugsky models available (Pro, Max, Frontier, Reasoning, Vision, Ultra, Kimi, OSS, Embed, …)
- One-key setup, one-command uninstall
- macOS / Linux / Windows support

## Quick start

```sh
# Pick the app you want
curl -fsSL https://plugsky.com/install           | bash   # CLI
curl -fsSL https://plugsky.com/install-desktop   | bash   # Desktop (Jan)
curl -fsSL https://plugsky.com/install-web       | bash   # Web (Open WebUI)

# Get an API key at https://plugsky.com/dashboard, then:
export PLUGSKY_API_KEY=sk-live-...

# Run the app
plugsky            # CLI
plugsky-desktop    # Desktop
plugsky-web start  # Web (browser opens to http://localhost:8080)
```

Or one-shot with the key:

```sh
curl -fsSL https://plugsky.com/install | bash -s -- --api-key sk-live-...
```

## Layout

```
.
├── cli/             Plugsky CLI (opencode-go-based)
│   └── install
├── desktop/         Plugsky Desktop — Jan (native .app / .AppImage / .exe)
│   ├── install
│   └── openwebui/   Plugsky Web — Open WebUI (Docker / pip)
│       ├── install
│       └── README.md
├── source/          Rebranded source fork (CLI, TUI internals)
├── docs/
├── scripts/
├── NOTICE
├── README.md
└── .gitignore
```

## License

- Installer scripts and shims — **MIT**
- Bundled runtimes — see [NOTICE](NOTICE) for upstream attribution and licenses:
  - [opencode](https://github.com/anomalyco/opencode) — MIT (CLI runtime)
  - [Jan](https://github.com/menloresearch/jan) — Apache-2.0 (Desktop runtime)
  - [Open WebUI](https://github.com/open-webui/open-webui) — Open WebUI License (Web runtime)
