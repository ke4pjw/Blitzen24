# Introduction
The Blitzen 24 is a 24-port pixel controller for E1.31 (sACN) and DDP, built on the Parallax Propeller 2 (P2).

[![Video Example](http://i3.ytimg.com/vi/Dj3J5ynzC24/hqdefault.jpg)](https://youtu.be/Dj3J5ynzC24)

# Current State
The current release is **Blitzen 24 Edge 2.6.6 (Rocklin)**. What changed in each release is in [CHANGELOG.md](CHANGELOG.md).

Evaluation boards are available. Please contact me at ttrapp at ieee dot org if you would like to purchase one.

# Features
- **24 WS2811/WS2812 pixel outputs**, with per-output color order and brightness.
- **4 extra outputs (25–28)** for either quad DMX or a long-range pixel board.
- **E1.31 (sACN) or DDP input**, selectable per controller. Each protocol keeps its own saved layout, so you can switch back and forth without losing either one.
- **W6100 Ethernet**, with DHCP or a static address, DNS, and mDNS (`<hostname>.local`).
- **Web configuration**: network, outputs, inputs, DMX and saved configs. Live input stats, and a banner when changes haven't been saved.
- **Online upgrades** from the configs page, with a choice of the release or development channel. Each file shows download progress and is checked against its MD5.
- **OLED and rotary encoder**, with these screens: Stats, a QR code for the controller's web page, Network, Version, and Pixel Test.
- **Pixel tests**: Red, Green, Blue, White and Rainbow on each port. Start them from the OLED or the web Outputs page.

# Getting It
- **Source with Boards:** [https://griswoldfx.visualstudio.com/_git/Blitzen 24](https://griswoldfx.visualstudio.com/_git/Blitzen%2024). Each release has a branch named `Blitzen_24_Edge_<version>_<name>`.
- **Source code only in Github:** [https://github.com/ke4pjw/Blitzen24](https://github.com/ke4pjw/Blitzen24), a mirror of this repo.
- **Binary Distribution:** [https://filesgriswoldfxcom.blob.core.windows.net/firmware/Blitzen24/Blitzen 24 Edge 2.6.6 (Rocklin).zip](https://filesgriswoldfxcom.blob.core.windows.net/firmware/Blitzen24/Blitzen%2024%20Edge%202.6.6%20(Rocklin).zip). It holds everything that goes on the SD card.
- **Updates thread:** [Parallax Forums](https://forums.parallax.com/discussion/174560/blitzen-24-e1-31-pixel-light-controller-updates-thread/)

As always, you can update directly from the controller itself on the configs page.

# Repository Layout
| Path | Contents |
|------|----------|
| `Blitzen24.spin2` | Top-level firmware: web server, configuration, OLED menus |
| `W6100drv.spin2` | W6100 Ethernet driver and the fast E1.31/DDP receive cog |
| `pixeltest.spin2` | Pixel test patterns, which run in their own cog |
| `tt_rgbx_pixel.spin2`, `tt_dmx_tx.spin2` | Pixel and DMX output drivers |
| `micro_sd_fat32_fs_v180.spin2` | SD card / FAT32 filesystem driver |
| `SDCARD/` | Web pages and other files that go on the SD card |
| `Hardware/` | KiCad schematics and board layout |
| `MemoryStructure.md` | Hub memory and config layout |
| `CHANGELOG.md` | Release history |

# Known Bugs
- [#67](https://griswoldfx.visualstudio.com/Blitzen%2024/_workitems/edit/67/) In DDP mode, the pixel buffer is too small for 24 outputs × 1,020 pixels plus DMX.
- [#76](https://griswoldfx.visualstudio.com/Blitzen%2024/_workitems/edit/76/) E1.31 DMX universes copy only 510 of 512 channels.

# To Do
- [#58](https://griswoldfx.visualstudio.com/Blitzen%2024/_workitems/edit/58/) NTP client.
- [#68](https://griswoldfx.visualstudio.com/Blitzen%2024/_workitems/edit/68/), [#69](https://griswoldfx.visualstudio.com/Blitzen%2024/_workitems/edit/69/), [#70](https://griswoldfx.visualstudio.com/Blitzen%2024/_workitems/edit/70/) Configure the Blitzen 24 from xLights: discovery, a JSON config API, and an xLights driver.

# Build
Releases are built with the Spin Tools compiler (`spinc`, 0.57.3):

```
spinc -u -b -o _BOOT_P2 Blitzen24.spin2
```

`-u` removes unused methods and `-b` writes a binary. Copy the result to the SD card as `_BOOT_P2.BIX`, along with the files in `SDCARD/`.

# Credits
- **Stephen M. Moraco and Chris Gadd** wrote the `micro_sd_fat32_fs` SD card / FAT32 driver.
- **Rayman** wrote the cog-less SD card driver used in early releases.

# Contribute
If you wish to contribute, please email me. ttrapp at ieee dot org
