# Epomaker Galaxy100

A 100% wireless mechanical keyboard with Bluetooth, 2.4GHz, and USB connectivity.

* Keyboard Maintainer: [Epomaker](https://github.com/Epomaker)
* Hardware Supported: Galaxy100
* Hardware Availability: [Epomaker](https://epomaker.com)

## Features

- **Tri-mode Connectivity**: Bluetooth (3 devices), 2.4GHz wireless, and USB wired
- **RGB Matrix**: Full per-key RGB with 40+ effects
- **Rotary Encoder**: Volume knob with customizable functions
- **VIA Support**: Full VIA configurator compatibility
- **Hot-swap**: Hot-swappable switches
- **Battery**: Built-in rechargeable battery with indicator

## Building

Make example for this keyboard (after setting up your build environment):

```bash
qmk compile -kb epomaker/galaxy100 -km default
```

Or using make:

```bash
make epomaker/galaxy100:default
```

Flashing example:

```bash
qmk flash -kb epomaker/galaxy100 -km default
```

Or:

```bash
make epomaker/galaxy100:default:flash
```

## Bootloader Mode

To reset the board into bootloader mode, do one of the following:

* Hold the **Reset switch** mounted on the bottom side of the PCB while connecting the USB cable
* Hold the **Escape key** while connecting the USB cable (also erases persistent settings)
* **Fn + R_Shift + Esc** will reset the board to bootloader mode if you have flashed the default QMK keymap
* **Fn + L + Esc** will reset the board to bootloader mode if you have flashed the default QMK keymap

## VIA Support

1. Download and open [VIA](https://usevia.app/)
2. Go to **Settings** → Enable **"Show Design Tab"**
3. Go to **Design** tab → **Load Draft Definition**
4. Select `keyboard_via.json` from this folder
5. The keyboard should now be detected and configurable

## Migration Notes (from older QMK to current version)

This keyboard was migrated from an older QMK firmware version. The following changes were made to ensure compatibility:

### Files Added

1. **Keyboard Definition** (`keyboards/epomaker/galaxy100/`)
   - `keyboard.json` - Hardware configuration, matrix pins, RGB layout
   - `config.h` - Pin definitions, RGB indices, feature flags
   - `galaxy100.c` - Main keyboard logic, indicators, wireless handling
   - `keyboard_via.json` - VIA configurator definition
   - `keymaps/default/keymap.c` - Default keymap with 5 layers
   - `keymaps/default/rules.mk` - Keymap build rules
   - `rgb_record/` - RGB recording feature
   - `wls/` - Wireless support wrapper

2. **Wireless Module** (`keyboards/linker/wireless/`)
   - Complete wireless communication module for Bluetooth and 2.4GHz support
   - Files: `wireless.c`, `transport.c`, `lowpower.c`, `module.c`, `md_raw.c`, `smsg.c`, `rgb_matrix_blink.c`, `lpwr_wb32.c`

### API Updates (Old QMK → New QMK)

| Component | Old API | New API |
|-----------|---------|---------|
| RGB Keycodes | `RGB_MOD`, `RGB_VAI`, `RGB_VAD`, `RGB_SPI`, `RGB_SPD`, `RGB_HUI`, `RGB_TOG` | `RM_NEXT`, `RM_VALU`, `RM_VALD`, `RM_SPDU`, `RM_SPDD`, `RM_HUEU`, `RM_TOGG` |
| EEPROM Read Keymap | `eeconfig_read_keymap()` | `eeconfig_read_keymap(&keymap_config)` |
| EEPROM Update Keymap | `eeconfig_update_keymap(raw)` | `eeconfig_update_keymap(&keymap_config)` |
| EEPROM User Data | `EECONFIG_USER_DATABLOCK` macro | `eeconfig_read_user_datablock()` / `eeconfig_update_user_datablock()` functions |
| USB Protocol | `keyboard_protocol = true` | `usb_device_state_set_protocol(USB_PROTOCOL_REPORT)` |

### Bug Fixes Applied

1. **Missing Header**: Added `#include <stddef.h>` to `rgb_matrix_blink.c` for `NULL` definition

2. **VIA Raw HID**: Updated `md_raw.h` line number mappings for new QMK's `via.c`:
   ```c
   #define _temp_rhs_481 replaced_hid_send // via.c (new QMK)
   ```

3. **RGB Matrix Layout**: Completed the RGB matrix layout in `keyboard.json` to include all 114 LEDs (was missing several keys like PgDn, Print, etc.)

4. **Indicator LED Indices**: Updated LED indices in `config.h` to match the complete RGB layout:
   - `HS_RGB_INDEX_CAPS` - Caps Lock indicator
   - `HS_RGB_INDEX_NUM_LOCK` - Num Lock indicator  
   - `HS_RGB_INDEX_WIN_LOCK` - Win Lock indicator
   - `HS_RGB_INDEX_BAT` - Battery indicator

5. **Indicator Behavior**: Modified `galaxy100.c` to only show white indicator when lock keys are ON, letting normal RGB effects show through when OFF

6. **Battery Indicator**: Disabled red battery LED to prevent constant red key lighting

7. **VIA in Wireless Mode**: Added `KEEP_USB_CONNECTION_IN_WIRELESS_MODE` to `config.h` to allow VIA communication even when using Bluetooth/2.4GHz

8. **Custom Keycode Aliases**: Added short form aliases for custom keycodes in `keyboard.json` to suppress build warnings

### Custom Keycodes

| Keycode | Alias | Description |
|---------|-------|-------------|
| `KC_BT1` | `BT1` | Bluetooth Device 1 |
| `KC_BT2` | `BT2` | Bluetooth Device 2 |
| `KC_BT3` | `BT3` | Bluetooth Device 3 |
| `KC_2G4` | `K2G4` | 2.4GHz Wireless Mode |
| `KC_USB` | `KUSB` | USB Wired Mode |
| `HS_BATQ` | `BATQ` | Battery Query |
| `RP_P0` | `RPP0` | RGB Record Play 0 |
| `RP_P1` | `RPP1` | RGB Record Play 1 |
| `RP_P2` | `RPP2` | RGB Record Play 2 |
| `RP_END` | `RPEND` | RGB Record End |

### Layer Structure

| Layer | Name | Description |
|-------|------|-------------|
| 0 | `_BL` | Base Layer (Windows) |
| 1 | `_FL` | Function Layer (Windows) |
| 2 | `_MBL` | Mac Base Layer |
| 3 | `_MFL` | Mac Function Layer |
| 4 | `_FBL` | Additional Function Layer |

## Resources

- [QMK Build Environment Setup](https://docs.qmk.fm/#/getting_started_build_tools)
- [QMK Make Instructions](https://docs.qmk.fm/#/getting_started_make_guide)
- [QMK Complete Newbs Guide](https://docs.qmk.fm/#/newbs)
- [VIA Configurator](https://usevia.app/)
