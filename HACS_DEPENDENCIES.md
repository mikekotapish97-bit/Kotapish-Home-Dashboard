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
| **button-card** | All templated glass tiles, BAS equipment/point tiles |
| **Mushroom** | Climate, light, lock, cover, media-player, alarm-panel, template cards |
| **Bubble Card** | Optional pop-up/header/separator styling — bundled for future pages (Settings quick-toggles) |
| **card-mod** | Global glass styling hook (`card-mod-card` in the theme), popup dialog styling |
| **clock-weather-card** | Weather hero on the Climate page only — the Home page's weather card uses HA's native `type: weather-forecast` instead (see `components/weather_forecast_card.yaml`) |
| **State Switch** | Camera hero card + fullscreen popup camera switching |
| **Mini Graph Card** | All temperature/humidity/energy/BAS trend graphs |
| **auto-entities** | Recent motion events, security events, UniFi device lists |

**Not used: Kiosk Mode.** This project intentionally does not hide the HA
sidebar/header via the Kiosk Mode HACS resource — its role-based hiding
repeatedly failed to reliably keep the sidebar reachable, and the
underlying project has been archived/unmaintained since 2022. The wall
iPad's kiosk feel comes from iPadOS's own Guided Access instead — see
`INSTALLATION.md` step 6. Do not install Kiosk Mode for this dashboard.

**Not used: Layout Card.** Every page previously used `custom:grid-layout`
(from Layout Card) to split the view into a nav-rail column and a content
column. Once the custom nav rail was dropped in favor of HA's native
sidebar (see `README.md`), no page needs a custom grid layout anymore —
each view is just `panel: true` with one top-level card. Layout Card is
not required.

## Home Assistant Core (no HACS needed)

- `input_select` (already used by `input_select.dashboard_camera`)
- `browser_mod` popups depend on the Browser Mod integration being loaded,
  not just the Lovelace resource — add it under **Settings → Devices &
  Services → Add Integration → Browser Mod** as well.
- **National Weather Service (NWS)** — core HA integration (not HACS),
  free, no signup. Powers the live Ranson, WV weather data behind the
  header's weather chip, the Home page forecast card, and the Climate
  page's weather hero. See `INSTALLATION.md` step 8.
- The Home page's thermostat widget (`components/thermostat_card.yaml`)
  is HA's built-in `type: thermostat` card — no HACS component involved.
- The Home page's calendar (`components/calendar_card.yaml`) is HA's
  built-in `type: calendar` card, pointed at the placeholder
  `calendar.kotapish_family_placeholder` — replace it with any real
  calendar integration (Google Calendar, CalDAV, iCloud via CalDAV, etc.)
  added under **Settings → Devices & Services**. No HACS card needed.

## After installing

1. HACS → Frontend → install each card above → **Home Assistant will
   prompt to add the Lovelace resource automatically** for most; if not,
   add manually under **Settings → Dashboards → Resources**.
2. Restart Home Assistant (recommended after adding Browser Mod).
3. Continue with [`INSTALLATION.md`](INSTALLATION.md).
