# OpenRCT2 Pelican Egg

[Download egg-openrct2.json](https://raw.githubusercontent.com/danhae/pelican-egg-openrct2/main/egg-openrct2.json)

I fixed the original egg's Debian version mismatch and missing Fontconfig libraries. This egg uses the Trixie release build, preserves the required libraries in `OpenRCT2/lib`, and stops installation on download errors. The official OpenRCT2 icon is embedded directly in the JSON.

Server startup and joining a park were confirmed with OpenRCT2 **v0.5.5** on my Pelican server.

## Setup

1. Import the JSON into Pelican, or update your existing egg.
2. Use `ghcr.io/parkervcp/yolks:debian` and version `v0.5.5`.
3. Assign a primary allocation, for example **11753/TCP**.
4. For the first start, set **Load Latest Autosave** to `false` and **Save File** to `ServerData/save/save.park` (or your own park). For a private LAN server, set **Advertise Server** to `false`.
5. If updating an existing installation, back up your files and run **Reinstall**, then start the server. The installer replaces `OpenRCT2` and its `temp` working directory.

Use a matching OpenRCT2 client version. Original RCT2 game assets are not included. Docker images and game downloads remain external dependencies.

## Credits

Based on the [original Pelican egg](https://github.com/pelican-eggs/games-standalone/tree/main/openrct2) by David Wolfe (Red-Thirten) and parkervcp. [Icon by OpenRCT2](https://github.com/OpenRCT2/OpenRCT2/blob/develop/resources/logo/icon_x256.png). Original MIT license retained.

Maintained by danhae · pommesmail@danielhaehnel.de
