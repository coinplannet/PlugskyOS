# Plugsky OS

The official CLI for [Plugsky](https://plugsky.com) — a branded AI coding agent for your terminal.

## Install

```sh
curl -fsSL https://plugsky.com/install | bash
```

Or with an API key:

```sh
curl -fsSL https://plugsky.com/install | bash -s -- --api-key sk-live-...
```

## What you get

- `plugsky` binary on your PATH
- Pre-configured `~/.plugsky/config.json` with `plugsky-pro` as the default model
- Plugsky brand theme (`~/.plugsky/theme.json`)
- Auto-detected macOS / Linux / Windows installer
- One-key setup, one-command uninstall

## Source

- `cli/install` — bash installer (~400 lines, no deps)
- Configs and theme are dropped in `~/.plugsky/` at install time

## License

- Installer — MIT
- Configs / theme — MIT
- Bundled runtime — MIT (see NOTICE file for upstream attribution)
EOF
