# 7010_AD9363_SDR_Mini

Function description of the device:
1. On board downloader, users can perform bare metal program debugging, firmware burning, etc. through the JTAG port.
2. The board supports FLASH boot mode and JTAG boot mode. When powered on, it defaults to FLASH boot mode and enters JTAG boot mode after pressing the BOOT-KEY button.
3. Equipped with DFU button, USB 2.0 interface, 0.5ppm TCXO, 1T1R interface.
Both USBs can provide power to the board.
5. The board comes pre installed with V0.38 version Pluto firmware, which can be used immediately upon receipt.

The main difference from the official ADAM PLUTO is:
1. Remove the ADM1177 power detection chip.
2. Add FT2232 JTAG+serial port 2-in-1 chip.
3. Replace the main control chip model with XC7Z010-CLG400.
