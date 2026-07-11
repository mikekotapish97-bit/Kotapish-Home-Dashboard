# Changelog

All notable changes to this project are documented here.
This project follows [Semantic Versioning](https://semver.org/).

## [1.0.2] - 2026-07-11

### Fixed

- **Settings → Kiosk Mode row did nothing when tapped.** It called a
  `kiosk_mode.user_settings` Home Assistant service that does not exist —
  Kiosk Mode (the HACS resource) has no backend integration and registers
  no services at all; it is pure client-side CSS driven by the static
  `kiosk_mode:` block in `dashboard.yaml`. Replaced the fake service call
  with an accurate, live `state_display` (reports whether the current
  signed-in session has chrome hidden, via `hass.user.is_admin`) and a
  tap action that opens an explanatory popup instead of pretending to
  toggle something at runtime.

## [1.0.1] - 2026-07-11

### Fixed

- **Blank page content on every view.** `custom:grid-layout` (Layout Card)
  is only valid as a view's own `type:`, not as a card nested inside a
  `cards:` list — every page nested it 2-3 levels deep for the "main"
  content area, tile rows, and BAS point grids, which silently failed to
  render. Replaced every nested occurrence with Home Assistant's built-in
  `vertical-stack` / `horizontal-stack` / `grid` cards; only the one
  view-level `custom:grid-layout` per page (nav rail + main content
  columns) remains.
- **Kiosk Mode could lock out administration.** `kiosk_mode` previously
  hid the header/sidebar unconditionally for every session, including
  admin accounts, with no way back to Settings on a device with no
  browser chrome. Replaced with `non_admin_settings` / `admin_settings`,
  so any Administrator account always keeps the sidebar and header.
  `INSTALLATION.md` now documents creating a separate non-admin "Wall
  Display" user for the iPad's daily session.
- **Deprecated `lovelace: mode: yaml`.** Home Assistant 2026.8 removes the
  legacy top-level `mode: yaml` key. `INSTALLATION.md` and the header
  comment in `dashboard.yaml` now show the current `lovelace: dashboards:`
  form only.

## [1.0.0] - 2026-07-11

### Added

- Initial production release of the Kotapish Home Dashboard.
- Apple Home / VisionOS-inspired glass theme (`kotapish_glass`) with light
  and dark modes, single-source CSS variables, and a global Card Mod hook
  so every card automatically inherits blur/radius/shadow.
- 80px left navigation rail with self-highlighting active state, covering
  10 destinations: Home, Cameras, Climate, Lighting, Security, Energy,
  Media, Network, Commercial HVAC, Settings.
- **Home** page: dynamic greeting header, live clock/date/weather, live
  camera hero (State Switch selector for Entry/Driveway/Deck), Honeywell
  T6 Pro thermostat card, Home Status row (Garage/Locks/Alarm/Internet/
  Powerwall), and a 6-tile room grid opening Browser Mod popups.
- **Cameras** page: large live feed, thumbnail selector strip, fullscreen
  popup, recent motion events list (Auto Entities).
- **Climate** page: full thermostat control, indoor temperature/humidity
  trend graph, outdoor temperature, and a Clock Weather Card hero.
- **Lighting** page: whole-home scene shortcuts and per-room light cards.
- **Security** page: alarm panel, garage/locks/smoke/water/motion grid,
  recent security events list.
- **Energy** page: Powerwall/Solar/Grid/Home-Load summary tiles and two
  trend graphs (state of charge, 24h power comparison).
- **Media** page: Apple TV now-playing hero, per-room speaker cards,
  quick source shortcuts.
- **Network** page: Internet/Ping/VPN/WiFi-client status tiles, WAN
  bandwidth trend graph, UniFi device list.
- **Commercial HVAC** page: BAS-style supervisory status (BACnet/Niagara/
  JACE), AHU-1 and RTU-1 point grids (supply/return/mixed air, static
  pressure, damper/valve position, fan status, occupancy), Boiler-1 and
  Chiller-1 equipment cards, VFD speed/current, schedule helpers, and a
  cross-equipment trend graph.
- **Settings** page: Kiosk Mode controls, dashboard data helpers, about
  panel.
- Button Card template library (`kotapish_glass_base`, `kotapish_nav_icon`,
  `kotapish_room_tile`, `kotapish_status_chip`, `kotapish_camera_pill`,
  `kotapish_header_icon_button`, `kotapish_bas_point`,
  `kotapish_bas_equipment_header`, `kotapish_list_row`).
- Browser Mod popup conventions and shared glass dialog styling.
- Full documentation set: `README.md`, `INSTALLATION.md`,
  `HACS_DEPENDENCIES.md`.

### Known limitations

- Every entity beyond `climate.t6_pro`, `camera.entry_cam`, and
  `input_select.dashboard_camera` is a placeholder (`# PLACEHOLDER ENTITY`)
  and must be remapped to real devices — see `INSTALLATION.md` step 7.
- SF Pro is not bundled (Apple-licensed); the system font stack renders as
  true San Francisco on iPadOS/Safari only.
