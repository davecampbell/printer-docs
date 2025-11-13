# Equipment Documentation Summaries

---

## PDF Documentation Summary

### 1. E000168727-updated.pdf (Proposal/Quote)
**Summary**: Equipment purchase quote for Predator Tamp Print & Apply System
- **Model**: Predator Tamp Print & Apply, Right-Hand configuration
- **Print Engine**: ZE521-6 (6-inch width, 203 DPI)
- **Label Size**: 6.00" x 4.00" (landscape orientation)
- **Key Features**: Electric tamp (48" stroke), Product trigger assembly, Tower lamp, Mounting stand
- **Electrical**: 120 VAC @ 5.0 amps
- **Protocols**: Built-in Ethernet I/P or Modbus TCP

### 2. Panther-Predator-Brochure.pdf (Marketing Material)
**Summary**: Product brochure highlighting Predator features
- **Key Feature**: 100% electric system (no compressed air required)
- **Speed**: Up to 200 IPS (inches per second) high-speed applicator
- **Control**: 52 I/O point PLC with expandable slices
- **Communication**: Ethernet I/P or Modbus TCP protocols built-in
- **HMI**: Remote-mounted 7" touch screen display
- **Print Engines**: OEM capability with SATO or Zebra printers
- **Accuracy**: Within 0.125" with consistent product
- **Applicator Configurations**: Tamp, Swing Arm, NextStep, Custom options

### 3. PM_Panther_Predator_Phantom_EtherNetIP_052323.pdf (Technical Manual)
**Summary**: EtherNet/IP connection setup and I/O mapping guide
- **Protocol**: EtherNet/IP (alternative to Modbus TCP)
- **Configuration**: Uses Rockwell/Allen-Bradley PLC integration
- **I/O Mapping**: 32 bytes input, 32 bytes output (16 words each)
- **Key Inputs** (from Panther to Host):
  - Print Engine Status (Power, Error, Ribbon Low, Label Out)
  - Applicator Status (Home, Cycle Complete)
  - Cycle Times (Applicator, Print)
  - Scanner Status (Good Read, Bad Read)
  - Error Codes
  - LOTAR (Label On Tamp And Ready)
- **Key Outputs** (from Host to Panther):
  - Trigger 1, Trigger 2
  - Reset, Bypass
  - Product Height Submit
- **Network Requirement**: Managed switch with IGMP snooping for multicast traffic
- **Connection Type**: Unicast with 20ms RPI (Requested Packet Interval)

### 4. Panther_Predator_Troubleshooting.pdf (Service Manual)
**Summary**: Comprehensive troubleshooting guide for common errors
- **Error Categories**: Printer errors, applicator errors, communication errors, electrical issues
- **Key Insights**:
  - Print engine communicates with Panther via discrete signals through applicator interface cable
  - Signals include: RIBBON LOW, LABEL OUT, PRINTER ERROR, +VOLTAGE (power status)
  - LOTAR sensor (Label On Tamp And Ready) is optional hardware
  - Servo motor control for electric applicator movement
  - Tower lamp for visual status indication
- **Common Issues**: Label tracking, applicator homing, print engine communication
- **Diagnostic Tools**: Built-in I/O status screens on HMI

### 5. ze511-ze521-spec-sheet-en-us.pdf (Print Engine Specifications)
**Summary**: Zebra ZE521 print engine technical specifications
- **Model**: ZE521 (6-inch print width)
- **Resolution Options**: 203 dpi (standard), 300 dpi, 600 dpi
- **Print Speed**: Up to 14 ips @ 203 dpi, 12 ips @ 300 dpi
- **Print Method**: Thermal transfer or direct thermal
- **Memory**: 512MB SDRAM, 512MB Flash
- **Communication**: USB 2.0, RS-232 Serial, 10/100 Ethernet, Bluetooth 4.1, Dual USB Host
- **Optional Comm**: Parallel, 802.11 a/c WiFi, Additional Ethernet
- **Programming Languages**: ZPL, ZPL II (Zebra Programming Language)
- **Print Touch**: NFC tag for mobile device pairing/printing
- **Label Formats**: Continuous, die-cut, black mark
- **Media Width**: 3" to 7.1"
- **Barcodes**: All standard 1D and 2D symbologies (Code 128, QR, PDF417, DataMatrix, etc.)
- **Network Ports**:
  - Port 9100: Raw ZPL data (standard for Zebra printers)
  - Ethernet/IP or Modbus TCP via applicator interface

---
