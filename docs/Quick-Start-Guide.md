---
title: 7010_AD9363_SDR_Mini Quick Start Guide
product: 7010_AD9363_SDR_Mini
doc_id: OSSDRLAB-7010_AD9363_SDR_Mini-QSG
version: 1.0
date: 2026-11-12
repo: https://github.com/OpenSourceSDRLab/7010_AD9363_SDR_Mini
---

<!-- ossl:header -->
<p><a href="https://www.opensourcesdrlab.com"><img src="../images/OpenSourceSDRLab-banner.jpg" alt="OpenSourceSDRLab" width="100%"></a></p>

# 7010_AD9363_SDR_Mini Quick Start Guide

| Product | Document | Version | Date |
|---|---|---|---|
| 7010_AD9363_SDR_Mini | OSSDRLAB-7010_AD9363_SDR_Mini-QSG | 1.0 | 2026-11-12 |
<!-- /ossl:header -->

## 1. Overview

The 7010 AD9363 SDR Mini is a PlutoSDR-compatible software-defined radio: a Xilinx Zynq-7010 SoC with 512 MB DDR3 and 32 MB Quad-SPI flash, and the Analog Devices AD9363 RF transceiver with one transmit and one receive channel. It comes with the Analog Devices PlutoSDR firmware v0.38 pre-installed, so it works with the same software as an ADALM-PLUTO.

Compared with the ADALM-PLUTO, the board has no ADM1177 power monitor, uses the XC7Z010-CLG400 Zynq, and adds an FT2232H bridge that gives JTAG and a serial console on a second USB port. The board can boot from the Quad-SPI flash (default) or over JTAG.

This guide covers the first test, the firmware update over USB (DFU) and the recovery of a board whose flash no longer boots.

## 2. What you need

| Item | Notes |
|---|---|
| 7010 AD9363 SDR Mini | |
| USB-C cable | a data cable, not a charge-only cable |
| SMA cable and attenuator | optional, for a TX → RX loop-back test (use 20 dB or more) |
| PC | Windows, Linux or macOS |
| PlutoSDR USB drivers (Windows) | Analog Devices: <https://wiki.analog.com/university/tools/pluto/drivers/windows>; the package also installs `dfu-util` |
| IIO Oscilloscope | Analog Devices: <https://wiki.analog.com/resources/tools-software/linux-software/iio_oscilloscope> |
| Firmware | `7010_AD9363_SDR_Mini_firmware_v0.38.zip` from the [Releases](../../../releases) page (only for update or recovery) |
| Vivado 2021.1 or later, Lab Edition | AMD/Xilinx: <https://www.xilinx.com/support/download.html> (only for flash recovery) |

Other PlutoSDR programs (SDR# or SDR++ with a PlutoSDR plug-in, GNU Radio, MATLAB) work as with an ADALM-PLUTO.

## 3. Board connectors and controls

| Item | Function |
|---|---|
| TX1A (SMA) | transmit output |
| RX1A (SMA) | receive input |
| OTG (USB-C) | USB 2.0 device port of the Zynq: PlutoSDR network adapter, mass-storage drive and serial console; powers the board |
| JTAG (USB-C) | FT2232H bridge: JTAG for Vivado / Vitis and a serial console; powers the board |
| BOOT_KEY | at power-up the Zynq boots from the Quad-SPI flash; press BOOT_KEY to switch to JTAG boot |
| DFU | hold while powering up to start the firmware-update (DFU) mode |
| LEDs | power, DONE (FPGA configured) and USER (blinks when the firmware is running) |

See `hardware/7010_AD9363_SDR_Mini_Schematic.pdf` for the full schematic.

## 4. First test

1. On Windows, install the PlutoSDR USB drivers.

2. Optionally connect TX1A to RX1A with an SMA cable and an attenuator of at least 20 dB.

3. Connect the **OTG** port to the PC. After about 30 seconds the USER LED blinks and the PC finds a PlutoSDR device: a network adapter (the board is at `192.168.2.1`) and a mass-storage drive.

4. Start IIO Oscilloscope. It finds the device (or enter `ip:192.168.2.1`); click **Connect**.

5. On the **FMComms2/3/4/5 Main** tab, set the TX1 LO and RX LO frequencies, enable the DDS tone for TX1, then open **Plot** → **Capture** to see the received waveform.

## 5. Updating the firmware (DFU)

1. Unzip `7010_AD9363_SDR_Mini_firmware_v0.38.zip`. It contains `pluto.dfu`, `uboot-env.dfu`, `boot.bin` and `fsbl.elf`.

2. Start the board in DFU mode: hold **DFU** while connecting the **OTG** port. Windows shows a **USB download gadget** in the Device Manager.

3. In a command window, go to the folder with the files and run `dfu-util` (on Windows it is installed with the PlutoSDR drivers, in the driver installation folder):

```
dfu-util -d 0456:b673,0456:b674 -D pluto.dfu -a firmware.dfu
dfu-util -d 0456:b673,0456:b674 -D uboot-env.dfu -a uboot-env.dfu
```

   The folder path must not contain non-ASCII characters.

4. When both commands have finished, disconnect and reconnect the board.

> **Tip:** The DFU method works even when the running firmware is damaged, as long as the boot loader in the flash starts. If it does not, see section 6.

## 6. Recovering a board that does not boot

If the board no longer starts (no USER LED blinking, no DFU device), write the boot loader to the flash again over JTAG, then reload the firmware.

1. Connect the **JTAG** port to the PC. If the DONE LED is on, the board has started from the flash: press **BOOT_KEY** (on the side) once to switch to JTAG boot.

2. In Vivado, open **Hardware Manager**, connect to the target, right-click the `xc7z010` device and choose **Add Configuration Memory Device**. Select `mt25ql256-qspi-x4-single`.

3. Program the configuration memory with:
   - Configuration file: `boot.bin`
   - Zynq FSBL: `fsbl.elf`
   - Operations: Erase, Program, Verify

   Wait for "Flash programming completed successfully".

4. Power the board again from the **OTG** port and wait until the PC shows a **USB download gadget**.

5. Load the firmware as in section 5, step 3, then power-cycle the board.

## 7. Support

- Email: support@opensourcesdrlab.com
- Forum: <https://forum.opensourcesdrlab.com>
- Discord: <https://discord.gg/nnxY6DkmvT>
- Blog and user manuals: <https://blog.opensourcesdrlab.com>

<!-- ossl:footer -->
---
<sub>© 2026 OpenSourceSDRLab. All rights reserved. · <a href="https://www.opensourcesdrlab.com">www.opensourcesdrlab.com</a></sub>
<!-- /ossl:footer -->
