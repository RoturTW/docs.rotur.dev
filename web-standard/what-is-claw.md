# Web standard overview

The Rotur web standard defines how browsers in originOS load and run pages from the Rotur web, and what those pages and browser extensions can rely on: the classes a browser exposes, the commands a page can call, the variables it can read or set, and the events extensions receive. For now it covers OSL browsers only.

Pages on the Rotur web use addresses such as `web://bouncy.flf`. The browser looks up the top-level domain (here `flf`) in a list of hosts, fetches the page source from that host, and runs it.

## Reference implementation

Each page in this section includes the code the **Flufi Browser** uses, as a reference for anyone building a browser.

Summit, the browser built into originOS, also runs Rotur web pages. It supports the [`redirect`](addons-extensions/commands/redirect.md) and [`opentab`](addons-extensions/commands/opentab.md) commands, the `title` key of [`tab_info`](addons-extensions/variables/tab_info.md), and the `page_len` variable described under [`page_width / height`](addons-extensions/variables/page_width-height.md).

## What the standard covers

| Part | What it defines |
| --- | --- |
| [Basic structure](addons-extensions/basic-structure.md) | The layout of an extension definition |
| [Events](addons-extensions/events/README.md) | Events a browser sends to extensions |
| [API](addons-extensions/api/README.md) | The `Page` and `URL` classes |
| [Commands](addons-extensions/commands/README.md) | Commands pages can call to navigate |
| [Variables](addons-extensions/variables/README.md) | Variables pages set or read to talk to the browser |
