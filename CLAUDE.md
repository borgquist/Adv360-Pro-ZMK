# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK firmware configuration repository for the Kinesis Advantage 360 Pro split keyboard. It uses a customized ZMK fork (`ReFil/zmk` branch `adv360-z3.5-2`) with Advantage 360-specific features including RGB underglow and indicator LEDs.

## Build Commands

### Local Build (Docker/Podman)

```bash
make          # Build firmware for both halves
make left     # Build only left half (faster iteration)
```

Firmware outputs to `firmware/` directory as `.uf2` files.

### Cleanup

```bash
make clean_firmware  # Remove built firmware files
make clean_image     # Remove Docker image
make clean           # Remove both
```

### GitHub Actions

Push to trigger automatic builds. Artifacts download as `firmware-no-clique` (legacy) and `firmware-clique` (with ZMK Studio support).

## Architecture

### Key Files

- `config/adv360.keymap` - Main keymap configuration (shared between halves)
- `config/adv360_left.keymap`, `config/adv360_right.keymap` - Per-side keymap includes
- `config/macros.dtsi` - Custom macro definitions
- `config/boards/arm/adv360/adv360_left_defconfig` - Left half board config (BT, RGB, NKRO settings)
- `config/boards/arm/adv360/adv360_right_defconfig` - Right half board config
- `config/west.yml` - West manifest pointing to ZMK fork revision

### Keymap Structure

The keymap uses ZMK's devicetree syntax. Key positions for combos are documented in `assets/key-positions.md`:
- Left keys: 0-6, 14-20, 28-34, 46-51, 60-64
- Right keys: 7-13, 21-27, 39-45, 54-59, 71-75
- Left thumbs: 35-36, 52, 65-67
- Right thumbs: 37-38, 53, 68-70

### Configuration Options

Notable settings in `adv360_left_defconfig`:
- `CONFIG_BT_BAS=n` - Battery reporting over BLE (change to `y` to enable)
- `CONFIG_ZMK_HID_KEYBOARD_EXTENDED_REPORT=n` - Extended NKRO for F13-F24 (change to `y` to enable)
- `CONFIG_ZMK_RGB_UNDERGLOW_MOD_COLOR` - Hex color for CAPS/NUM/SCROLL indicators

## Development Notes

- The web GUI at https://kinesiscorporation.github.io/Adv360-Pro-GUI can edit keymaps, but changes may not be compatible with other ZMK editors
- ZMK documentation at https://zmk.dev/docs applies, except RGB Underglow, Backlight, and Power Management sections which use custom implementations
- Firmware version is embedded via `Mod+V` macro (format: YYYYMMDD-XXXX-YYYYYY where XXXX=branch prefix, YYYYYY=commit hash)
