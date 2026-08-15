# fix9900

Fork of [Bill-lulu/uc9900](https://github.com/Bill-lulu/uc9900) by Daniel Gentleman
(thoughtfix, <daniel@danielgentleman.com>). MIT licensed, same as
upstream. See `LICENSE.txt`.

## Where this code comes from

Three separate codebases, maintained by three different people, are stacked
on top of each other here:

- **[ZMK](https://github.com/zmkfirmware/zmk)**: the open-source keyboard
  firmware project everything is built on. We don't touch this layer.
- **[ZitaoTech's ZMK fork](https://github.com/ZitaoTech/zmk)**
  (`bbkeyboard_tp` branch): adds the actual BB9900/uConsole hardware
  support on top of ZMK core (the trackpad sensor driver, board
  definition, etc.).
- **[Bill-lulu/uc9900](https://github.com/Bill-lulu/uc9900)**: the
  keymap/config layer on top of ZitaoTech's hardware support, adapted for
  the uConsole. Most of what this fork fixes (see below and
  `config/NOTES.md`) turned out to be porting mistakes in this layer, not
  bugs in ZMK or in ZitaoTech's hardware support.

Our own changes live in two places:

- **This repo** (`config/`): keymap-level fixes (volume, right-side
  modifiers, Pause/Start, the bootloader combo).
- **[thoughtfix/zmk](https://github.com/thoughtfix/zmk)**, branch
  `bbkeyboard_tp-fix9900`: our fork of ZitaoTech's ZMK fork, for fixes that
  needed real C changes rather than just a keymap edit (the Enter key's
  matrix-transform bug, the CapsLock/scroll-mode decoupling, the trackpad
  click-to-toggle scroll behavior, and the volume shift-intercept behavior).
  `config/west.yml` points here instead of at ZitaoTech's branch.

We are not attempting to track either upstream via rebase. Both have
diverged enough by now, and Bill-lulu's own history is rough enough, that a
clean rebase isn't realistic. If either upstream lands a fix we need, we'll
cherry-pick or manually backport that specific change rather than rebasing
this fork wholesale. Treat this as a standalone maintenance line from here
on, not a tracking branch.

## ⚠️ Flash at your own risk

This firmware controls a real, physical keyboard and trackpad. A bad flash
can leave the board unresponsive to normal input. The bootloader-entry
combos below exist specifically so you can recover from that, but we can't
guarantee every board, cable, or host setup behaves identically. **Before
flashing anything from this repo, keep a copy of whatever `.uf2` your board
currently runs** (e.g. `CURRENT.UF2`), so you always have a known-good file
to flash back to if something goes wrong.

Every change below has been flash-tested on real hardware, but this is a
small, unpaid, spare-time fork, not a supported product. No warranty,
express or implied. Use at your own risk.

## Major changes compared to uc9900 source

1. Fixed volume control. Pressing the speaker key lowers volume. Pressing it while holding Shift raises volume. Pressing it with Fn mutes/unmutes.
2. Caps Lock is now ONLY Caps Lock: no LED, and no effect on the trackpad. Caps Lock and Scroll Lock previously switched the trackpad into scroll mode silently as a side effect; that's gone too.
3. Clicking down on the trackpad is no longer a mouse click. Mouse clicks stay on the dedicated left/right buttons. A trackpad click now toggles between cursor mode and scroll mode, with some cardinal-direction snapping so scrolling doesn't drift diagonally.
4. Fixed Right Alt and Right Ctrl, which went completely dead under either Fn layer. Some of that space was previously reserved for BLE controls, but the keyboard inside the uConsole uses USB only, so those BLE bindings could be freed up.
5. Fixed the Pause/Start key, which was firing phantom mouse-button clicks instead of a Pause keypress.
6. Fixed a completely dead Enter key, caused by a one-column error in the keyboard's matrix transform.
7. Added a second, safer "flash mode" combo: **LCtrl+LAlt+\\** (in addition to the existing Fn+\\), because a two-key combo that essentially unplugs the keyboard/trackpad deserves to sit behind something less accidental. Fn+\\ is NOT YET REMOVED. I'd like to confirm with Bill-lulu that it's safe to drop, since other `uc9900` users may already depend on it, and I don't have a USB jig here to independently confirm the side button/switch still reaches flash mode if the key-combo path is ever removed entirely.

See [`config/NOTES.md`](config/NOTES.md) for in-depth details on all of the above, including a couple of warnings worth knowing before you start poking at this yourself.

## Installing

Firmware builds automatically via GitHub Actions on every push (same CI setup as upstream). Fork this repo, push any change, then grab `bb9900-zmk.uf2` from the resulting Actions run's artifacts. No local toolchain required for that path.

If you'd rather build locally, useful if you want to modify the firmware yourself, or just don't want to trust a stranger's compiled binary for something with keyboard input (a reasonable instinct), see the "Building locally" section of `config/NOTES.md`.

## Found a bug?

Open a GitHub Issue on this fork. Include what you pressed, what you expected, and what actually happened. A `libinput debug-events --show-keycodes` capture (see `config/NOTES.md`) is the single most useful piece of evidence if you're on a Linux host and can grab one.

## AI disclosure

Generative AI was used in the development of this fork. Visual Studio Code with the Claude extension was instrumental in providing code quality and speed that wouldn't have come from a lone developer in a reasonable amount of time. This is especially true in keeping track of the large tables of keypress-to-function mappings and the rabbit-hole investigations.

A more detailed accounting of specific AI contributions will be added here after independent human review of the code.

## From this part down is the original README.md of Bill-lulu
# First thanks to [Zitaotech](https://github.com/ZitaoTech)

I modify his fireware to our project,whitout his help ,we cannt see trackpad on uconsole ,thanks to him and his many interesting productions.

# uconsole BB9900 wireless/usb Keyboard: zmk-config
------------------------------

The key:

Part ONE--USB

1.when connect uconsole via usb,ble dont work!

Part TWO--BLE

1.RFN+1 2 3 is three different equipment ,RFN+ESC is Clean BLE(when you wanna connect new equipment and clean you ble info)  

2.RFN+ (\\|) is bootloader

3.RFN+LFN is soft-reset


New update need you help

1.LFN +trackpad is ↑↓←→

2.Caps light！！

3.outside Crystal oscillator works！

--------------------------------
Hey 👋 welcome. Use this repo to generate your own ZMK keymap for the BB9900 BLE keyboard.  
[Keycode that you can use in ZMK firmware](https://zmk.dev/docs/codes)  
[Different behaviors that you can use in ZMK firmware](https://zmk.dev/docs/behaviors)  
## Get started
0. Register a github account if you don't have one.
1. Fork this repo.![fork](https://github.com/ZitaoTech/zmk-config_9900/assets/145678024/4ffc71b9-0ed3-4ae9-ace7-99078dd1d9bc)  
2. Open up `config/bb9900.keymap` and edit the keymap to your liking.![image](https://github.com/ZitaoTech/zmk-config_9900/assets/145678024/a0900a5c-6650-4794-9d11-a17c380a973d)  
3. After editing the keymap, choose commit changes![image](https://github.com/ZitaoTech/zmk-config_9900/assets/145678024/c708dbd0-6c90-49da-aeda-053668ae43c8)
 and then check the Github Actions section.![image](https://github.com/ZitaoTech/zmk-config_9900/assets/145678024/fb534054-add6-4517-8643-8270cbf6d8c7)
 Your new firmware file should be available for download.![image](https://github.com/ZitaoTech/zmk-config_9900/assets/145678024/ae6a1646-c8ab-4966-b969-12e68ecaa0ab)
![image](https://github.com/ZitaoTech/zmk-config_9900/assets/145678024/a6140108-9e27-4d51-aa42-ba12233b8738)
5. Unzip the firmware.zip file. You should see one files: `bb9900-zmk.uf2`.  
6. Flash the keyboard with your new firmware.[How to flash the firmware](https://github.com/ZitaoTech/BB9900-USB_BLE_Keyboard?tab=readme-ov-file#-how-to-update-the-firmware---) 
