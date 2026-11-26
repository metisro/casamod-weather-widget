# ReCasaOS CasaMOD - Weather Widget v1.4.1

This mod adds a weather card to both the classic and beta ReCasaOS dashboards.
It detects the active dashboard panel and follows it when switching modes. On
the beta dashboard, its settings offer a header widget, an expanded card below
Backups, both, or disabled.

It is based on the Weather widget idea from
[IceWhaleTech/CasaOS-UI pull request #257](https://github.com/IceWhaleTech/CasaOS-UI/pull/257/commits),
but adapted as a standalone CasaMOD extension instead of a compiled Vue
component.

## Features

- Injects a Weather widget into the classic sidebar or beta dashboard's side panel.
- Adds an optional compact beta header button with a five-day forecast popover.
- Lets beta users choose **Header**, **Expanded card**, **Both**, or **Disabled**.
- Keeps the card title aligned to the left and lays out placement choices in a
  compact two-column grid; settings remain scrollable on short screens.
- Shows current temperature, apparent temperature, humidity, wind speed, and
  weather description.
- Includes a compact hourly temperature chart for the next 8 hours.
- Shows temperature-only tooltips when hovering over chart dots or nearby graph
  points.
- Includes a stats-style **10 days** button that switches the widget content to
  a compact 10-day forecast while keeping the header visible.
- Shows a clickable clock that toggles between 12-hour and 24-hour time.
- Allows custom city, latitude, longitude, refresh interval, and Celsius or
  Fahrenheit settings.
- Includes CasaOS, Aero, and macOS-inspired Liquid Glass themes.
- Uses Open-Meteo, so no bundled OpenWeatherMap API key is required.
- Shows today's precipitation chance and temperature range in the header popover.
- Persists settings through ReCasaOS' file API at:

```text
/DATA/AppData/casamod/weather-widget.json
```

## Installation on ReCasaOS

Copy the complete `weather-widget-RE` folder (including `mod.js`, `themes/`,
and `icons/`) to the ReCasaOS host:

```text
/DATA/AppData/casamod/mod/weather-widget-RE
```

Restart CasaMOD and hard-refresh the ReCasaOS dashboard. To uninstall, remove
the folder and restart CasaMOD. The widget stores its settings at
`/DATA/AppData/casamod/weather-widget.json` through ReCasaOS's `/v1/file` API.

For new installations, the beta display setting defaults to **Both** so the
top-bar shortcut and expanded card are visible. Existing saved settings remain
in effect. It changes only the beta dashboard; the classic dashboard continues
to show its sidebar card.

## Usage

- Click the clock in the widget header to switch between 12-hour and 24-hour time.
- Click the refresh icon to update weather immediately.
- Click the settings icon to configure a custom city or coordinates.
- In settings, click **Look Up City** to resolve coordinates using Open-Meteo
  geocoding.
- In settings, use the temperature unit switch to choose Celsius or Fahrenheit.
- In settings, use the theme switch to choose CasaOS, Aero, or Liquid Glass.

## Themes

Theme CSS is split into separate files:

```text
themes/base.css
themes/casa.css
themes/aero.css
themes/liquid-glass.css
```

The Aero theme does not use the original Windows gadget images. It uses selected
Meteocons Fill SVG icons from `@meteocons/svg`, which is MIT licensed. The
selected icons and attribution are stored in:

```text
icons/meteocons-fill/
```

The selected theme is stored in the CasaOS weather config and loaded by the mod
at runtime.

### Creating A Custom Theme

Keep shared sizing and layout rules in `themes/base.css`. A custom theme should
only override colors, materials, borders, shadows, artwork placement, and other
visual details.

1. Create a new CSS file, for example:

   ```text
   themes/my-theme.css
   ```

2. Add the theme to the `THEMES` registry near the top of `mod.js`:

   ```js
   myTheme: {
     label: "My Theme",
     file: "themes/my-theme.css",
     className: MOD_ID + "-theme-my-theme"
   }
   ```

3. Scope every CSS selector to the theme root class:

   ```css
   .casamod-weather.casamod-weather-theme-my-theme {
     --casamod-weather-chart-line: rgb(120, 220, 255);
     --casamod-weather-chart-fill: rgba(120, 220, 255, .25);
     --casamod-weather-chart-grid: rgba(255, 255, 255, .12);
     --casamod-weather-chart-text: rgba(255, 255, 255, .75);
   }

   .casamod-weather.casamod-weather-theme-my-theme .casamod-weather-card {
     background: rgba(20, 30, 45, .8);
     border: 1px solid rgba(255, 255, 255, .2);
   }
   ```

The same theme root class is applied to the settings overlay, so modal styles
can use selectors such as:

```css
.casamod-weather.casamod-weather-theme-my-theme .casamod-weather-modal {
  /* Custom modal appearance */
}
```

Useful scene classes are applied automatically:

```text
casamod-weather-scene-sun
casamod-weather-scene-moon
casamod-weather-scene-partly-cloudy
casamod-weather-scene-moon-cloudy
casamod-weather-scene-cloudy
casamod-weather-scene-fog
casamod-weather-scene-showers
casamod-weather-scene-rain
casamod-weather-scene-snow
casamod-weather-scene-hail
casamod-weather-scene-thunderstorm
casamod-weather-scene-wind
```

Use these classes to change colors or artwork positioning for particular
weather conditions. Avoid unscoped selectors such as `#casamod-weather-icon`,
because they affect every theme.

## How It Differs From CasaOS-UI PR #257

The original PR added a `src/widgets/Weather.vue` component and a `chart.js`
dependency to CasaOS-UI.

This CasaMOD version does not require rebuilding CasaOS-UI. It injects a widget
at runtime and draws the forecast chart with the browser canvas API. It also
uses Open-Meteo instead of the demo OpenWeatherMap API key from the PR.

The PR also contains a separate Cloudflared/Syncthing block refactor. That is
not included in this weather extension because it targets a different dashboard
component and should be packaged as a separate CasaMOD if needed.

## Development

Before publishing a change, run:

```powershell
node --check mod.js
```

## Author

Created by **metisro**.
Part of CasaMOD - [https://github.com/metisro/CasaMOD](https://github.com/metisro/CasaMOD)
