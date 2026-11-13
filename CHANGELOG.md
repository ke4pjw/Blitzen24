# Blitzen 24 Edge changelog

Changes since 2.5.0 (Rocklin). Numbers in brackets are Azure DevOps work items in the Blitzen 24 project.

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

## Repository

- The Azure Pipelines mirror pipeline triggers on pushes to any branch and no longer pushes `origin/HEAD` as a branch.

## Known issues (deferred)

- [#67] The pixel buffer is too small for 24 outputs × 1,020 pixels plus DMX in DDP mode, so the DDP limit is 23,909 pixels. DMX channels 511–512 send the next DMX output's channels 1–2.
- [#76] E1.31 DMX universes copy only 510 of 512 channels.
