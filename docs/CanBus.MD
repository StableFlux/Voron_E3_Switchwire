# CAN Bus Setup & Troubleshooting (CB1 + Manta M4P + EBBCan SB2209)
For image: CB1_Debian12_Klipper_kernel6.6_20241219

This guide documents the exact steps required to bring up the CAN bus interface (can0) on the BIGTREETECH CB1 when used with a Manta M4P and an EBBCan SB2209 toolhead board. It is designed to ensure a reliable, reproducible setup every time the OS is reflashed.

---

## Overview

On this OS image, CAN bus support is provided through an MCP2515 SPI‑to‑CAN controller. For can0 to appear and function correctly, two components must be configured:

1. Enable the MCP2515 hardware overlay
2. Configure the CAN0 network interface

If either step is missing, the CAN interface will not come up.

---

## 1. Enable CAN Hardware (MCP2515 Overlay)

Edit the bootloader configuration:

sudo nano /boot/armbianEnv.txt

Ensure these lines are present and not commented:
```
overlays=mcp2515
param_mcp2515_speed=500000
```

Save and reboot:
```
sudo reboot
```
This loads the MCP2515 driver and exposes the CAN controller to the kernel.

---

## 2. Configure the CAN0 Network Interface

Create or edit:
```
sudo nano /etc/network/interfaces.d/can0
```

Use this configuration:
```
auto can0
iface can0 can static
    bitrate 500000
    up ifconfig $IFACE txqueuelen 1024
```

This ensures the CAN interface is brought up automatically at boot.

---

## 3. Verification After Reboot

Check that the interface exists:

```
ip link show can0
```

You should see a can0 entry.

Query the CAN bus:

```
canbus_query.py can0
```

You should receive the UUID of your EBBCan SB2209.

---

## 4. Manual Bring‑Up (Optional)

Useful for testing without rebooting:

```
sudo ip link set can0 up type can bitrate 500000
```

---

## 5. Common Symptoms & Fixes

Symptom | Cause | Fix
------- | ------ | ------
can0 does not appear | MCP2515 overlay not enabled | Add overlays=mcp2515 to /boot/armbianEnv.txt
can0 appears but won’t communicate | Wrong bitrate | Ensure both sides use 500000
canbus_query.py returns nothing | EBBCan not powered or wiring issue | Check 24V, CANH/CANL, GND
dmesg shows MCP2515 errors | SPI bus not initialized | Ensure overlay is active and reboot

---

## 6. Notes for Future You

- This image does not expose a native RK3566 CAN controller. CAN is provided only through the MCP2515 overlay.
- If you reflash the OS, you must re‑enable the overlay and recreate the can0 interface file.
- The overlay configuration is the most common reason CAN disappears after a reinstall.

---

## 7. Related Files in This Repository

- mcu_configs/ebb_sb2209.cfg — Klipper config for the toolhead board
- printer.cfg — Main Klipper configuration
- docs/ — Additional documentation