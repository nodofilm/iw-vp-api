# IW-VP-API

The Virtual Production (VP) API from NODO Film Systems.

### Compatible Firmwares
This API document is updated as of Inertia Wheels firmware 1.0.0.

## Instructions

### Setup
Ensure the [driver](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers) is installed.

Activate the "IW-VP" output from the Inertia Wheels > Menu > Remote Head > Output. 

### Port

Plug in the receiver to your computer via micro USB.

For Windows, the com port must be manually located.

For macOS, the com port is *cu.SLAB_USBtoUART*.

### UART

UART: 921600 Baud, 8N2

### Refresh Rate

100 Packets per Second aka 100Hz


## Packet Format

### Endianness

Data is sent as Big-Endian. Example, an int32_t made of the bytes arriving in the order of 0A 0B 0C 0D is equal to 0x0A0B0C0D = 168496141.

### Uint8_t Blips

The blip items are tied to buttons. Each time the button is pressed, the integer increases by 1. To follow button presses, you will need to track this value and when it changes, act on the change. 

### Packet

| Byte | Function | Format                | Example   | Hex |   |
|------|----------|-----------------------|-----------|-----|---|
| 0    | Header   | ASCII Char            | I         | 49  |   |
| 1    | Header   | ASCII Char            | W         | 57  |   |
| 2    | Header   | ASCII Char            | A         | 41  |   |
| 3    | Header   | ASCII Char            | P         | 50  |   |
| 4    | Header   | ASCII Char            | I         | 49  |   |
| 5    | Header   | ASCII Char            | x         | 78  |   |
| 6    | Version  | uint8_t               | 1         | 1   |   |
| 7    | Pan      | int32_t               |           | 0A  |   |
| 8    | Pan      | int32_t               |           | 0B  |   |
| 9    | Pan      | int32_t               |           | 0C  |   |
| 10   | Pan      | int32_t               | 168496141 | 0D  |   |
| 11   | Tilt     | int32_t               |           | 0A  |   |
| 12   | Tilt     | int32_t               |           | 0B  |   |
| 13   | Tilt     | int32_t               |           | 0C  |   |
| 14   | Tilt     | int32_t               | 168496141 | 0D  |   |
| 15   | Roll     | int32_t               |           | 0A  |   |
| 16   | Roll     | int32_t               |           | 0B  |   |
| 17   | Roll     | int32_t               |           | 0C  |   |
| 18   | Roll     | int32_t               | 168496141 | 0D  |   |
| 19   | Jog A    | int32_t               |           | 0A  |   |
| 20   | Jog A    | int32_t               |           | 0B  |   |
| 21   | Jog A    | int32_t               |           | 0C  |   |
| 22   | Jog A    | int32_t               | 168496141 | 0D  |   |
| 23   | Jog B    | int32_t               |           | 0A  |   |
| 24   | Jog B    | int32_t               |           | 0B  |   |
| 25   | Jog B    | int32_t               |           | 0C  |   |
| 26   | Jog B    | int32_t               | 168496141 | 0D  |   |
| 27   | Focus    | uint16_t              |           | 0A  |   |
| 28   | Focus    | uint16_t              | 2571      | 0B  |   |
| 29   | Iris     | uint16_t              |           | 0A  |   |
| 30   | Iris     | uint16_t              | 2571      | 0B  |   |
| 31   | Zoom     | uint16_t              |           | 0A  |   |
| 32   | Zoom     | uint16_t              | 2571      | 0B  |   |
| 33   | To One   | uint8_t (blip)               | 10        | 0A  |   |
| 34   | Reverse  | uint8_t (blip)               | 10        | 0A  |   |
| 35   | Pause    | uint8_t (blip)               | 10        | 0A  |   |
| 36   | Forward  | uint8_t (blip)              | 10        | 0A  |   |
| 37   | Reserved |                       |           |     |   |
| 38   | Reserved |                       |           |     |   |
| 39   | Reserved |                       |           |     |   |
| 40   | Reserved |                       |           |     |   |
| 41   | Reserved |                       |           |     |   |
| 42   | Reserved |                       |           |     |   |
| 43   | Reserved |                       |           |     |   |
| 44   | Reserved |                       |           |     |   |
| 45   | Checksum | CheckSum8 Modulo 256  |           |     |   |
| 46   | Footer   | ASCII Char            | ;         | 3B  |   |
| 47   | Footer   | ASCII Char            | !         | 21  |   |
| 48   | Footer   | ASCII Char            | ;         | 3B  |   |
