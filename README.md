# 345 N Main — Home Assistant Management

Tooling and documentation for configuring and managing the **345 N Main Home Assistant** server.

## Server

- **URL:** http://192.168.254.244:8123 (`homeassistant.local`)
- **Version:** 2026.6.2
- Connection details live in `.env` (gitignored). Copy `.env.example` → `.env` and fill in a
  Long-Lived Access Token (HA → profile → Security → Long-lived access tokens).

## Layout

| Path | What |
|---|---|
| `.env` / `.env.example` | Server URL + API token (real `.env` is gitignored) |
| `scripts/ha` | Small management CLI (see below) |
| `homeassistant/` | Mirror of the server's YAML config for version control (secrets excluded) |
| `entities.md` | Snapshot of all entities by domain |
| `docs/` | Architecture notes and runbooks |

## `scripts/ha` — management CLI

```bash
scripts/ha ping            # is the server up?
scripts/ha config          # core config summary
scripts/ha states [domain] # list entities (optional domain filter, e.g. light)
scripts/ha unavailable     # everything currently unavailable/unknown
scripts/ha check           # validate configuration.yaml
scripts/ha reload <domain> # reload a YAML domain (automation, scripts, scenes, template…)
scripts/ha restart         # restart HA core
```

## Notes

- The server config is **YAML-based** (`configuration.yaml`, `automations.yaml`, etc.), edited
  via the File editor add-on at `/core_configurator`.
- Secrets are stored in the server's `secrets.yaml` and referenced with `!secret`. Never commit
  real secret values — `secrets.yaml` and `.env` are gitignored.
