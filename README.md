# Voron_E3_Switchwire

## 📌 Important Documentation  
For CAN bus setup, troubleshooting, and configuration details, see:  
👉 **[CAN Bus Setup & Troubleshooting](docs/CanBus.md)**

# Klipper Configuration for BIGTREETECH Manta M4P + EBBCan SB2209

This repository contains a set of Klipper configuration files for a 3D printer using the BIGTREETECH Manta M4P mainboard and EBBCan SB2209 toolhead board. The setup is designed for a CoreXZ kinematics printer with sensorless homing, inductive probe, and advanced features such as bed mesh leveling, input shaping, and display integration.

---

## Features

- **CoreXZ Kinematics**: Optimized for fast and precise motion.
- **Sensorless Homing**: Utilizes TMC2209 drivers for stall-detection-based homing on X and Y axes.
- **Inductive Probe**: For accurate Z homing and bed mesh generation.
- **Bed Mesh Leveling**: 3x3 grid with bicubic interpolation.
- **Input Shaping**: For vibration reduction and improved print quality.
- **Mini12864 Display**: With encoder and Neopixel RGB support.
- **Advanced Macros**: Including safe homing, conditional homing, and display status integration.
- **CAN Toolhead**: EBBCan SB2209 for extruder, probe, and fans.
- **Multiple Fan and Heater Controls**: For hotend, bed, and chamber.

---

## File Structure

- `printer.cfg`  
  Main configuration file. Includes board pin mappings, stepper and driver settings, heater/fan/display configuration, and includes other config files.

- `feature_configs/sensorless_homing.cfg`  
  Macros and overrides for sensorless homing, including custom X/Y homing routines and a homing override macro for safe and reliable operation.

- `mcu_configs/ebb_sb2209.cfg`  
  Configuration for the EBBCan SB2209 toolhead board, including extruder, probe, fans, and Neopixel LEDs.

- `macros/macros_include.cfg`  
  (Not shown here) Additional user macros.

---

## Sensorless Homing

Sensorless homing is implemented using TMC2209 drivers' StallGuard feature. The macros temporarily lower motor current for reliable stall detection, move the axis to a safe position, and restore normal current after homing.

**Key settings:**
- `endstop_pin: tmc2209_stepper_x:virtual_endstop`
- `position_endstop: 0` (homes to minimum)
- `driver_SGTHRS`: StallGuard threshold for each axis

See `feature_configs/sensorless_homing.cfg` for macro details.

---

## Z-Probe and Bed Mesh

- Inductive probe connected to EBBCan SB2209.
- Bed mesh defined with a 3x3 grid and bicubic interpolation.
- Z homing and mesh probing are performed at the center of the bed for accuracy.

---

## Display and LEDs

- Mini12864 LCD with encoder and Neopixel RGB support.
- Macros for display status and LED color control.

---

## Getting Started

1. **Flash Klipper** to your Manta M4P and EBBCan SB2209 with the correct MCU and communication settings.
2. **Update serial/canbus UUIDs** in `[mcu]` and `[mcu EBBCan]` sections as needed.
3. **Adjust probe offsets** and bed mesh area to match your printer's geometry.
4. **Tune StallGuard thresholds** (`driver_SGTHRS`) for reliable sensorless homing.
5. **Include or modify macros** in `macros/macros_include.cfg` as needed.

---

## Notes

- The `[force_move]` section is enabled for kinematic position adjustments during homing.
- All stepper and driver settings are tuned for the listed motors; adjust currents if you use different hardware.
- The configuration is modular—edit or extend included files as your setup evolves.

---

## References

- [Klipper Documentation](https://www.klipper3d.org/)
- [BIGTREETECH Manta M4P](https://github.com/bigtreetech/Manta-M4P)
- [BIGTREETECH EBBCan SB2209](https://github.com/bigtreetech/EBB)

---

## License

This configuration is provided as-is for personal and educational use. Please review and test thoroughly before use on your hardware.
