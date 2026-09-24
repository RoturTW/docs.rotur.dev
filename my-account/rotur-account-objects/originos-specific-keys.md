# originOS specific keys

These keys live on your account object but only originOS uses them. All of them are writable with the same [update endpoint](README.md#update-a-key) as other keys.

| Key | Description |
| --- | --- |
| `onboot` | App paths that originOS loads on boot. New accounts get the default list shown below. |
| `hostOS` | The operating system the user last signed in from. Example: `"macOS"`. |
| `timezone` | The user's timezone as a whole-hour UTC offset, from `UTC-14` to `UTC+14`. Example: `"UTC+0"`. The `{{ time }}` [bio template](../bio-templates.md) also uses it. |
| `proxy` | A CORS proxy the user has chosen. Apps can send requests through it. Example: `"https://apps.mistium.com/cors?url="`. |
| `wallpaper_mode` | How to draw the wallpaper: `"Fill"`, `"Center"`, `"Fit"` or `"Stretch"`. |
| `scroll_speed` | Mouse scroll speed. Example: `-1.2`. |
| `origin_dock` | Dock modules for the origin dock to render. See the example below. |

Default `onboot`:

```json
[
  "Origin/(A) System/System Apps/originWM.osl",
  "Origin/(A) System/System Apps/Desktop.osl",
  "Origin/(A) System/Docks/Dock.osl",
  "Origin/(A) System/System Apps/Quick_Settings.osl"
]
```

Example `origin_dock`:

```json
[
  "Origin/(A) System/Docks/Modules/main.ode",
  "Origin/(A) System/Docks/Modules/applications.ode",
  "Origin/(A) System/Docks/Modules/time.ode",
  "Origin/(A) System/Docks/Modules/battery.ode"
]
```
