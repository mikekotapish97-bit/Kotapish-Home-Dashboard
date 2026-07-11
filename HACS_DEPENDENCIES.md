# HACS Dependency List

Install every item below through **HACS → Frontend** (cards) or **HACS →
Integrations** (the one backend integration) before loading the dashboard.
All of these are long-standing, default-repository HACS entries — search
for the exact name shown in the HACS UI rather than adding a custom
repository URL, unless HACS reports it isn't found in your version.

## Integrations (backend)

| Name | Type | Why this project needs it |
|---|---|---|
| **Browser Mod** | Integration + Frontend | Popups (room tiles, camera fullscreen), per-device kiosk commands |

## Frontend (Lovelace cards)

| Name | Used for |
|---|---|
| **button-card** | All templated glass tiles, nav rail, BAS equipment/point tiles |
| **Mushroom** | Climate, light, lock, cover, media-player, alarm-panel, template cards |
| **Bubble Card** | Optional pop-up/header/separator styling — bundled for future pages (Settings quick-toggles) |
| **card-mod** | Global glass styling hook (`card-mod-card` in the theme), popup dialog styling |
| **Layout Card** | `custom:grid-layout` — nav rail + main content grid on every page |
| **clock-weather-card** | Full weather hero on the Climate page |
| **State Switch** | Camera hero card + fullscreen popup camera switching |
| **Mini Graph Card** | All temperature/humidity/energy/BAS trend graphs |
| **auto-entities** | Recent motion events, security events, UniFi device lists |
| **Kiosk Mode** | Hides HA's own header/sidebar for the wall-mounted iPad |

## Home Assistant Core (no HACS needed)

- `input_select` (already used by `input_select.dashboard_camera`)
- `browser_mod` popups depend on the Browser Mod integration being loaded,
  not just the Lovelace resource — add it under **Settings → Devices &
  Services → Add Integration → Browser Mod** as well.

## After installing

1. HACS → Frontend → install each card above → **Home Assistant will
   prompt to add the Lovelace resource automatically** for most; if not,
   add manually under **Settings → Dashboards → Resources**.
2. Restart Home Assistant (recommended after adding Browser Mod).
3. Continue with [`INSTALLATION.md`](INSTALLATION.md).
