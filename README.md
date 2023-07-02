# time-machine

## About

This is an implementation of the OCP TimeCard for personal use based on the AMD Artix 7 XC7A100T FPGA, Symmetricom X72 rubidium atomic clock, and an RCB-F9T GNSS module. The card will either be half-height PCI Express (PCIe) x1 or full-height PCI utilizing PI7C9X111SL as a reverse PCI-PCIe bridge. The card is meant to interface with a PCI 33MHz 5V slot directly or via a StarTech PCI1PEX1 adapter card. The X72 oscillator may be chassis-mounted or board-mounted if feasible.

The use case of this card is to make use of the single PCI slot in the Supermicro X13SAE workstation motherboard for hosting a virtualized NTP and PTP server as well as serve as a frequency standard for hobbyist test equipment and amateur radio applications. Accurate timestamping of the IQ stream from one or more software-defined radios (SDRs) is one forseen application.

This project is also meant to help the auther develop her electronics development skills.

## Goals

The outcomes of this project shall include:
1. Working PCI card compatible with the Linux TimeCard driver
2. Extended FPGA design which can configure the atomic clock at startup/reset and control its frequency
3. Adequate cooling for the atomic clock in order to maintain frequency lock

## Notes

### Circuit

StarTech PCI1PEX1 already implements a reverse PCI-PCIe bridge utilizing the PI7C9X111SL. A half-height PCIe card can mount to this adapter and would enable using PCIe in other systems instead of requiring the obsolete PCI bus.

If a half-height PCIe card is implemented, the atomic clock may need to be mounted to an optional heatsink/bracket attached to the backside of the card to 'overhang' the X72 in front of the StarTech PCI1PEX1 adapter. In other systems, the X72 may instead be chassis-mounted. A cabled connection between the atomic clock and the TimeCard is then required.

Symmetricom X72 firmware 5.04 is required for the necessary 1 PPS input. Analog frequency control of the X72 may require configuration over UART at reset depending on factory settings. Changed settings are reverted on power-off.

### FPGA

Startup/reset configuration of the X72 may be implemented with the FPGA's configuration AXI bus controller. This IP core is meant to take control of the AXI bus on startup/reset and write configuration data to the registers of the AXI agents. The controller can be programmed with a script and be instructed to read data, write data, or wait. Writing data through a UART agent to the X72's serial interface appears feasible.

### Driver

For debugging or user-space configuration the atomic clock, `/dev/ttyS6` is exposed by the TimeCard driver for the clock's serial port.

The X72 has the following serial port specification:
* Logic level: 5V
* Baud rate: 57.6k
* 8 data bits
* 1 stop bit
* No local echo
* No flow control
