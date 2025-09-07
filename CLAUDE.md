# Budget Wireless Corne Keyboard - Codebase Documentation

## Project Overview
This is a DIY wireless split keyboard project based on the Corne layout, designed to be built for approximately $25 USD. The keyboard uses ZMK firmware running on ProMicro clones with nRF52840 chips for Bluetooth connectivity.

## Architecture

### Hardware
- **Controller**: ProMicro clone with nRF52840 chip (one for each half)
- **Layout**: 42-key Corne split layout (3x6 columns + 3 thumb keys per half)
- **Connection**: Bluetooth wireless (left half acts as primary controller)
- **Power**: Battery-powered with slide switches for power control

### Firmware
- **Framework**: ZMK (Zephyr-based Mechanical Keyboard firmware)
- **Build System**: West + Zephyr RTOS
- **CI/CD**: GitHub Actions for automated firmware builds

## Directory Structure

```
.
├── 3DFiles/                    # 3D printable case files
│   ├── STEP/                   # CAD source files
│   └── STL/                    # Printable STL files
├── config/                     # Keyboard configuration
│   ├── boards/shields/corne/  # Shield definitions
│   │   ├── corne.dtsi         # Device tree for matrix definition
│   │   ├── corne_left.*       # Left half specific configs
│   │   └── corne_right.*      # Right half specific configs
│   ├── corne.keymap           # Keymap definition
│   └── west.yml               # West manifest
├── firmware/                   # Pre-built firmware files
└── zephyr/                    # Zephyr module configuration
```

## Key Components

### Keymap Configuration (`config/corne.keymap`)
- Defines 3 layers: default, lower (navigation/numbers), raise (symbols)
- Implements combos for commonly used keys (parentheses, brackets, escape, enter)
- Uses ZMK behaviors and keycodes

### Matrix Configuration
- **Rows**: 4 rows per half (GPIO pins 18-21)
- **Columns**: 6 columns per half
  - Left: GPIOs 2,7,6,5,4,3
  - Right: GPIOs 3,4,5,6,7,2
- Configured in device tree overlays (`corne_left.overlay`, `corne_right.overlay`)

### Build Configuration
- **CI/CD**: `.github/workflows/build.yml` - Automated firmware builds on push
- **Module**: `zephyr/module.yml` - Defines this as a Zephyr module
- **Build targets**: `build.yaml` - Specifies build for nice_nano_v2 board

## Development Workflow

### Modifying Keymap
1. Edit `config/corne.keymap` to change key bindings, layers, or combos
2. Push changes to trigger GitHub Actions build
3. Download firmware artifacts from GitHub

### Flashing Firmware
1. Double-press reset button on controller
2. Controller appears as USB mass storage device
3. Copy appropriate `.uf2` file to the device
4. Device auto-reboots with new firmware

### Pin Remapping
If physical wiring doesn't match expected pins, update:
- `config/boards/shields/corne/corne.dtsi` - Row definitions
- `config/boards/shields/corne/corne_left.overlay` - Left column pins
- `config/boards/shields/corne/corne_right.overlay` - Right column pins

## Important Notes

### Battery Management
- Batteries connect: GND → GND, Positive → Slide switch → RAW pin
- Monitor battery health to prevent case warping from swelling

### Pairing Issues
If unable to reconnect after forgetting device:
1. Flash `settings_reset-nice_nano_v2-zmk.uf2`
2. Re-flash normal firmware
3. Re-pair with host device

### ZMK Configuration
- Power management settings in `corne.conf`
- Per-side configurations in `corne_left.conf` and `corne_right.conf`
- Shield definitions in `Kconfig.shield` and `Kconfig.defconfig`

## Build Requirements
- ZMK toolchain (West, Zephyr SDK)
- Basic soldering equipment
- 3D printer for case parts
- Components as listed in BOM (README.md)

## Customization Points
1. **Keymap**: Primary customization via `corne.keymap`
2. **Case design**: Modify STEP files in `3DFiles/STEP/`
3. **Matrix layout**: Adjust pin mappings in overlay files
4. **Power settings**: Tune in `corne.conf` for battery life vs responsiveness