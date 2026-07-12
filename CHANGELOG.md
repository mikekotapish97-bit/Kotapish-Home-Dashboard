# Changelog

All notable changes to this project are documented here.
This project follows [Semantic Versioning](https://semver.org/).

## [1.2.3] - 2026-07-11

### Fixed

- **Camera fullscreen popup had no visible close button.** The popup's
  style explicitly hid `.mdc-dialog__title` and `.mdc-dialog__actions` for
  a clean edge-to-edge look — which also hid the dialog's close (X)
  button. On an iPad there's no Escape key and no address bar/back gesture
  in a home-screen web app, so this left no way to dismiss it.
  `components/camera_card.yaml` now keeps the title bar (styled as a slim
  dark strip) so its close button stays visible, and
  `popups/camera_fullscreen.yaml` adds a `tap_action` on the video itself
  that closes the popup (`browser_mod.close_popup`) as a second, redundant
  way back.
- **Home page temperature graph still not loading, while the identical
  entity's graph on the Climate page works fine.** Two independent causes:
  - `components/thermostat_card.yaml`'s mini-graph-card also graphed the
    `temperature` (setpoint) attribute, which doesn't exist while a
    climate entity is in heat_cool/auto mode (it reports
    `target_temp_low`/`target_temp_high` instead) — an all-null series
    can break the whole card. Dropped it in favor of `current_temperature`
    alone, which is always present; the full current+humidity breakdown
    (attributes already confirmed to always exist) stays on the Climate
    page.
  - The Home page nested this graph one level deeper than the working
    Climate page graph (`horizontal-stack > vertical-stack >
    thermostat_card's-own-vertical-stack > mini-graph-card` vs. Climate's
    `horizontal-stack > vertical-stack > mini-graph-card`) — extra
    flex-container nesting is a known trigger for chart cards computing a
    collapsed/zero width before layout settles. Flattened this by folding
    `components/home_status_card.yaml` into `components/thermostat_card.yaml`
    as its own last card, so `pages/home.yaml` now includes
    `thermostat_card.yaml` directly as a `horizontal-stack` sibling of the
    camera card, with no extra wrapping `vertical-stack` — matching the
    Climate page's nesting depth exactly.

## [1.2.2] - 2026-07-11

### Fixed

- **Temperature history graph on the Home page never loading.** Mini
  Graph Card caches fetched history client-side by default (`cache: true`)
  and its own documentation names stale/corrupt cached history as a known
  failure mode ("if you have issues after updating the card, try clearing
  your browser cache"). Added `cache: false` to every `custom:mini-graph-card`
  in the project (thermostat history on Home, indoor/outdoor graphs on
  Climate, room popups, Energy, Network, Commercial HVAC trends) so each
  always fetches fresh history instead of risking a stuck/stale cached
  result. If the Home page graph still doesn't populate after this, check
  Developer Tools → History for `climate.t6_pro` directly to confirm the
  recorder actually has data for it over the last 24 hours.

## [1.2.1] - 2026-07-11

### Fixed

- **Room tile text overlapping/hidden behind the icon.** The
  `kotapish_room_tile` button-card template pinned the icon to the top and
  the name/state to the bottom using fixed pixel offsets
  (`position: absolute`), which only avoids overlap if the card's actual
  rendered height matches the `aspect-ratio: 1.6` the offsets assumed.
  Safari/iPadOS is known to handle `aspect-ratio` inconsistently on flex
  containers, so the card could render shorter than assumed, landing the
  bottom-pinned text directly on top of the icon. Rebuilt the template
  using normal document flow (icon, then name, then state, stacked with
  `gap`) instead — this can't overlap regardless of the card's actual
  height, at the cost of the icon no longer floating over a background
  wash.
- **Camera hero showing no camera view at all.** The `custom:state-switch`
  card has no built-in fallback: if `input_select.dashboard_camera`'s
  actual current state doesn't exactly match one of "Entry"/"Driveway"/
  "Deck" (e.g. it was never initialized after creating the helper, or its
  options differ from what this project assumes), it renders nothing, with
  no error. Added `default: Entry` to both `components/camera_card.yaml`
  and `popups/camera_fullscreen.yaml` so a real camera always shows
  regardless of the helper's current state. If this doesn't fully resolve
  it, check Developer Tools → States for `input_select.dashboard_camera`'s
  actual current value and options.

## [1.2.0] - 2026-07-11

### Fixed

- **"ButtonCardJSTemplateError" on camera pills, status chips, and BAS
  points.** Every `[[[ ]]]` template that read `states['some.entity'].state`
  did so with no guard for the entity not existing. Since this project
  ships with placeholder entities that genuinely don't exist until you map
  them to real ones — and `input_select.dashboard_camera` may not be set
  up in a fresh install either — that lookup throws `Cannot read
  properties of undefined`, which button-card surfaces as this error card.
  Every occurrence across `home_status_card.yaml`, `camera_card.yaml`,
  `room_tiles.yaml`, and `commercial_hvac.yaml` now guards with
  `(states['x'] && states['x'].state)`.
- **Room tiles showing raw, unrendered template text** (e.g. literally
  `[[[ return states[...] ]]]` as the tile's subtitle). The trailing
  `# PLACEHOLDER ENTITY` comment sat on the same line as the closing `]]]`
  *inside* a `|` block scalar, so YAML treated it as literal string
  content rather than a comment — button-card no longer recognized the
  value as a template at all. Converted these to quoted single-line
  strings (matching the safe pattern already used in `camera_card.yaml`),
  where a trailing `#` comment is unambiguous.
- **"Good Evening" overlapping the date/weather line underneath it** in
  the header. Button-card doesn't automatically stack multiple
  `custom_fields` into separate rows — without an explicit
  `grid-template-areas`, they default to the same grid cell. Added one to
  `components/header.yaml`.
- **Status chip row (Garage/Locks/Alarm/Internet/Powerwall) pushing
  content off the right edge of the iPad, forcing horizontal scroll.**
  `home_status_card.yaml` used a `horizontal-stack`, which forces all 5
  chips onto one non-wrapping row — inside a column that's only half the
  page width (shared with the camera card), that's far too narrow.
  Switched to a wrapping `grid` (2 columns).

### Changed

- **Removed the custom 80px nav rail from every page.** Once Kiosk Mode
  was removed (1.1.0), Home Assistant's native sidebar and its own
  automatic view-tabs strip became visible again — running our nav rail
  as a *third* navigation surface alongside those two pushed page content
  past the iPad's viewport width, forcing horizontal scrolling on every
  page. Each page is now `panel: true` with a single top-level card (no
  `custom:grid-layout` nav+main split); navigation is entirely HA's native
  sidebar plus the auto-generated view tabs. The original rail design is
  kept, unreferenced, at `components/nav_rail.yaml` for any future
  non-iPad/kiosk deployment that wants it back.
- **Layout Card is no longer a required HACS dependency.** It was only
  ever used for the nav+main view split now removed above.
- Re-validated the whole project with a loader that catches duplicate
  YAML mapping keys, not just syntax errors.

## [1.1.1] - 2026-07-11

### Fixed

- **"Configuration error" / blank content on every view.** The Settings
  page's "Wall Display" section used a core `type: entities` card with
  full `type: custom:button-card` configs nested inside its `entities:`
  list. That list only accepts simple entity-row configs
  (`entity`/`name`/`icon`/...), not full nested cards — this is schema-
  invalid, and because a YAML-mode dashboard's config is validated as one
  whole document, this single bad card made Home Assistant reject the
  ENTIRE dashboard config, showing "Configuration error" regardless of
  which view tab was actually selected. Replaced the `entities` card with
  a plain `vertical-stack` of standalone glass cards, and updated the
  `kotapish_list_row` button-card template to be a self-contained glass
  row (extends `kotapish_glass_base`, matching the existing
  `kotapish_status_chip` pattern) instead of assuming it's nested inside
  an `entities` card's chrome. Also removed a leftover duplicate
  `card_mod:` key on the same card left over from a previous edit.
- Re-validated the whole project with a YAML loader that raises on
  duplicate mapping keys (plain `yaml.safe_load` silently allows them,
  last-key-wins) — this specific duplicate-key bug wouldn't have been
  caught by the plain-syntax check used for earlier fixes.

### Note

Once the header/sidebar are visible (as of 1.1.0's Kiosk Mode removal),
Home Assistant automatically renders each view's `icon`/`title` as a tab
strip along the top of the dashboard — this is core Lovelace behavior for
any multi-view YAML dashboard, not something this project adds or can
remove without reintroducing a hiding hack. It's a harmless, safe
secondary way to navigate alongside the custom left nav rail.

## [1.1.0] - 2026-07-11

### Changed

- **Removed the Kiosk Mode HACS resource entirely.** Despite two rounds
  of config hardening (`admin_settings`/`non_admin_settings`,
  `ignore_mobile_settings`, `ignore_entity_settings`), the sidebar/header
  could still disappear with no way back, even for the only (admin)
  account on the instance. Given the underlying project has been
  archived/unmaintained since 2022 with uncertain fork behavior, it's no
  longer worth the risk. The HA sidebar/header are now always left
  visible; `dashboard.yaml` no longer has a `kiosk_mode:` block at all.
- The wall-mounted, single-app "kiosk" feel now comes entirely from
  **iPadOS's own Guided Access** (OS-level, no plugin config involved) —
  see `INSTALLATION.md` step 6 (rewritten) and the Settings page's
  "Wall Display" row (now shows Guided Access info instead of a Kiosk
  Mode status/popup).
- Updated `README.md` and `HACS_DEPENDENCIES.md` to drop Kiosk Mode from
  the required components list and explicitly call out that it should
  not be installed for this project.

## [1.0.3] - 2026-07-11

### Fixed

- **Sidebar/header could still disappear with no way back, even for an
  admin account.** `dashboard.yaml`'s `kiosk_mode` block used an incorrect
  key (`hide_menu_button` instead of the plugin's actual `hide_menubutton`)
  and two undocumented keys (`hide_sidebar_toggle`, `hide_scrollbar`).
  More importantly, per the plugin's own docs, `mobile_settings` and
  `entity_settings` can silently override `admin_settings`/
  `non_admin_settings` unless told not to — added
  `ignore_mobile_settings: true` and `ignore_entity_settings: true` to
  both blocks so nothing can override the "admins always keep chrome"
  guarantee.
- Documented the plugin's `?disable_km` URL query-string override in
  `INSTALLATION.md` and in the Settings page's Kiosk Mode popup as a
  guaranteed manual escape hatch, independent of any YAML config — added
  because the underlying HACS project (`maykar/kiosk-mode`) has been
  archived/read-only since 2022 and HACS may resolve to any of several
  community forks with possibly different behavior.

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
