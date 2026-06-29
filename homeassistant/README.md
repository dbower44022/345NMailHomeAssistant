# homeassistant/ — config mirror

This directory is the intended home for a version-controlled copy of the server's YAML config
(`configuration.yaml`, `automations.yaml`, `scripts.yaml`, `scenes.yaml`, …).

**`secrets.yaml` is gitignored** and must never be committed.

## Populating / syncing this mirror

The server config lives on the HA box at `/homeassistant/`. To pull it here for version control,
enable one of these on the server, then sync:

- **Samba share add-on** — mount the config share and copy the YAML files in.
- **SSH / Terminal add-on** — `scp`/`rsync` the YAML files (excluding `secrets.yaml`) into this dir.
- **Studio Code Server add-on** — edit with git directly on the box.

Once a sync method is in place, commit the YAML here so changes are tracked and reviewable before
being applied to the live server. Until then, edits are made via the File editor add-on
(`/core_configurator`) and validated with `scripts/ha check`.

> Note: `configuration.yaml` is safe to commit — all credentials are referenced via `!secret`
> and resolved from the (uncommitted) `secrets.yaml`.
