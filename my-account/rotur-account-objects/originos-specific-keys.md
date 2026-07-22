# originOS specific keys

These keys live on your account object but are only used by originOS. They are all writable.

```
onboot
- an array of app paths that originOS loads on boot
  eg. [
    "Origin/(A) System/System Apps/originWM.osl",
    "Origin/(A) System/System Apps/Desktop.osl",
    "Origin/(A) System/Docks/Dock.osl",
    "Origin/(A) System/System Apps/Quick_Settings.osl"
  ]

hostOS
- the os that the user last logged in on
  eg. macOS

timezone
- the user's timezone as a UTC offset
> this is also used by the {{ time }} bio template to show your local time
  eg. UTC+0

proxy
- a cors proxy that the user can configure. Apps in the operating system
  can use it to make requests through
  eg. https://apps.mistium.com/cors?url=

wallpaper_mode
- how the os should render the wallpaper, one of ["Fill", "Center", "Fit", "Stretch"]
  eg. "Fill"

scroll_speed
- how fast the mouse should scroll
  eg. -1.2

origin_dock
- an array of dock modules for the origin dock to render
  eg. [
    "Origin/(A) System/Docks/Modules/main.ode",
    "Origin/(A) System/Docks/Modules/applications.ode",
    "Origin/(A) System/Docks/Modules/time.ode",
    "Origin/(A) System/Docks/Modules/battery.ode"
  ]
```
