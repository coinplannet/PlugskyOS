# Plugsky

Open-source tools from Plugsky.

- **CLI** (`cli/`) — One-line installer that wraps [opencode](https://github.com/anomalyco/opencode) (MIT) with Plugsky branding, preset provider (`https://api.plugsky.com/v1`), default model (`plugsky-pro`), and Plugsky brand theme. Ships as a single `plugsky` binary for macOS, Linux, and Windows.
- **Desktop** — coming soon

## Install

```sh
curl -fsSL https://plugsky.com/install | bash
```

Or with an API key:

```sh
curl -fsSL https://plugsky.com/install | bash -s -- --api-key sk-live-...
```

## Source

The installer is ~500 lines of bash in `cli/install`. The configuration files it drops in `~/.plugsky/`:

- `opencode.json` — preset provider + default model
- `theme.json` — Plugsky brand colors
- `WELCOME.txt` — quickstart
- `.env` (optional) — saved API key

We do NOT ship a 156MB copy of the upstream opencode source. The installer downloads the prebuilt opencode binary at install time.

## License

- `cli/install` — MIT
- Preset configs (opencode.json, theme.json) — MIT
- Bundled `opencode` binary — MIT (github.com/anomalyco/opencode)
