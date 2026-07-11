# Kotapish Home Dashboard

A production-ready Home Assistant dashboard built for a permanently
wall-mounted **12.9" iPad Pro (landscape)**, designed to feel like
**Apple Home on iPadOS** — not stock Home Assistant, not Material Design.

Frosted glass surfaces, 28px rounded corners, large touch targets, SF Pro
(system font) typography, and smooth motion, running on Home Assistant
2026 in full YAML Lovelace mode.

## Quick start

See [`INSTALLATION.md`](INSTALLATION.md) for the complete step-by-step
setup, and [`HACS_DEPENDENCIES.md`](HACS_DEPENDENCIES.md) for everything
that must be installed first.

## Project structure

```
kotapish-home-dashboard/
├── README.md
├── CHANGELOG.md
├── INSTALLATION.md
├── HACS_DEPENDENCIES.md
└── src/
    ├── dashboard.yaml            # Root Lovelace config — title, theme,
    │                              # button-card templates, Kiosk Mode,
    │                              # view registry (10 pages)
    ├── theme/
    │   └── kotapish_glass.yaml   # Single source of truth: colors, blur,
    │                              # radius, spacing, fonts, global Card Mod
    ├── templates/
    │   ├── button_card_templates.yaml   # Named button-card templates
    │   ├── mushroom_templates.yaml      # Reference card_mod for Mushroom
    │   ├── card_mod_templates.yaml      # One-off Card Mod snippets
    │   └── popup_templates.yaml         # Browser Mod popup conventions
    ├── components/
    │   ├── nav_rail.yaml         # 80px left navigation rail (all pages)
    │   ├── header.yaml           # Greeting, clock, date, weather, bell
    │   ├── camera_card.yaml      # State Switch live camera hero
    │   ├── thermostat_card.yaml # Honeywell T6 Pro
    │   ├── home_status_card.yaml # Garage/Locks/Alarm/Internet/Powerwall
    │   ├── room_tiles.yaml       # 6-room tile grid -> Browser Mod popups
    │   └── weather_card.yaml     # Clock Weather Card hero
    ├── popups/
    │   ├── room_living_room.yaml
    │   ├── room_kitchen.yaml
    │   ├── room_bedroom.yaml
    │   ├── room_office.yaml
    │   ├── room_garage.yaml
    │   ├── room_patio.yaml
    │   └── camera_fullscreen.yaml
    ├── pages/
    │   ├── home.yaml
    │   ├── cameras.yaml
    │   ├── climate.yaml
    │   ├── lighting.yaml
    │   ├── security.yaml
    │   ├── energy.yaml
    │   ├── media.yaml
    │   ├── network.yaml
    │   ├── commercial_hvac.yaml
    │   └── settings.yaml
    └── assets/
        ├── README.md              # Why no font/icon binaries are bundled
        ├── fonts/                 # (intentionally empty — see assets/README.md)
        └── icons/                 # (intentionally empty — see assets/README.md)
```

## Design language

- **Glass surfaces** — every card is a translucent, blurred sheet via a
  single global Card Mod hook (`card-mod-card` in the theme). No page or
  component file redeclares blur/radius/shadow.
- **28px rounded corners**, generous whitespace, 80px nav rail, large
  touch targets sized for a wall-mounted tablet viewed from a few feet away.
- **Typography** — system font stack that resolves to true San Francisco
  on iPadOS/Safari, no bundled font files.
- **One design language, reused everywhere** — button-card templates
  (`templates/button_card_templates.yaml`) are the single source for tile
  geometry/typography; Mushroom and Card Mod snippets are documented once
  and copied consistently rather than redefined per page.

## Pages

| Page | Path | Highlights |
|---|---|---|
| Home | `/home` | Greeting header, live camera hero, T6 Pro thermostat, Home Status row, 6 room tiles |
| Cameras | `/cameras` | Large feed, thumbnail selector, fullscreen, recent motion |
| Climate | `/climate` | Full thermostat, temp/humidity graphs, outside weather |
| Lighting | `/lighting` | Whole-home scenes + per-room light control |
| Security | `/security` | Alarm panel, garage/locks/smoke/water/motion, event log |
| Energy | `/energy` | Powerwall/Solar/Grid/Load tiles + trend graphs |
| Media | `/media` | Apple TV now playing, per-room speakers, sources |
| Network | `/network` | Internet/Ping/VPN/WiFi tiles, bandwidth graph, UniFi devices |
| Commercial HVAC | `/commercial-hvac` | BAS front-end: AHU/RTU/Boiler/Chiller/VFD, BACnet/Niagara/JACE status, schedules, trends |
| Settings | `/settings` | Kiosk Mode controls, dashboard data, about |

## Required HACS components

Button Card, Mushroom, Bubble Card, Browser Mod, Layout Card, Card Mod,
Clock Weather Card, State Switch, Mini Graph Card, Auto Entities, Kiosk
Mode. Full detail in [`HACS_DEPENDENCIES.md`](HACS_DEPENDENCIES.md).

## Entities

Only three entities are assumed to exist in a bare install:

- `climate.t6_pro` — Honeywell T6 Pro thermostat
- `camera.entry_cam` — Entry camera
- `input_select.dashboard_camera` — camera selector (options: `Entry`, `Driveway`, `Deck`)

Every other entity referenced in this project is a clearly marked
placeholder (`# PLACEHOLDER ENTITY` comment on the same line) meant to be
remapped to your real devices. See `INSTALLATION.md` step 7 for how to
find and replace all of them.

## License

Internal project for the Kotapish smart home. No warranty; verify all
BAS/HVAC placeholder points against your real Niagara/JACE point list
before relying on this for building automation monitoring.
