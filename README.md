# IW-VP-API

The Virtual Production (VP) API from NODO Film Systems.

For hardware requirements such as pin out, see [the hardware repository](https://github.com/nodofilm/iw-hardware).

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

### Translating Wheel Intergers into Camera Orrientation Coordinates

The int32_t data for pan, tilt and roll comes as cumulative integers. The intergers are scaled such that 0 = 0° and 360,000 is +360°, - 360,000 is -360°. So 1,000 integers per degree. Cumulative in that 720,000 would equate to a 0° position with 2 full rotations. 

In order to provide the best operating experience, we suggest deriving the difference in coordinates between packets. Then apply the difference as a position offset to the camera. For example, if the previous packet had a coordinate of 1,000, and the next has a coordinate of 2,000, derive the difference with subtraction. 2,000 minus 1,000 is +1,000, ie +1° change. 

Finally, this should be lossless. Every integer change should be talied and maintained. It is tempting to see +1° as a rate that can be applied to the camera until the next update is parsed. However, this is a shortcut that can result in performance that does not meet the standards of seasoned camera operators. Camera operators expect that when they return the wheel handle to a particular position, such as a 1 o'clock position, the camera will be in the exact same place it was last time. If the data is translated into a rate, the variances in packet timing/parsing will result in a gradual loss in discreet integers over time. 

It's important to understand that the origins of hand wheels are rooted in mechanical geared heads, and qualities of these geared heads have become foundational to the art and skill of operating. As a mechanical wheel turned, if the gears were engaged, the head moved. Because the wheel was mechanically linked, it was impossible for the camera position to shift away from a direct link to the wheel's position. Some operators rely on this as a way of knowing when they are about to hit a mark before they do. In an all-digital world, careful detail must be paid to maintain this. In the Inertia Wheels, the integers that change at the encoder are carefully maintained throughout the Inertia Wheel's signal chain.  

This is why we suggest losslessly deriving the difference in between packets. If a camera has already been positioned and a set of wheels are assigned to it and engage, one should imagine it will work like a mechanical gear head. The camera is orriented, that shouldn't change as the digital gears are engaged. And once that link is made any movement of the wheels after that point of engagement will result in precise, exact, lossless connection with the camera's position.
