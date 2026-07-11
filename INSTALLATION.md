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

Add to `configuration.yaml`:

```yaml
lovelace:
  mode: yaml
  dashboards:
    kotapish-home:
      mode: yaml
      title: Kotapish Home
      icon: mdi:apple
      show_in_sidebar: true
      filename: kotapish-home-dashboard/src/dashboard.yaml
```

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

## 6. Set up Kiosk Mode

Kiosk Mode is already configured at the top of `dashboard.yaml`
(`hide_header`, `hide_sidebar`, etc.). No extra YAML is required — it
activates automatically once the Kiosk Mode HACS resource is installed.

## 7. Replace placeholder entities

Search the project for the comment tag `# PLACEHOLDER ENTITY` — every
match is an `entity_id` that does not exist in a bare install and must be
pointed at a real entity (or removed) before that card will show live
data. The only entities guaranteed to exist out of the box are:

- `climate.t6_pro`
- `camera.entry_cam`
- `input_select.dashboard_camera` (must have options: `Entry`, `Driveway`, `Deck`)

## 8. iPad setup (kiosk wall mount)

1. Add the dashboard URL to the Home Screen from Safari (`Share → Add to
   Home Screen`) so it opens full-screen without Safari chrome.
2. In iPadOS **Settings → Accessibility → Guided Access**, enable Guided
   Access and triple-click the side button once the dashboard is open to
   lock the iPad to this single app.
3. Set **Auto-Lock** to *Never* for the dedicated wall-mount iPad (Settings
   → Display & Brightness), since this is a permanently powered display.

## 9. Verify

Open each of the 10 views from the nav rail and confirm no
`custom-element-not-found` or YAML parse errors appear. See
[`README.md`](README.md) for the full page-by-page feature list.
