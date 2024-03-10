# ZPacket-spec
Specification for ZPacket V0.1.0

## Purpose

- To provide a simple packet based messaging system for between micro controllers and Single Board Computers (SBC) for my model railroad endevors
- Have basic addressing for support of multidrop serial bus
- Simple packet start identification
- Rudimentary Error Detection with CRC
- Allowing **variable** message length while suporting single byte packets up to support of moderate length data
- Independant of actual implementation for transmission. This allows multiple protocols and electrical implementations to be supported as needed

## Packet

### Overview

| Field Name | Length in Bytes |
| :- | :-: |
| Destination Address and packet start identifier | 1 |
| Sender Address | 1 |
| Data Length | 1 |
| Data | Value of Data Length |
| CRC | 1 | 

### By Field

#### Destination Address

This field contains the packet start identifier of 0b10XX_XXXX
Aso contains 6 bits of the address of who should receive the packet 0bXXAA_AAAA

When sending recommended is to bitwise OR the start identifier and the destionation. When receiving it recommended to bitwise AND a mask of 0b1100_0000 with the
received byte to see if bit pattern of 0b10XX_XXXX is present. If so then treat as start of packet and to decode the destination address by bitwise AND
with the mask 0b0011_1111. If the start packet bit pattern was not present then continue searching for packet start.

Reason that the Packet Start Identifier is in the first byte is it allows for single byte look ahead for packet start. Allows for reciever to come alive during
packet transmission and for reciever to be synchronized with next packet start. Also by using an alternating bit state of 1 and 0 in the identifier we can prevent
transmission lines that are stuck in a single state from causing reciever to believe packet has started. Also by only using 2 bits allows for still 64 addresses to be
available for use.

Destination Packet is first as that allows for receivers to rapidly identify packets that belong to them or to another. Not sure though this is as beneficial in practice.

#### Sender Address

This field contains the address of the sender of the current packet. The sender address can be decoded by bitwise AND 0b0011_1111 with the received byte. 
Currently the two most significant bits are reserved for future use and should be ignored.

#### Data Length

This field contains the length of the Data section in bytes.

#### Data

This field is variable length see **Data Length**. This section can contain whatever data one wishes to contain as the receiver and transmitters should just read and send as bytes.

#### CRC

This field is the sum of all prior fields bitwise XORed together. The XOR calculation should start with the value of the destination address **byte as received** and NOT 0.

This field is intended to allow receivers to detect errors during transmission but not what was received incorrectly


