# Python Implementation Ideas for UPS Label Printing System

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

## System Overview
Based on the background and PDF specifications, this system needs to:
1. Receive tracking codes (13 chars, starting with "1Z") from a barcode scanner system (UL-01)
2. Request label data from IT-01 system via HTTP
3. Transform the JSON response into ZPL (Zebra Programming Language) format
4. Send ZPL data to the Zebra print engine via TCP (port 9100)
5. Send control signals to the Panther Predator via Modbus TCP or EtherNet/IP

## Hardware Specifications (from PDF)
- **System**: Panther Predator Tamp Print & Apply System
- **Print Engine**: Zebra ZE521-6 (6-inch width, 203 DPI)
- **Label Size**: 6.00" x 4.00" (landscape orientation)
- **Control Communication**: Modbus TCP OR EtherNet/IP (choose one)
- **Print Communication**: ZPL via Ethernet (port 9100), Serial, or USB
- **Power**: 120 VAC @ 5.0 amps
- **Network**: 52 I/O point PLC (Panther controller), Ethernet capable
- **HMI**: 7" color touchscreen (can be remote mounted up to 2m)

---

## Proposed Architecture

### 1. **Main Controller Service** (Python)
**Purpose**: Orchestrate the entire workflow

**Key Components**:
- HTTP server to receive barcode events from UL-01
- HTTP client to communicate with IT-01
- ZPL client to send label data to Zebra print engine (port 9100)
- Modbus TCP or EtherNet/IP client to communicate with Panther PLC
- Queue management for handling multiple labels
- Error handling and logging

**Python Libraries**:
```python
# Web framework
- FastAPI or Flask (for HTTP server)
- httpx or requests (for HTTP client to IT-01)

# Printer communication
- socket (standard library for ZPL to port 9100)
- pymodbus (Modbus TCP client for Panther control)
# OR
- pycomm3 (for EtherNet/IP to Panther - if using Allen-Bradley protocol)

# ZPL generation
- zebra-zpl (for ZPL label generation)
- pillow (for image handling if needed)

# Data handling
- pydantic (for data validation)
- json (standard library)

# Async/Queue management
- asyncio (for async operations)
- redis or RabbitMQ (for job queuing - optional)

# Logging/monitoring
- logging (standard library)
- structlog (structured logging)
```

**File structure**:
```
ups_label_system/
├── main.py                      # Entry point
├── config.py                   # Configuration management
├── models/
│   ├── barcode.py             # Barcode data models
│   └── label.py               # Label data models
├── services/
│   ├── barcode_receiver.py    # HTTP endpoint for barcodes
│   ├── it01_client.py         # IT-01 communication
│   ├── zebra_client.py        # ZPL print engine client (port 9100)
│   ├── panther_client.py      # Modbus/EtherNet-IP Panther PLC client
│   └── zpl_generator.py       # JSON to ZPL transformation
├── utils/
│   ├── logger.py
│   └── validators.py
└── tests/
    ├── test_it01.py
    ├── test_zpl_generation.py
    └── mocks/
        ├── mock_it01.py
        └── mock_panther.py
```

---

### 2. **Barcode Receiver Service** (HTTP Server)

**Endpoint Design**:
```python
POST /api/v1/barcode
{
  "tracking_code": "1Z999AA10123456784",
  "timestamp": "2025-11-06T10:30:00Z",
  "box_id": "BOX-12345"  # optional metadata
}
```

**Implementation Approach**:
- FastAPI for async HTTP server
- Validate tracking code (13 chars, starts with "1Z")
- Add to processing queue
- Return immediate acknowledgment
- Process asynchronously

**Code Sketch**:
```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel, validator

class BarcodeRequest(BaseModel):
    tracking_code: str
    timestamp: str
    box_id: str | None = None

    @validator('tracking_code')
    def validate_tracking_code(cls, v):
        if len(v) != 13 or not v.startswith('1Z'):
            raise ValueError('Invalid tracking code format')
        return v

app = FastAPI()

@app.post("/api/v1/barcode")
async def receive_barcode(request: BarcodeRequest):
    # Add to queue for processing
    await label_queue.put(request)
    return {"status": "queued", "tracking_code": request.tracking_code}
```

---

### 3. **IT-01 Client Service**

**Purpose**: Communicate with IT-01 to fetch label data

**HTTP Request Design**:
```python
GET/POST https://it01.example.com/api/label?code=1Z999AA10123456784
# OR
POST https://it01.example.com/api/label
{
  "code": "1Z999AA10123456784"
}
```

**Expected Response**: Nested JSON with label information
```json
{
  "tracking_number": "1Z999AA10123456784",
  "label_data": {
    "recipient": {
      "name": "John Doe",
      "address": "123 Main St",
      "city": "Louisville",
      "state": "KY",
      "zip": "40292"
    },
    "barcode_data": "...",
    "service_level": "Ground",
    "weight": "5.2 lbs",
    "dimensions": "12x10x8"
  },
  "rendering": {
    "format": "ZPL",  # or ZPL, PCL, etc.
    "data": "^XA^FO50,50^A0N,50,50^FDShipping Label^FS..."
  }
}
```

**Implementation**:
```python
import httpx
from typing import Dict

class IT01Client:
    def __init__(self, base_url: str, timeout: int = 10):
        self.base_url = base_url
        self.client = httpx.AsyncClient(timeout=timeout)

    async def get_label_data(self, tracking_code: str) -> Dict:
        response = await self.client.post(
            f"{self.base_url}/api/label",
            json={"code": tracking_code}
        )
        response.raise_for_status()
        return response.json()

    async def close(self):
        await self.client.aclose()
```

---

### 4. **Label Transformer Service**

**Purpose**: Convert IT-01 JSON response to printer-ready format

**Challenges**:
- Need to understand what format the Predator printer expects
- Zebra printers typically use ZPL (Zebra Programming Language)
- May need to generate ZPL from JSON data if not provided

**Potential Formats**:
1. **ZPL** (Zebra Programming Language) - most likely
2. **EPL** (Eltron Programming Language)
3. **Raw label data with Modbus registers**

**Implementation Options**:

**Option A: IT-01 provides ZPL directly**
```python
def transform_label(it01_response: Dict) -> str:
    # If IT-01 already provides ZPL
    if 'rendering' in it01_response and it01_response['rendering']['format'] == 'ZPL':
        return it01_response['rendering']['data']

    # Otherwise, generate ZPL from data
    return generate_zpl(it01_response['label_data'])
```

**Option B: Generate ZPL from JSON**
```python
def generate_zpl(label_data: Dict) -> str:
    zpl = "^XA"  # Start format

    # Add recipient info
    zpl += f"^FO50,50^A0N,30,30^FD{label_data['recipient']['name']}^FS"
    zpl += f"^FO50,100^A0N,25,25^FD{label_data['recipient']['address']}^FS"

    # Add barcode
    zpl += f"^FO50,200^BY3^BCN,100,Y,N^FD{label_data['tracking_number']}^FS"

    zpl += "^XZ"  # End format
    return zpl
```

**Libraries**:
- `zebra-zpl` (for ZPL generation)
- `pillow` (if need to convert images to ZPL)
- Custom template engine

---

### 5A. **Zebra Print Engine Client** (ZPL over TCP)

**Purpose**: Send ZPL label data to the Zebra ZE521 print engine

**Communication Method**: Direct TCP socket to port 9100 (standard for Zebra printers)

**Workflow**:
1. Generate ZPL from IT-01 data
2. Open TCP connection to print engine (port 9100)
3. Send ZPL data
4. Close connection
5. Print engine automatically prints the label

**Implementation**:
```python
import socket
from typing import Optional

class ZebraClient:
    def __init__(self, host: str, port: int = 9100, timeout: int = 10):
        self.host = host
        self.port = port
        self.timeout = timeout

    def print_label(self, zpl_data: str) -> bool:
        """Send ZPL data to Zebra print engine"""
        try:
            with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
                s.settimeout(self.timeout)
                s.connect((self.host, self.port))
                s.sendall(zpl_data.encode('utf-8'))
                return True
        except socket.timeout:
            raise TimeoutError(f"Connection to Zebra printer {self.host}:{self.port} timed out")
        except ConnectionRefusedError:
            raise ConnectionError(f"Zebra printer {self.host}:{self.port} refused connection")
        except Exception as e:
            raise Exception(f"Failed to print label: {str(e)}")

    def check_status(self) -> dict:
        """Query printer status using ~HS command"""
        status_zpl = "~HS"  # Host Status command
        try:
            with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
                s.settimeout(self.timeout)
                s.connect((self.host, self.port))
                s.sendall(status_zpl.encode('utf-8'))
                response = s.recv(1024).decode('utf-8')
                return self._parse_status(response)
        except Exception as e:
            return {"error": str(e), "online": False}

    def _parse_status(self, response: str) -> dict:
        # Parse Zebra status response
        # Example response includes printer status, errors, etc.
        return {"online": True, "raw_response": response}
```

**Alternative: Use Serial or USB**:
If network connection is unstable, the Zebra print engine also supports:
- Serial connection (RS-232)
- USB connection
- Both accept the same ZPL commands

---

### 5B. **Panther PLC Client** (Modbus TCP or EtherNet/IP)

**Purpose**: Send control signals to Panther Predator applicator system

**Communication Method**: Modbus TCP OR EtherNet/IP (choose based on your PLC infrastructure)

**I/O Mapping** (from EtherNet/IP manual):

**Outputs (Host → Panther)**:
- Byte 0, Bit 0: Trigger 1 (initiate print/apply cycle)
- Byte 0, Bit 1: Trigger 2 (optional second trigger)
- Byte 0, Bit 2: Reset (clear errors)
- Byte 0, Bit 3: Bypass (bypass current cycle)
- Byte 0, Bit 5: Product Height Submit
- Bytes 2-3: Product Height (integer, millimeters)

**Inputs (Panther → Host)**:
- Byte 0, Bit 0: Print Engine Power
- Byte 0, Bit 1: Print Engine Error
- Byte 0, Bit 4: Online/Data Ready
- Byte 0, Bit 7: Panther Fault
- Byte 1, Bit 0: Cycle Complete
- Byte 1, Bit 1: Applicator Home
- Byte 1, Bit 3: LOTAR (Label On Tamp And Ready)
- Bytes 4-5: Applicator Cycle Time (ms)
- Bytes 6-7: Print Cycle Time (ms)
- Bytes 8-9: Current Error Code

**Implementation Option 1: Modbus TCP**:
```python
from pymodbus.client import ModbusTcpClient
from pymodbus.exceptions import ModbusException

class PantherModbusClient:
    def __init__(self, host: str, port: int = 502, unit_id: int = 1):
        self.host = host
        self.port = port
        self.unit_id = unit_id
        self.client = ModbusTcpClient(host, port)

    def connect(self):
        if not self.client.connect():
            raise ConnectionError(f"Failed to connect to Panther at {self.host}:{self.port}")

    def trigger_apply(self):
        """Trigger the print and apply cycle"""
        # Set Trigger 1 bit (Byte 0, Bit 0)
        # This assumes coil addressing - may need to adjust based on actual Panther configuration
        self.client.write_coil(address=0, value=True, slave=self.unit_id)

    def read_status(self) -> dict:
        """Read Panther status"""
        # Read input registers (16 words = 32 bytes)
        result = self.client.read_input_registers(address=0, count=16, slave=self.unit_id)

        if result.isError():
            raise ModbusException("Error reading Panther status")

        # Parse status from registers
        status = {
            "print_engine_power": bool(result.registers[0] & 0x0001),
            "print_engine_error": bool(result.registers[0] & 0x0002),
            "online": bool(result.registers[0] & 0x0010),
            "panther_fault": bool(result.registers[0] & 0x0080),
            "cycle_complete": bool(result.registers[0] & 0x0100),
            "applicator_home": bool(result.registers[0] & 0x0200),
            "lotar": bool(result.registers[0] & 0x0800),
            "applicator_cycle_time": result.registers[2],
            "print_cycle_time": result.registers[3],
            "error_code": result.registers[4]
        }
        return status

    def reset_errors(self):
        """Send reset signal to clear errors"""
        # Set Reset bit (Byte 0, Bit 2)
        self.client.write_coil(address=2, value=True, slave=self.unit_id)

    def close(self):
        self.client.close()
```

**Implementation Option 2: EtherNet/IP**:
```python
from pycomm3 import LogixDriver

class PantherEtherNetIPClient:
    def __init__(self, host: str, slot: int = 0):
        self.host = host
        self.plc = LogixDriver(host, slot=slot)

    def connect(self):
        self.plc.open()

    def trigger_apply(self):
        """Trigger the print and apply cycle"""
        # Write to Panther module output tag
        self.plc.write('Panther_Line1.O.Data[0].0', True)  # Trigger 1

    def read_status(self) -> dict:
        """Read Panther status"""
        # Read input data array
        inputs = self.plc.read('Panther_Line1.I.Data')

        if inputs.error:
            raise Exception(f"Error reading Panther status: {inputs.error}")

        # Parse byte array into status dict
        data = inputs.value
        status = {
            "print_engine_power": bool(data[0] & 0x01),
            "print_engine_error": bool(data[0] & 0x02),
            "cycle_complete": bool(data[1] & 0x01),
            "applicator_home": bool(data[1] & 0x02),
            "lotar": bool(data[1] & 0x08),
            # Additional fields...
        }
        return status

    def close(self):
        self.plc.close()
```

**Key Differences**:
- **Modbus TCP**: Universal, works with any controller, simpler setup
- **EtherNet/IP**: Better for Allen-Bradley/Rockwell PLCs, faster, more industrial features

---

### 6. **Main Orchestration Logic**

**Workflow**:
```python
async def process_label(tracking_code: str):
    logger.info(f"Processing label for {tracking_code}")

    zebra_client = None
    panther_client = None
    it01_client = None

    try:
        # Step 1: Fetch label data from IT-01
        it01_client = IT01Client(config.IT01_URL)
        label_data = await it01_client.get_label_data(tracking_code)

        # Step 2: Transform to ZPL format
        zpl_generator = ZPLGenerator()
        zpl_data = zpl_generator.generate(label_data)

        # Step 3: Check Panther system status
        panther_client = PantherModbusClient(config.PANTHER_HOST)
        panther_client.connect()

        panther_status = panther_client.read_status()
        if not panther_status["online"]:
            raise Exception("Panther system offline")
        if panther_status["panther_fault"]:
            raise Exception(f"Panther fault: Error code {panther_status['error_code']}")
        if not panther_status["applicator_home"]:
            raise Exception("Applicator not in home position")

        # Step 4: Send ZPL to Zebra print engine
        zebra_client = ZebraClient(config.ZEBRA_HOST)
        zebra_client.print_label(zpl_data)

        # Step 5: Wait for label to be ready on tamp (if LOTAR sensor available)
        if config.HAS_LOTAR_SENSOR:
            await wait_for_lotar(panther_client, timeout=10)

        # Step 6: Trigger apply cycle
        panther_client.trigger_apply()

        # Step 7: Wait for cycle completion
        await wait_for_cycle_complete(panther_client, timeout=30)

        logger.info(f"Successfully printed and applied label for {tracking_code}")

    except Exception as e:
        logger.error(f"Error processing {tracking_code}: {str(e)}")
        # Handle retry logic, alert, etc.
        if panther_client:
            panther_client.reset_errors()

    finally:
        if panther_client:
            panther_client.close()
        if it01_client:
            await it01_client.close()


async def wait_for_lotar(panther_client, timeout: int = 10):
    """Wait for Label On Tamp And Ready signal"""
    import asyncio
    start_time = asyncio.get_event_loop().time()

    while True:
        status = panther_client.read_status()
        if status["lotar"]:
            return True

        if asyncio.get_event_loop().time() - start_time > timeout:
            raise TimeoutError("Timeout waiting for LOTAR signal")

        await asyncio.sleep(0.1)


async def wait_for_cycle_complete(panther_client, timeout: int = 30):
    """Wait for applicator cycle to complete"""
    import asyncio
    start_time = asyncio.get_event_loop().time()

    while True:
        status = panther_client.read_status()
        if status["cycle_complete"]:
            return True

        if status["panther_fault"]:
            raise Exception(f"Panther fault during cycle: Error code {status['error_code']}")

        if asyncio.get_event_loop().time() - start_time > timeout:
            raise TimeoutError("Timeout waiting for cycle completion")

        await asyncio.sleep(0.1)
```

**Key Workflow Points**:
1. **Fetch label data** from IT-01 via HTTP
2. **Generate ZPL** from JSON response
3. **Check Panther status** via Modbus/EtherNet-IP
4. **Send ZPL to Zebra** via TCP port 9100
5. **Wait for LOTAR** (optional, if sensor present)
6. **Trigger apply** via Modbus/EtherNet-IP
7. **Monitor completion** via Modbus/EtherNet-IP

---

### 7. **Configuration Management**

```python
# config.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # Service settings
    SERVICE_HOST: str = "0.0.0.0"
    SERVICE_PORT: int = 8000

    # IT-01 settings
    IT01_URL: str = "http://it01.example.com"
    IT01_TIMEOUT: int = 10

    # Zebra Print Engine settings
    ZEBRA_HOST: str = "192.168.1.100"  # IP of Zebra ZE521 print engine
    ZEBRA_PORT: int = 9100             # Standard ZPL port
    ZEBRA_TIMEOUT: int = 10

    # Panther PLC settings
    PANTHER_HOST: str = "192.168.1.101"  # IP of Panther controller
    PANTHER_PROTOCOL: str = "modbus"     # "modbus" or "ethernetip"
    PANTHER_MODBUS_PORT: int = 502
    PANTHER_MODBUS_UNIT_ID: int = 1

    # Hardware options
    HAS_LOTAR_SENSOR: bool = False  # Label On Tamp And Ready sensor

    # Queue settings
    REDIS_URL: str = "redis://localhost:6379"
    MAX_RETRIES: int = 3

    # Timeouts
    LOTAR_TIMEOUT: int = 10        # Seconds to wait for LOTAR
    CYCLE_TIMEOUT: int = 30        # Seconds to wait for apply cycle

    # Logging
    LOG_LEVEL: str = "INFO"

    class Config:
        env_file = ".env"

config = Settings()
```

**Example .env file**:
```bash
# IT-01 Service
IT01_URL=http://192.168.1.50:8080

# Zebra Print Engine
ZEBRA_HOST=192.168.1.100
ZEBRA_PORT=9100

# Panther Controller
PANTHER_HOST=192.168.1.101
PANTHER_PROTOCOL=modbus
PANTHER_MODBUS_PORT=502

# Hardware Options
HAS_LOTAR_SENSOR=true

# Logging
LOG_LEVEL=DEBUG
```

---

### 8. **Docker Containerization**

**Dockerfile**:
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application
COPY . .

# Expose port
EXPOSE 8000

# Run application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**docker-compose.yml**:
```yaml
version: '3.8'

services:
  label-service:
    build: .
    ports:
      - "8000:8000"
    environment:
      - IT01_URL=${IT01_URL}
      - PRINTER_HOST=${PRINTER_HOST}
      - REDIS_URL=redis://redis:6379
    depends_on:
      - redis
    networks:
      - label-network

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    networks:
      - label-network

  # Mock services for testing
  mock-it01:
    build: ./mocks/it01
    ports:
      - "8001:8001"
    networks:
      - label-network

networks:
  label-network:
    driver: bridge
```

---

### 9. **Testing Strategy**

**Mock Services Needed**:

1. **Mock UL-01** - Simulate barcode scanner sending codes
2. **Mock IT-01** - Return sample JSON label data
3. **Mock Printer** - Simulate Modbus responses

**Mock IT-01 Implementation**:
```python
# mocks/it01/server.py
from fastapi import FastAPI

app = FastAPI()

SAMPLE_RESPONSE = {
    "tracking_number": "1Z999AA10123456784",
    "label_data": {
        "recipient": {
            "name": "John Doe",
            "address": "123 Main St",
            "city": "Louisville",
            "state": "KY",
            "zip": "40292"
        }
    },
    "rendering": {
        "format": "ZPL",
        "data": "^XA^FO50,50^A0N,50,50^FDTest Label^FS^XZ"
    }
}

@app.post("/api/label")
async def get_label(request: dict):
    code = request.get("code")
    response = SAMPLE_RESPONSE.copy()
    response["tracking_number"] = code
    return response
```

---

### 10. **Resolved Questions & Remaining Unknowns**

**✅ Resolved from PDFs**:
1. **✅ Communication Architecture**:
   - ZPL data goes to Zebra print engine via TCP port 9100
   - Control signals go to Panther PLC via Modbus TCP or EtherNet/IP
   - Complete I/O mapping documented in EtherNet/IP manual

2. **✅ Label Format**: ZPL (Zebra Programming Language) confirmed
   - Print engine: Zebra ZE521-6, 203 DPI
   - Supports ZPL, ZPL II languages
   - 6" x 4" label size (landscape)

3. **✅ Printer Protocols**:
   - Zebra print engine: TCP port 9100, Serial, USB, Bluetooth
   - Panther control: Modbus TCP (port 502) OR EtherNet/IP
   - I/O structure: 32 bytes input, 32 bytes output

4. **✅ Status Monitoring**:
   - Real-time status available via Panther I/O
   - Error codes provided in status registers
   - LOTAR sensor available (optional hardware)
   - Cycle timing data available

5. **✅ Troubleshooting**:
   - Comprehensive error guide in troubleshooting PDF
   - Built-in diagnostics via 7" HMI touchscreen
   - Common issues and solutions documented

**❓ Remaining Unknowns**:
1. **IT-01 API Specification**:
   - Exact endpoint URL and authentication method
   - Request/response format (does IT-01 provide ZPL or just data?)
   - Error handling and retry behavior
   - Rate limiting or throttling considerations

2. **Network Configuration**:
   - IP addresses for Zebra print engine and Panther PLC
   - Network topology (same subnet? VLANs?)
   - Firewall rules if applicable

3. **UL-01 Integration**:
   - How does UL-01 communicate barcode reads? (HTTP? Socket? File?)
   - Event format and timing
   - Multiple barcode handling

4. **Production Workflow Details**:
   - Product spacing and timing constraints
   - Maximum throughput requirements
   - Queue depth and backpressure handling
   - Failover and redundancy requirements

**Development Phases**:

**Phase 1**: Mock Services & ZPL Testing
- Create mock IT-01 service (returns sample JSON)
- Develop ZPL generation logic
- Test ZPL output on a standalone Zebra printer (if available)
- Create mock Panther client (simulate I/O responses)

**Phase 2**: IT-01 Integration
- Connect to real IT-01 API
- Implement authentication
- Parse and validate JSON responses
- Handle IT-01 errors and timeouts

**Phase 3**: Printer System Integration
- Configure Zebra print engine network settings
- Test ZPL transmission to port 9100
- Implement Panther Modbus/EtherNet-IP client
- Test I/O mapping (triggers, status reads, error handling)
- Validate LOTAR sensor functionality

**Phase 4**: End-to-End Integration
- Integrate all components
- Test complete workflow from barcode scan to label application
- Tune timing parameters (LOTAR timeout, cycle timeout)
- Performance testing and optimization

**Phase 5**: Production Hardening
- Error handling and automatic retry logic
- Monitoring, alerting, and logging
- Graceful degradation (what happens if IT-01 is down?)
- Security considerations (network segmentation, access control)
- Documentation and operator training materials

---

## Technology Stack Summary

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Web Framework | FastAPI | Modern, async, good performance |
| HTTP Client | httpx | Async support, modern API |
| ZPL Communication | socket (stdlib) | Direct TCP to Zebra print engine (port 9100) |
| Panther Control | pymodbus OR pycomm3 | Modbus TCP or EtherNet/IP to Panther PLC |
| ZPL Generation | zebra-zpl OR custom | Generate ZPL from JSON data |
| Data Validation | Pydantic | Type safety, validation |
| Queuing | Redis/asyncio | Handle async workload |
| Containerization | Docker | Portability, easy deployment |
| Logging | structlog | Structured logging for debugging |
| Testing | pytest | Standard Python testing |

---

## Estimated Complexity

- **Low Complexity**:
  - HTTP server/client
  - ZPL generation (basic labels)
  - ZPL transmission to Zebra (simple TCP socket)

- **Medium Complexity**:
  - Panther Modbus/EtherNet-IP integration (I/O mapping well documented)
  - Queue management and async orchestration
  - Status monitoring and error handling

- **High Complexity**:
  - Advanced ZPL generation (complex labels with graphics)
  - IT-01 integration (unknown API structure)
  - Timing coordination between print and apply
  - Production-grade error recovery

---

## Final Thoughts

The system architecture is now **well-defined** based on the technical documentation:

**What We Know**:
1. ✅ **Print data path**: ZPL → Zebra print engine via TCP port 9100
2. ✅ **Control path**: Trigger/Status → Panther PLC via Modbus TCP or EtherNet/IP
3. ✅ **I/O mapping**: Complete documentation for all inputs/outputs
4. ✅ **Label format**: ZPL with 6"x4" labels on Zebra ZE521-6 print engine
5. ✅ **Status monitoring**: Real-time cycle times, error codes, and fault detection

**Recommended Approach**:
1. **Start with ZPL testing**: Create sample ZPL labels and test on the Zebra print engine
2. **Build Panther client**: Implement Modbus TCP client with documented I/O mapping
3. **Mock IT-01**: Create a simple mock service to provide label data
4. **Integrate step-by-step**: Print engine first, then Panther control, then IT-01

**Key Success Factors**:
- The **separation of concerns** between print data (ZPL to Zebra) and control signals (Modbus to Panther) simplifies the architecture
- **Well-documented I/O mapping** from the EtherNet/IP manual eliminates guesswork for Panther integration
- **Standard ZPL protocol** means we can test label generation independently before full system integration
- **Built-in status monitoring** via Panther I/O enables robust error detection and recovery

**Next Immediate Steps**:
1. Obtain IT-01 API documentation and sample responses
2. Get IP addresses for Zebra and Panther devices
3. Create a sample ZPL label and test on the Zebra print engine
4. Build a minimal Modbus client to read Panther status
5. Develop mock services for isolated testing
