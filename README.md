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
    │                              # button-card templates, view registry
    │                              # (10 pages)
    ├── theme/
    │   └── kotapish_glass.yaml   # Single source of truth: colors, blur,
    │                              # radius, spacing, fonts, global Card Mod
    ├── templates/
    │   ├── button_card_templates.yaml   # Named button-card templates
    │   ├── mushroom_templates.yaml      # Reference card_mod for Mushroom
    │   ├── card_mod_templates.yaml      # One-off Card Mod snippets
    │   └── popup_templates.yaml         # Browser Mod popup conventions
    ├── components/
    │   ├── nav_rail.yaml         # Custom nav rail — kept for reference,
    │   │                          # not included by any page (see below)
    │   ├── header.yaml           # Top bar: personal-tracker pills + clock
    │   │                          # + avatars (Home page, ported from reference)
    │   ├── camera_card.yaml      # State Switch live camera hero
    │   ├── thermostat_card.yaml # Honeywell T6 Pro (native circular dial
    │   │                          # + HVAC mode row)
    │   ├── home_status_card.yaml # Garage/Locks/Alarm/Internet/Powerwall tiles
    │   │                          # — kept for reference, not included by
    │   │                          # any page (see below)
    │   ├── rooms_list.yaml       # Rooms browse list -> Browser Mod popups
    │   ├── shortcuts_card.yaml   # Quick device toggles (Home page)
    │   ├── room_tiles.yaml       # 6-room tile grid — kept for reference,
    │   │                          # superseded by rooms_list.yaml (see below)
    │   ├── calendar_card.yaml    # Today Card + Atomic Calendar Revive (Home page)
    │   ├── status_bar.yaml       # Bottom status bar + Assist button (Home page)
    │   ├── weather_card.yaml     # Clock Weather Card hero (Climate page)
    │   └── weather_forecast_card.yaml # Native weather-forecast card (Home page)
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
- **28px rounded corners**, generous whitespace, large touch targets sized
  for a wall-mounted tablet viewed from a few feet away.
- **Typography** — system font stack that resolves to true San Francisco
  on iPadOS/Safari, no bundled font files.
- **One design language, reused everywhere** — button-card templates
  (`templates/button_card_templates.yaml`) are the single source for tile
  geometry/typography; Mushroom and Card Mod snippets are documented once
  and copied consistently rather than redefined per page.

## Navigation

This dashboard has no custom nav rail. Once Kiosk Mode was removed (see
below), Home Assistant's native sidebar and its automatic view-tabs strip
(generated from each page's `title`/`icon`) became the only navigation —
adding a third, custom rail alongside those two pushed content past the
iPad's viewport width and forced horizontal scrolling. Most pages are
`panel: true` with a single top-level card, using the full content width
Home Assistant already gives a panel view; the Home page uses a `type:
sections` view instead (see below). The original rail design is kept,
unreferenced, at `components/nav_rail.yaml` in case a future non-iPad/
kiosk deployment wants it back.

## Home page layout

The Home page is a `type: sections` view — Home Assistant's native
grid-based dashboard layout — rather than a hand-rolled stack of
horizontal-stacks, matching the structure of the reference dashboard this
project was modeled on:

- Header (full width): 4 personal-status pills (sleep tracker, washing
  machine, meeting status, 3D printer — ported directly from the
  reference, all placeholders since this project has no equivalent
  devices) + a live clock with seconds + a person-avatar pair
  (placeholders) + notifications bell
- Main row: Camera hero | Weather (native `weather-forecast` card, not
  Clock Weather Card — see `components/weather_forecast_card.yaml` for
  why) | Thermostat (native circular dial via HA's built-in `type:
  thermostat` card, plus a 4-button HVAC mode row below it)
- Middle row: Rooms list (browse/info) | Shortcuts (real device toggles:
  garage, TV, 4 room lights) | Calendar (Today Card + Atomic Calendar
  Revive, both ported directly from the reference — new HACS
  dependencies, see `HACS_DEPENDENCIES.md`)
- Bottom status bar (full width): lights/climate/media/garage pills +
  a native Assist voice button

This is a direct, literal port of the reference dashboard's actual
templates and card choices (not a reinterpretation) — including its own
personal-life-tracker pills and calendar cards — with entity IDs adjusted
to this project's real/placeholder entities. Two exceptions, explained in
`HACS_DEPENDENCIES.md`: the camera and thermostat keep this project's
existing working cards (State Switch, native `type: thermostat`) rather
than the reference's Scrypted-NVR iframe (a whole separate NVR server
this project doesn't run) — everything else matches. Its `input_select`-
driven swipeable-column customization system (letting the reference's
owner rearrange columns via a settings picker) was not ported — that's
invisible configuration plumbing, not something visible in the dashboard
itself, and would need dozens of new helper entities for no visual
difference. `components/home_status_card.yaml` (an earlier, different
take on this row) is kept unreferenced, same pattern as `nav_rail.yaml`.

The visual language (30px card radius, layered glass highlight/shadow,
"SF Pro Rounded" font) was adapted directly from the reference
dashboard's own CSS — see `theme/kotapish_glass.yaml`'s `card-mod-card`
hook. Everything is sized to fit one 12.9" iPad Pro screen with no
scrolling — see the comments in `pages/home.yaml` and each component file
for how each card was trimmed to fit.

## Pages

| Page | Path | Highlights |
|---|---|---|
| Home | `/home` | Dense `sections`-view grid: status-pill header, camera/weather/thermostat row, rooms/shortcuts/calendar row, bottom status bar + Assist — no scrolling on a 12.9" iPad |
| Cameras | `/cameras` | Large feed, thumbnail selector, fullscreen, recent motion |
| Climate | `/climate` | Full thermostat, temp/humidity graphs, outside weather |
| Lighting | `/lighting` | Whole-home scenes + per-room light control |
| Security | `/security` | Alarm panel, garage/locks/smoke/water/motion, event log |
| Energy | `/energy` | Powerwall/Solar/Grid/Load tiles + trend graphs |
| Media | `/media` | Apple TV now playing, per-room speakers, sources |
| Network | `/network` | Internet/Ping/VPN/WiFi tiles, bandwidth graph, UniFi devices |
| Commercial HVAC | `/commercial-hvac` | BAS front-end: AHU/RTU/Boiler/Chiller/VFD, BACnet/Niagara/JACE status, schedules, trends |
| Settings | `/settings` | Guided Access info, dashboard data, about |

## Required HACS components

Button Card, Mushroom, Bubble Card, Browser Mod, Card Mod, Clock Weather
Card, State Switch, Mini Graph Card, Auto Entities. Full detail in
[`HACS_DEPENDENCIES.md`](HACS_DEPENDENCIES.md).

**Not used: Kiosk Mode.** The HA sidebar/header are left always visible;
the wall-mounted feel comes from iPadOS's own Guided Access instead (see
`INSTALLATION.md` step 6) — Kiosk Mode's role-based hiding repeatedly
failed to reliably guarantee a way back into Settings.

**Not used: Layout Card.** Every page previously used `custom:grid-layout`
to split the view into a nav-rail column and a content column. With the
custom nav rail dropped (see Navigation, above), no page needs a custom
grid layout anymore — each view is just `panel: true` with one card.

## Entities

Only three entities are assumed to exist in a bare install:

- `climate.t6_pro` — Honeywell T6 Pro thermostat
- `camera.entry_cam` — Entry camera
- `input_select.dashboard_camera` — camera selector (options: `Entry`, `Driveway`, `Deck`)

One additional entity has a full setup guide because it's backed by a
specific integration choice rather than being a stand-in for "whatever
you have": `weather.ranson_wv_nws`, the National Weather Service entity
for Ranson, WV, used by the Home page's weather forecast card and the
Climate page's weather hero. See `INSTALLATION.md` step 8. (The Home
page's weather card also needs a few placeholder sensors — see
`components/weather_forecast_card.yaml` — since NWS/HA's modern weather
entities don't expose daily high/low as plain attributes; those are
covered by the general placeholder convention below.)

Every other entity referenced in this project is a clearly marked
placeholder (`# PLACEHOLDER ENTITY` comment on the same line) meant to be
remapped to your real devices. See `INSTALLATION.md` step 7 for how to
find and replace all of them.

## License

Internal project for the Kotapish smart home. No warranty; verify all
BAS/HVAC placeholder points against your real Niagara/JACE point list
before relying on this for building automation monitoring.
