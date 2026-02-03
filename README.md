# ZMK Config for Corne Mini

Corne mini (42 keys) keyboard configuration with ZMK firmware for wireless operation.

## Keymap

Ported from Keyboardio Atreus (QMK/Vial) configuration.

### Features

- **Home Row Modifiers**: Bottom row (Z=Ctrl, X=Shift, C=Alt, V=Gui)
- **Tap-Hold**: 200ms tapping term (balanced flavor)
- **4 Layers**: Base, Navigation, Reset/Bluetooth, Numbers/Symbols
- **Japanese IME**: 無変換(LNG2) / 変換(LNG1) keys

### Base Layer

```
┌─────┬─────┬─────┬─────┬─────┬─────┐   ┌─────┬─────┬─────┬─────┬─────┬─────┐
│ TAB │  Q  │  W  │  E  │  R  │  T  │   │  Y  │  U  │  I  │  O  │  P  │ BSP │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│ ESC │  A  │  S  │  D  │  F  │  G  │   │  H  │  J  │  K  │  L  │  -  │  '  │
├─────┼─────┼─────┼─────┼─────┼─────┤   ├─────┼─────┼─────┼─────┼─────┼─────┤
│SHIFT│Z/CTL│X/SFT│C/ALT│V/GUI│  B  │   │  N  │  M  │  ,  │  .  │  /  │ENTER│
└─────┴─────┴─────┴─────┼─────┼─────┤   ├─────┼─────┼─────┴─────┴─────┴─────┘
                        │LNG2 │ SPC │   │ BSP │LNG1 │
                        │ /L1 │/GUI │   │ /L3 │ /L2 │
                        └─────┴─────┘   └─────┴─────┘
```

### Layer 1: Navigation (LNG2/無変換 hold)

- Arrow keys, Home/End, Page Up/Down
- Modifier keys on bottom row

### Layer 2: Reset/Bluetooth (LNG1/変換 hold)

- Bluetooth profile switching (BT1-BT5)
- Bluetooth clear, Output toggle (USB/BLE)
- Bootloader mode

### Layer 3: Numbers/Symbols (Backspace hold)

- Number row
- Symbols (=, \, `, ;, ', [, ])

## Hardware

- **Keyboard**: Corne mini (42 keys)
- **Controller**: nice!nano v2
- **Firmware**: ZMK

## Build

Firmware is built automatically via GitHub Actions on push.

1. Push changes to this repository
2. GitHub Actions builds firmware
3. Download `.uf2` files from Actions artifacts

## Flash

1. Download firmware from GitHub Actions artifacts
2. Put nice!nano into bootloader mode (double-tap reset button)
3. Drag `.uf2` file to the mounted drive
4. Repeat for both halves (left and right)

## References

- [ZMK Documentation](https://zmk.dev/docs)
- [Corne Shield](https://zmk.dev/docs/hardware)
- Original config: [qmk_for_atreus](https://github.com/ogatatomoyuki/qmk_for_atreus)
