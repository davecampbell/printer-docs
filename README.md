# UPS Label Printing System - Documentation

This repository contains the complete documentation for the UPS Panther Predator Tamp Print & Apply System integration project.

## Project Overview

This project implements a complete automated label printing and application system for warehouse operations. The system receives tracking codes from a barcode scanner, fetches label data from an IT service, generates ZPL print commands for a Zebra thermal printer, and controls a robotic applicator to stamp labels on boxes.

### System Components

- **Zebra ZE521-6 Print Engine** - 6-inch thermal transfer printer (203 DPI)
- **Panther Predator PLC** - Electric tamp print & apply system with 52 I/O points
- **Python Controller** - Orchestration service coordinating the entire workflow
- **IT-01 Service** - Backend API providing label rendering data
- **UL-01 Scanner** - Vision system for barcode reading

### Communication Protocols

- **ZPL** - Zebra Programming Language for print commands (TCP port 9100)
- **Modbus TCP** - Industrial control protocol for PLC communication (port 502)
- **HTTP/REST** - API communication for label data requests

---

## Documentation Files

### 1. **background.md**
**Purpose**: Original project requirements and workflow overview

This document captures the initial project scope and goals. It describes the complete pipeline from box identification through barcode scanning, label data retrieval from IT-01, ZPL generation, and final label application via the Panther system.

**Key Contents**:
- Complete workflow description (vision system → barcode read → IT-01 lookup → print → apply)
- Known communication protocols (Modbus for printer control, HTTP for IT-01)
- Initial requirements and constraints
- Project goals: dockerized simulation environment for development

**Use this document to**: Understand the original business requirements and project motivation.

---

### 2. **ideas.md**
**Purpose**: Technical architecture and implementation planning

This is the most comprehensive technical document, containing detailed implementation proposals, architecture diagrams, and technology stack decisions. It includes complete code examples for all major components.

**Key Contents**:
- Hardware specifications from equipment PDFs (Predator system, Zebra printer)
- Proposed Python architecture with FastAPI orchestration
- Complete code examples for:
  - HTTP server for barcode events
  - IT-01 client implementation
  - Zebra print client (TCP port 9100)
  - Panther Modbus client with full I/O mapping
- ZPL generation strategies
- Docker containerization approach
- Testing strategy with mock services
- Technology stack comparison and rationale
- Development phases (Mock → Integration → Production)
- Complete I/O register mapping from EtherNet/IP manual
- Resolved questions and remaining unknowns

**Use this document to**: Understand the complete technical architecture and implementation approach. Contains the most detailed technical specifications.

---

### 3. **system_diagram.md**
**Purpose**: Visual architecture and component relationships

This document provides a visual representation of the system using Mermaid diagrams, showing how all components connect and communicate.

**Key Contents**:
- Mermaid diagram showing complete data flow
- Component descriptions (color-coded by function)
- Communication protocol table with ports and data formats
- Network configuration example
- Data flow summary (6-step workflow)
- Key design principles (separation of concerns, standard protocols, async operation)

**Use this document to**: Quickly visualize system architecture and understand component interactions. Excellent for presentations and onboarding.

---

### 4. **implementation-01.md**
**Purpose**: Docker implementation guide and development progress

This document tracks the actual implementation of the dockerized simulator environment. It contains the step-by-step build process, verified functionality, and current system capabilities.

**Key Contents**:
- Complete project structure
- Docker service definitions (controller, mock-it01, mock-zebra, mock-panther, dashboard, MQTT)
- Environment variable configuration
- Build and deployment instructions
- Test results and verification steps
- Dashboard features (industrial SCADA-style web interface)
- Real-time event log and monitoring
- Quick start guide for running the simulator
- Current system capabilities and status

**Use this document to**: Learn how to run the simulator, understand what's been built, and see test results. This is the "living document" that tracks actual implementation progress.

---

### 5. **EQUIPT-DOCS/doc-summaries.md**
**Purpose**: Equipment specification summaries

Centralized summaries of all vendor-provided PDF documentation for the Panther Predator system and Zebra print engine.

**Key Contents**:
- Equipment quote and pricing (E000168727-updated.pdf)
- Marketing brochure with features (Panther-Predator-Brochure.pdf)
- EtherNet/IP technical manual with I/O mapping (PM_Panther_Predator_Phantom_EtherNetIP_052323.pdf)
- Troubleshooting guide (Panther_Predator_Troubleshooting.pdf)
- Zebra print engine specifications (ze511-ze521-spec-sheet-en-us.pdf)

**Use this document to**: Quickly reference hardware specifications without reading full PDF manuals. Essential for configuration and troubleshooting.

---

## Related Code Repository

The actual simulator implementation code is maintained in a separate repository:

**Code Location**: `~/projects/ups-label-system/`

This contains:
- All Docker service implementations
- Python source code for controller and mock services
- Web dashboard (FastAPI + HTMX + Alpine.js)
- MQTT event broker configuration
- Complete docker-compose.yml orchestration
- README with operational instructions

See `implementation-01.md` for details on building and running the simulator.

---

## Quick Reference

### For New Team Members
1. Start with **background.md** - Understand the business need
2. Review **system_diagram.md** - Visualize the architecture
3. Read **ideas.md** sections 1-3 - Learn the technical approach
4. Check **EQUIPT-DOCS/doc-summaries.md** - Know the hardware specs

### For Developers
1. Read **ideas.md** - Complete technical specification
2. Review **implementation-01.md** - See what's been built
3. Clone the code repo at `~/projects/ups-label-system/`
4. Run `docker-compose up` to start the simulator

### For Hardware Integration
1. Review **EQUIPT-DOCS/doc-summaries.md** - Hardware specs
2. Reference **ideas.md** section on "Real Hardware Integration"
3. Check Modbus register map in **ideas.md**
4. Review ZPL format examples and state machine diagrams

### For Troubleshooting
1. Check **implementation-01.md** - Known issues and solutions
2. Review **EQUIPT-DOCS/doc-summaries.md** - Troubleshooting PDF summary
3. Examine **ideas.md** - Error handling strategies

---

## Equipment Documentation Summaries

The following summaries provide quick reference to the vendor-provided PDF specifications.

### 1. E000168727-updated.pdf (Proposal/Quote)
**Summary**: Equipment purchase quote for Predator Tamp Print & Apply System
- **Model**: Predator Tamp Print & Apply, Right-Hand configuration
- **Print Engine**: ZE521-6 (6-inch width, 203 DPI)
- **Label Size**: 6.00" x 4.00" (landscape orientation)
- **Price**: $31,631.61 total system
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

## Project Status

**Current Phase**: Development simulator complete with web dashboard and real-time monitoring

**Next Steps**:
1. Hardware arrives and network configuration
2. Real IT-01 API integration (pending endpoint documentation)
3. Physical Zebra print engine connection and ZPL testing
4. Panther PLC Modbus integration and I/O validation
5. End-to-end workflow testing with real hardware
6. Production deployment and operator training

---

## Contact & Resources

**Documentation Location**: `~/Documents/ObsidianVault/PROJECTS/UPS-PRINTER/`
**Code Repository**: `~/projects/ups-label-system/`
**Equipment PDFs**: `./EQUIPT-DOCS/` folder

**Last Updated**: 2025-11-13

---

## License

Internal development project - not for public distribution.

© 2025 - UPS Label Printing System Documentation
