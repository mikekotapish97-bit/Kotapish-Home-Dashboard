# Installation

## 1. Copy the project into your Home Assistant config

Copy this entire repository into your HA `config/` directory, e.g.:

```
config/
└── kotapish-home-dashboard/
    ├── src/
    ├── README.md
    ├── CHANGELOG.md
    ├── INSTALLATION.md
    └── HACS_DEPENDENCIES.md
```

## 2. Install HACS dependencies

Follow [`HACS_DEPENDENCIES.md`](HACS_DEPENDENCIES.md) completely before
continuing — the dashboard will fail to render (custom element errors) if
any card is missing.

## 3. Add the theme

Copy the contents of [`src/theme/kotapish_glass.yaml`](src/theme/kotapish_glass.yaml)
into your `config/themes.yaml` (or point HA at it directly):

```yaml
# configuration.yaml
frontend:
  themes: !include_dir_merge_named themes
```

```
config/
└── themes/
    └── kotapish_glass.yaml   <-- copy of src/theme/kotapish_glass.yaml
```

Then set the theme to **kotapish_glass** for the profile used on the wall
iPad (Profile → Theme), or leave it set via the dashboard's own `theme:`
key in `dashboard.yaml` (already configured).

## 4. Register the dashboard

Add to `configuration.yaml`. **Do not** put `mode: yaml` directly under the
top-level `lovelace:` key — that legacy form is deprecated and is removed
in Home Assistant 2026.8. The per-dashboard `mode: yaml` below is a
different, still-supported setting and is required:

```yaml
lovelace:
  dashboards:
    kotapish-home:
      mode: yaml
      title: Kotapish Home
      icon: mdi:apple
      show_in_sidebar: true
      filename: kotapish-home-dashboard/src/dashboard.yaml
```

If you were previously on the old `lovelace: mode: yaml` single-dashboard
form and want to keep managing Lovelace *resources* (the HACS card
registrations) via YAML too, add `resource_mode: yaml` alongside
`dashboards:` — otherwise manage resources from **Settings → Dashboards →
Resources** as normal.

Restart Home Assistant (a full restart, not just a reload, is required the
first time a new YAML dashboard is registered).

## 5. Configure Browser Mod for the wall iPad

1. **Settings → Devices & Services → Add Integration → Browser Mod** (if
   not already installed as part of HACS setup).
2. Open the dashboard once on the iPad — Browser Mod will auto-register
   the browser as a device under **Settings → Devices & Services →
   Browser Mod**.
3. Rename that device (e.g. `ipad_pro_wall_dashboard`) for clarity when
   targeting it from automations later.

## 6. Wall-mount the iPad with Guided Access (no Kiosk Mode plugin)

This project deliberately does **not** use the Kiosk Mode HACS resource.
It repeatedly failed to reliably guarantee the sidebar/header stayed
reachable — up to and including for admin accounts — and it's a
long-archived, unmaintained project (the original `maykar/kiosk-mode`
repo has been read-only since 2022, with HACS resolving to any of several
forks of uncertain behavior). The Home Assistant sidebar and header are
simply left **always visible** here; there is no config to get wrong and
no way to get locked out.

The single-app "kiosk" feel for the wall-mounted iPad comes entirely from
**iPadOS's own Guided Access** instead — an OS-level lock, not a frontend
plugin:

1. Add the dashboard URL to the Home Screen from Safari (`Share → Add to
   Home Screen`) so it opens full-screen without Safari's own browser
   chrome.
2. In iPadOS **Settings → Accessibility → Guided Access**, turn it on and
   set a passcode.
3. Open the dashboard, then **triple-click the side button** to start a
   Guided Access session — this locks the iPad to that one Safari tab.
   Triple-click again and enter the passcode to exit and get back to the
   Home Screen (e.g. to adjust iPad-level settings).
4. Set **Auto-Lock** to *Never* for this iPad (Settings → Display &
   Brightness), since it's a permanently powered wall display.

The Home Assistant sidebar/header remain visible on-screen the whole
time — that's expected and intentional. If you ever want to hide them
again in the future, revisit a Kiosk Mode–style plugin only after
confirming its current, actively maintained fork and testing its escape
hatch thoroughly first.

## 7. Replace placeholder entities

Search the project for the comment tag `# PLACEHOLDER ENTITY` — every
match is an `entity_id` that does not exist in a bare install and must be
pointed at a real entity (or removed) before that card will show live
data. The only entities guaranteed to exist out of the box are:

- `climate.t6_pro`
- `camera.entry_cam`
- `input_select.dashboard_camera` (must have options: `Entry`, `Driveway`, `Deck`)

## 8. Set up live weather for Ranson, WV (National Weather Service)

Two cards need a real weather entity: the compact weather chip in the
header, `components/weather_forecast_card.yaml` (current conditions +
daily/weekly forecast on the Home page), and `components/weather_card.yaml`
(the Climate page's weather hero). All three currently point at the
placeholder `weather.ranson_wv_nws`.

This project uses the **National Weather Service (NWS)** integration —
free, no account/signup, built into Home Assistant core, and it's the
official US government forecast source, which tends to be more locally
accurate for a specific US town than a global provider.

1. **Settings → Devices & Services → Add Integration**, search for
   **"National Weather Service (NWS)"**.
2. When prompted for an API key: despite the name, NWS's API doesn't
   require a real registered key — HA just wants an identifying string so
   NWS can see who's calling their API. Enter something like
   `kotapish-dashboard (youremail@example.com)`, including your email as
   NWS's own usage policy requests (for them to contact you if their API
   ever needs to reach out about usage — not for any account/signup on
   your end).
3. Enter Ranson, WV's coordinates (or let it use your Home Assistant
   instance's configured Home Zone location, if that's already set to
   Ranson, WV) — leave the station field blank to let HA pick the nearest
   METAR station automatically.
4. Complete setup, then find the entity it created: **Developer Tools →
   States**, filter for `weather.`, and look for the new NWS entity (the
   exact `entity_id` HA assigns depends on the station/location name it
   picked, so it won't necessarily be `weather.ranson_wv_nws`).
5. Either note that real `entity_id` and replace every
   `weather.ranson_wv_nws` placeholder with it (search the project for
   that string), **or** rename the entity itself to match: **Settings →
   Devices & Services → Entities**, find it, gear icon → **Settings →
   Advanced → Entity ID**, and set it to `weather.ranson_wv_nws` — this
   avoids editing multiple files.
6. NWS forecasts (both daily and hourly) are served through Home
   Assistant's modern `weather.get_forecasts` mechanism rather than the
   old deprecated forecast attribute — Clock Weather Card (the card
   behind all three of the cards above) supports this natively, so no
   special config is needed beyond the `entity:` reference.

## 9. Verify

Open each of the 10 views from the nav rail and confirm no
`custom-element-not-found` or YAML parse errors appear. See
[`README.md`](README.md) for the full page-by-page feature list.
