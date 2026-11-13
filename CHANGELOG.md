# Blitzen 24 Edge changelog

All releases since the project started in January 2022. Numbers in brackets are Azure DevOps work items in the Blitzen 24 project. Each heading's date is the date of its release branch (`Blitzen_24_Edge_<version>_<name>`).

How this history was put together:

- **2.6.x** comes from detailed commit messages.
- **Earlier releases** are reconstructed from the version string and code at each release branch, the work items closed before each branch, and the commit messages that describe a change. Many early commits are just "Updated N files", so a few items are placed by closing date. Those are marked "closed with this release".

## 2.6.6 (Rocklin): 2026-11-12

Release build. This includes everything listed below since 2.5.0.

- **Rainbow pixel test** [#73]: Rainbow is now one of the pixel test colors, on the OLED Pixel Test menu and on the web Outputs page. Each output under test shows a color wheel that starts at its own pixel 1 and moves along the string. Rainbow outputs can run alongside blinking single-color outputs.
- **Pixel tests run in their own cog** [#73]: the blink and Rainbow drawing moved out of the watchdog/menu cog into a new `pixeltest.spin2` object. It starts in the fast-RX cog's place while tests run and is stopped before receive restarts.
  - Output layout changes during a test (Outputs, E1.31 or protocol saves) stop and restart it, so it never draws with a half-updated layout.
  - The watchdog keeps only the blinking OLED receive icon.
- OLED: Rainbow appears between White and Off and is drawn one color per letter. Rainbow ports are listed in magenta.

## 2.6.5 Dev: 2026-11-12

- **Upgrade download progress bar** [#75]: during an upgrade, each file's log line shows a progress bar with percent and KB done, then the MD5 check result. Progress is sent as Server-Sent Events on the existing upgrade connection.

## 2.6.4 Dev: 2026-11-12

- **OLED Pixel Test menu** [#42]: a new encoder screen to start and stop pixel tests per port (Red, Green, Blue, White).
  - Ports 25–28 are offered only when DMX is disabled.
  - A short press acts on release, and holding the button for 1.5 s stops all tests.
  - Lists wrap around, and leaving the screen stops all tests.
  - The web Outputs page and the OLED share the same test state.
- **OLED Network screen** [#72]: hostname (without ".local"), IP address, MAC address and speed/duplex moved to a new Network screen next to Version. The Version screen no longer wraps or scrolls. This also fixes a flicker on the Pixel Test screen.
- **Pixel tests keep running when the display sleeps** [#74].

## 2.6.3 Dev: 2026-11-12

- **Home page Input Stats refresh about once a second** [#65]:
  - A new `stats.js` is built in RAM without an SD lookup.
  - The page polls one request at a time, pauses while the tab is hidden, and retries after an error.
- **Unsaved-changes banner and upgrade block** [#64]:
  - Every page shows a banner with a Save link while the running config differs from the saved one, using a CRC of the config block.
  - Upgrades are refused while changes are unsaved, and Reboot warns first.
- Output name and hostname fields are cleared before a new value is decoded, so a shorter name no longer leaves old bytes behind and keeps the banner on [#64].
- **Outputs save keeps the color order of disabled outputs** [#71]. Browsers don't submit disabled fields, so they used to be set to 0.

## 2.6.2 Dev: 2026-11-11

- **OTA download fixes** [#66]:
  - The controller waits for the full HTTP headers.
  - It uses a 1 s receive timeout.
  - It closes a partial file on timeout, so it can't be installed.
  - It uses 4 KB read buffers.

  These fix the MD5 errors and the failed retries.
- SD card files (`blitzen.png`, `style.css`, `script.js`) in the repo now match the published OTA packages.

## 2.6.1 Dev: 2026-11-11

- **DDP input** [#63]: DDP (UDP 4048) can be selected as an alternative input to E1.31.
  - DDP has its own saved channel map: each output has a start channel and a pixel count, and gaps and overlaps are allowed.
  - Switching protocols keeps both configurations.
  - DMX outputs take 510 channels each in DDP mode.
- W6100drv 1.8:
  - The fast-RX cog decodes DDP through a lookup map.
  - The Socket 3 RX buffer is now 4 KB, Sockets 0 and 7 are 1 KB, and the others 2 KB.
- Web:
  - New `ddp.htm`.
  - Protocol selector on `e131.htm`.
  - DDP start channels on `dmx.htm`.
  - The nav link is now "Input Settings".
- Config: the new settings are appended to the end of the saved block, so 2.6.0 configs load unchanged and 2.6.0 ignores the new fields.

## 2.6.0 Dev: 2026-11-10

- **E1.31 drain fix** [#62]: the receive cog consumes one datagram at a time, using the packet length, instead of flushing the socket buffer after each packet. Queued universes are no longer dropped.
- W6100drv 1.7: faster bus timing.
- Repository: the Azure Pipelines mirror pipeline triggers on pushes to any branch and no longer pushes `origin/HEAD` as a branch.

## 2.5.0 (Rocklin): 2026-11-07

- **mDNS works with the default hostname** [#59]: the hostname is lowercased before Ethernet starts, so mDNS queries match.
- **DNS works after booting with DHCP** [#60]: W6100drv adds `Close()`, and Socket 0 is closed after DHCP and DNS, so it can be reopened cleanly.
- **Stephen Moraco's SD card driver upgraded to upstream 1.8.0 (`micro_sd_fat32_fs_v180`), with boot-file fixes** [#61]: compacting `_BOOT_P2.BIX` can no longer corrupt other files, and a fragmented boot file can no longer pass the contiguity check.
- `jm_gamma8.spin2` added; the old defrag driver removed.
- Repository:
  - Azure Pipelines CI set up (2026-04-30).
  - Board files moved to `/Hardware` (2026-04-16).

## 2.4.2 (Rocklin): 2026-03-27

- The boot-file check after an upgrade is turned on. If `_BOOT_P2.BIX` is fragmented, it is compacted, or the upgrade log says to defragment the card by hand.

## 2.4.1 (Rocklin): 2026-03-27

- Stephen Moraco's SD driver, now built with its defrag support (`micro_sd_fat32_fs_defrag`).
- An SD mount error is shown on the OLED, with its error code.
- The upgrade log can report whether the boot file is fragmented. The check was off in this release and turned on in 2.4.2.

## 2.4.0 Dev (Rocklin): 2026-03-20

- **New SD card / FAT32 driver: `micro_sd_fat32_fs`** by Stephen M. Moraco (handle-based API) and Chris Gadd (original driver). This is a high-performance FAT32 microSD filesystem driver for the P2. It replaces the `FAT32` object used since Pre-Release 1. The old `FAT32.spin2`, `sdspi_bashed2.spin2` and `sdspi_with_audio.spin2` were removed (2026-03-13).
- **Multithreaded web server** [#57]: three cooperative web server tasks, so more than one browser request is handled at a time.
- **Hostname and mDNS** [#56]: a configurable hostname, and the controller answers `<hostname>.local`.
- **Output names** [#53]: a text box to describe each output, saved in the config.
- **Downloaded config file name** [#54] changed.
- **A new config sets the pixel type** (RGB, BGR and so on) [#55].
- Web: URL decoding and HTML escaping for user-entered text.
- Closed with this release:
  - Duplicate W6100 data bus pin constants [#6].
  - Files over 64 KB crashed the web server [#9].
  - SendData overwrote its buffer at 10 Mbps [#13].
  - Comment cleanup in `tt_rgbx_pixel.spin2` [#35].

## 2.3.1 Dev (Rocklin): 2025-10-10

- **Ethernet and E1.31 status** [#41]: the OLED status icon now follows the per-second packet and discard rates, not just whether a counter changed.

## 2.3.0 (Rocklin): 2025-10-10

- **Online upgrade to development builds** [#51]: the Upgrade page can use the Dev channel (`/devfirmware/`) as well as the release channel.
- Fixed the brightness slider value on the Outputs page.
- Closed with this release:
  - iOS Safari form posts [#46].
  - `outputs.js` failing to load now and then [#47].
  - Heavy glitching in 2.2.3 [#52].

## 2.2.3 (Rocklin): 2025-04-01

- **Disable All Tests button** on the Outputs page [#43].
- Saving E1.31 settings no longer switches the DMX port back to long-range output [#44].
- E1.31 saves respect pixel test (blink) mode [#45].
- First workaround for iOS Safari sending a form in two parts.

## 2.2.2 (Rocklin): 2025-03-24

- **Long Range Board on the DMX port** [#40]: outputs 25–28 can drive a long-range pixel board instead of DMX, selected with the DMX enable setting.
- Memory layout documented in `MemoryStructure.md`.

## 2.0 (Rocklin): 2024-10-30

- **OLED screens on the rotary encoder**: Stats, a QR code for the controller's web address, and Version.
- **Display timeout** [#39]: the OLED turns off after a set time. The work item was closed in February 2025.
- The hardware version is detected at boot.

## 1.3 (Music City): 2023-02-07

- **Duplicate E1.31 universe detection** [#30], including within the output being edited.
- **DMX settings page** (`dmx.htm`).
- **Boot logo on the OLED** (`GFX.RAW`).
- Fixed pin assignments for unused pins (schematic and W6100 code) [#33], and fixed static pin assignments in the SSD1331 code [#34].

## 1.2 (Music City): 2023-01-22

- Upgrade reliability: longer receive timeouts, socket disconnects after each download, and a retry when a download fails.

## 1.1 (Music City): 2023-01-21

- **MD5 checks on downloaded upgrade files** [#31].
- **Live upgrade progress** in the browser [#32].
- Reboot page.
- Fixed HTTP response codes during upgrades.

## 1.0 (Music City): 2023-01-13

First release. This is also the `Pre-Release_4` branch.

- **Remote firmware updates** [#26]: downloads a manifest and files from the upgrade server, then installs them.
- **DNS resolver** [#27].
- **Version and uptime status** [#29].
- Config download from the web.
- Fixed a slow web server when built with Propeller Tool 2.9.2 [#28].

## Pre-Release 2: 2023-01-03

- **Quad DMX output** [#18], with DMX timing fixed [#25].
- **"Test" diagnostic menu** [#20]: blinking pixel tests per output.
- **Ethernet speed and duplex selection** [#23].
- The HDMI driver was removed and all video output now goes to debug [#24].

## Pre-Release 1: 2022-12-30

The first controller firmware, built through 2022.

- **E1.31 input** [#1]: multiple universes mapped to multiple pins, with one cog driving 8 WS2812B outputs.
- **WS2811/WS2812 output ports** [#2], with brightness control.
- **Web-based configuration** [#8], saved to the SD card:
  - network settings [#11];
  - E1.31 [#14];
  - outputs [#15];
  - network page validation [#16];
  - an extra MIME type [#10].
- **W6100 Ethernet**, with locks for exclusive bus access [#7] and a forced 10 Mbps option [#12].
- **SSD1331 OLED display and input switches** [#19], with packet counters on the home page.
- A cog-less SD card driver (thanks to Rayman).
- Board layout [#3], Ethernet switch chip [#4] and RTC chip [#5] chosen, and prototype boards ordered (August 2022).
- Fixes:
  - lockups when a universe was added or removed [#21];
  - connectivity status not updating before the DHCP reply [#22];
  - repeated + and − on a freshly loaded Outputs page [#17];
  - pin setup for the second WS2812 output.

## Credits

- **Stephen M. Moraco and Chris Gadd**: the `micro_sd_fat32_fs` SD card / FAT32 filesystem driver, used since 2.4.0 and upgraded to upstream 1.8.0 in 2.5.0.
- **Rayman**: the cog-less SD card driver used from 2022 (Pre-Release 1).

## Open work items

- [#58] NTP client.
- [#68], [#69], [#70] Configure the Blitzen 24 from xLights: discovery, a JSON config API, and an xLights driver.

## Known issues (deferred)

- [#67] The pixel buffer is too small for 24 outputs × 1,020 pixels plus DMX in DDP mode, so the DDP limit is 23,909 pixels. DMX channels 511–512 send the next DMX output's channels 1–2.
- [#76] E1.31 DMX universes copy only 510 of 512 channels.
