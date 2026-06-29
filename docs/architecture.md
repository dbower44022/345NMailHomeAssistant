# 345 N Main — Home Assistant architecture notes

Snapshot as of 2026-06-29. Regenerate entity details with `scripts/ha states`.

## Server
- HA Core 2026.6.2 at `192.168.254.244:8123` on LAN `192.168.254.0/24`, TZ America/New_York.
- ~877 entities. Config is YAML-based, edited via the File editor add-on (`/core_configurator`).

## Cameras
- **Amcrest (YAML platform, `amcrest:`):** 10 cameras at `192.168.254.90–.110`, password via
  `!secret amcrest_password`. Startup-only platform → changes need a full restart.
- **Reolink (UI integration):** "South Side" RLC-1240A at `192.168.254.95`
  (`camera.south_side_*`). NOT Amcrest — a duplicate Amcrest block for `.95` was removed.
- A separate batch of camera motion/AI `binary_sensor`s (e.g. `breezeway_cam`, `garage_ne_cam`,
  `amcrest_ip8m_t2669e_*`) currently report `unavailable` — not yet investigated.

## Climate
- 3 ecobee thermostats: Family Room, Master Bedroom, Hugh Bedroom.
  - Family Room & Master Bedroom run in `heat_cool` (range) mode; Hugh Bedroom in single-setpoint `cool` mode.
- **Integrated via HomeKit Controller** (`homekit_controller`), not the native ecobee cloud
  integration — so HA cannot read or edit the ecobee weekly comfort schedule, and there are no
  `ecobee.*` services or preset/program attributes. The ecobee runs its own schedule; HA setpoints
  become *holds* whose duration depends on the thermostat's "Hold Action" preference.
- **Decision (2026-06-29):** HA does **not** manage thermostat setpoints. An overnight setback
  automation was trialed and removed — without flattening the ecobee schedule (or setting Hold
  Action to "Until I change it", which the app didn't expose), HA holds and the ecobee schedule
  fought each other. The ecobee remains the source of truth for climate.
- Disabled duplicate climate entities exist from an inactive **Hubitat** integration
  (`climate.living_room`, `master_bedroom_2`, `hugh_bedroom_2`, `hugh_bedroom_controller`).

## Lighting
- 53 `light` entities: outdoor coach/carriage/flood lights, room dimmers (Leviton/Levoit),
  and WLED strips.
- **WLED:** 5 controllers (`192.168.254.182–.190`) are offline (no ping / no mDNS) — power or
  physical issue, not config. HA `setup_retry` will recover them when powered on.

## Security / network
- UniFi UDM Pro: `alarm_control_panel.345_n_main_udm_pro_alarm_manager`.
- A UniFi Protect camera also shows up (manufacturer Ubiquiti, `192.168.254.254`).

## Notifications
- Working: mobile-app notify services (`notify.mobile_app_doug_iphone`, `_doug_iphone_13`,
  `_douglass_ipad`).
- **Pushover:** in `setup_retry` — "user is valid but has no active devices" (Pushover
  account needs a registered device; account-side fix, not HA config).

## Automations (4)
- Water leak alert / Laundry water detected
- Outdoor LEDs on at sunset / off in morning

## Known open items
- Pushover: register a device, or drop in favor of mobile-app notify.
- WLED controllers offline (power/physical).
- Camera motion/AI binary_sensors unavailable (separate from the fixed Amcrest cameras).
- Consider rotating device credentials (now in `secrets.yaml`) and moving the `decora_wifi`
  username into secrets as well.
