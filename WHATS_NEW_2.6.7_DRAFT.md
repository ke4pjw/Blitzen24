# Blitzen 24 Edge 2.6.7 (Rocklin): what's new since 2.6.6

**Every SD card boots after every reboot. No more pulling the card.**

Blitzen 24 Edge 2.6.7 (Rocklin) is out on the Main upgrade channel. It's a focused fix release: one bug, chased down to the pin level and verified on real hardware.

---

## The bug

Some SD card brands would leave the controller dark after a firmware update or a Reboot. Pull the card, push it back in, reset, and it booted fine. Not every card did it, which made it a classic "works on my bench" problem.

## What was going on

When the P2 resets, the SD card keeps its power. The card stayed in whatever state the SD driver last left it in, and on some brands that state is one the P2 boot ROM can't get past.

We built a standalone test harness to find out exactly what the card needed, and read the SD pin states before and after each step:

- **Doing nothing (the old behavior):** the card held its data line (MISO) high and **did not boot**.
- **Deselecting it and sending 16 clocks:** the card let go of MISO, but still **did not boot**. A freed bus isn't enough.
- **Sending CMD0 (GO_IDLE_STATE), then deselecting it and sending 16 clocks:** the card answered `R1 = $01` and **booted**.

The card has to be reset to idle before the P2 resets.

## The fix

- **A new `sdrelease` object runs just before every reboot,** after the SD driver is unmounted and stopped. It covers the Reboot page and the reboot after an online upgrade.
- **It clears the SD driver's leftover smart pin modes first.** A P2 smart pin's mode belongs to the pin, not the cog, so it outlives the driver's cog. If it isn't cleared, the smart pins take over SCK and MOSI and CMD0 never reaches the card. This was the gotcha that made the first firmware attempt fail where the harness had worked.
- **Then CMD0, CS high, 16 clocks,** and all four SD pins floated, just as at power on.
- **Debug builds** log the unmount and stop status and the card's CMD0 reply, so the next pin-level mystery is quicker to diagnose.

## Tested

- **The affected card** booted after a Reboot and after a full online upgrade, with no card removal.
- **Two more cards** booted after a Reboot.
- **Every run** got `R1 = $01`.

## One-time note for the upgrade to 2.6.7

The upgrade to 2.6.7 is installed and rebooted by the firmware you're running now, which doesn't have the fix yet. If your card is one of the affected ones, it may need one last power cycle after this upgrade. From then on, every reboot is clean.

---

## Get it

**Source with Boards: [https://griswoldfx.visualstudio.com/_git/Blitzen 24?path=/&version=GBBlitzen_24_Edge_2.6.7_Rocklin](https://griswoldfx.visualstudio.com/_git/Blitzen%2024?path=%2F&version=GBBlitzen_24_Edge_2.6.7_Rocklin)**<br>
**Source code only in Github: [https://github.com/ke4pjw/Blitzen24/tree/Blitzen_24_Edge_2.6.7_Rocklin](https://github.com/ke4pjw/Blitzen24/tree/Blitzen_24_Edge_2.6.7_Rocklin)**<br>
**Binary Distribution: [https://filesgriswoldfxcom.blob.core.windows.net/firmware/Blitzen24/Blitzen 24 Edge 2.6.7 (Rocklin).zip](https://filesgriswoldfxcom.blob.core.windows.net/firmware/Blitzen24/Blitzen%2024%20Edge%202.6.7%20(Rocklin).zip)**

As always, you can update directly from the controller itself on the configs page.

The full history is in `CHANGELOG.md` in the Blitzen 24 repo.

*Thanks to Stephen M. Moraco and Chris Gadd for the `micro_sd_fat32_fs` SD/FAT32 driver.*
