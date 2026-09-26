# OpenRCT2 Pelican egg — compatibility fix

![Official OpenRCT2 icon](assets/openrct2-icon.png)

Maintained by [danhae](https://github.com/danhae). Based on the [Pelican Eggs OpenRCT2 egg](https://github.com/pelican-eggs/games-standalone/tree/main/openrct2), originally authored by David Wolfe (Red-Thirten) and parkervcp.

[Download the egg JSON](https://raw.githubusercontent.com/danhae/pelican-egg-openrct2/main/egg-openrct2.json)

## Why I made this

My OpenRCT2 server failed before it could even print its version:

```text
./OpenRCT2/openrct2-cli: error while loading shared libraries:
libicuuc.so.72: cannot open shared object file: No such file or directory
```

The original egg explicitly downloaded `Linux-bookworm-x86_64.tar.gz` (Debian 12). The current Dockerfile for `ghcr.io/parkervcp/yolks:debian` uses Debian 13 (Trixie). The Bookworm binary expects ICU 72, which was unavailable in the running container.

I changed the release asset selector to `Linux-trixie-x86_64.tar.gz` to match the current runtime. OpenRCT2 v0.5.5 provides this asset. I also enabled installation failure handling with `set -e`, added curl HTTP-error checking, and made unknown release tags fail instead of falling back to an unfiltered list of downloads.

## Follow-up: missing Fontconfig

After switching to the Trixie build, the server reported `libfontconfig.so.1` missing. The generic runtime does not include Fontconfig. I updated the installation container to `debian:trixie-slim` and made the installer copy Fontconfig and its required shared libraries into `OpenRCT2/lib`. The egg's existing `LD_LIBRARY_PATH` loads them there at runtime. Installing packages only inside the temporary installer container would not fix the server container.

The GitHub Actions workflow installs the actual egg into a shared directory, then checks `ldd` and runs `openrct2-cli --version` as the normal server user inside the unmodified runtime image. This checks library loading, not multiplayer connectivity or a complete park-hosting session.

## Install or update in Pelican

1. Back up your server files, especially `ServerData`, parks, configuration, and original game assets.
2. Download the JSON above and import it as an egg, or update your existing OpenRCT2 egg with it. Ensure your server uses the updated egg and installation script.
3. Keep the runtime image `ghcr.io/parkervcp/yolks:debian`.
4. Set **OpenRCT2 Version** to `v0.5.5` (asset availability checked) or `latest`. Other release tags must provide a Trixie x86_64 asset. Use matching client/server versions.
5. Assign a valid primary server allocation. If the rendered startup command says `--port 0`, correct the allocation before starting.
6. For the first start, set **Load Latest Autosave** to `false` and select an existing park using **Save File**. The inherited autosave selector assumes an appropriate autosave exists; an empty autosave directory can otherwise be selected as the input.
7. Run **Reinstall** to replace the OpenRCT2 binaries, then start the server. A restart alone does not replace the Bookworm build.

The inherited installer replaces `OpenRCT2` and a working directory named `temp`. Keep your own files out of those directories before reinstalling. The `develop` source-build option is inherited and has not been validated for this fix; use a release for this documented path.

## Validation and limitations

- Egg JSON parses successfully and the embedded installer passes `bash -n`.
- The official v0.5.5 release includes the selected Trixie x86_64 archive.
- Successful park hosting on a Pelican host still requires confirmation. The automated runtime check covers installation and binary loading; it does not test server allocations, game assets, or multiplayer connectivity.
- The generic `:debian` image tag is mutable. If it changes Debian versions again, the runtime and selected OpenRCT2 build must be reviewed together.
- Original RCT2 assets are not included. Supply your own game data as required. OpenRCT2's own data files must match the installed OpenRCT2 version.
- Docker images, OpenRCT2 releases, and the upstream default park remain external dependencies.

## Sources and credits

- [Original egg](https://github.com/pelican-eggs/games-standalone/tree/main/openrct2)
- [Runtime Dockerfile](https://github.com/parkervcp/yolks/blob/master/oses/debian/Dockerfile)
- [OpenRCT2 v0.5.5](https://github.com/OpenRCT2/OpenRCT2/releases/tag/v0.5.5)

Original author credits and the MIT license are retained. Fork maintainer contact: pommesmail@danielhaehnel.de.

Icon: [official OpenRCT2 artwork](https://github.com/OpenRCT2/OpenRCT2/blob/develop/resources/logo/icon_x256.png), credited to the OpenRCT2 project; not generated artwork.
