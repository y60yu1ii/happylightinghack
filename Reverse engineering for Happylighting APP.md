# Reverse engineering for Happylighting APP
- We got a set of Chinese cheap ble LED strip that controls via happy lighting app and wish to control it with my own app or ble device
## Ble Sniffing with iOS device
- A Mac and an iPhone connect to it with cable is needed.
- Xcode, apple developer account is needed
- iOS version > 13, Xcode > 11
### Steps
1. Download profile with this link and install it on iPhone.
https://developer.apple.com/bug-reporting/profiles-and-logs/?name=bluetooth
2. Download additional tools for xcode and install it. Open packet logger inside hardware folder.
3. Filter handle 0x0040 in my case
![](https://i.imgur.com/GAbGqIO.png)

## Cracking the protocols
- Thanks to [saberlight](https://gitlab.com/madhead/saberlight/blob/master/protocols/Triones/protocol.md) the status and static color protocols are the same.
- However, the modes is a different story.
### SPP like protocol

- Discovered the characteritics under FFD5 & FFD0, and we would easily found FFD9 as RX and FFD4 as TX

![](https://i.imgur.com/xJhre5d.jpg)
> FFD1 works, but not FFDA.
> thus I end up follow saberlight's version using only FFD9 & FFD4
 
### 1. Status
- Status works pretty much the same.
#### Request 
- [EF, 01, 77]
#### Result
- payload[0], always 0x66
- payload[1], ??
- payload[2], power status, 0x23 on, 0x24, off
- payload[3], modes, 0x41 static mode, starting from 0x00 in my led (up to 0xFF)
- payload[4], ??
- payload[5], speed
- payload[6], R
- payload[7], G
- payload[8], B
- payload[9], W
- payload[10], ?? 
- payload[11], always 0x99

### Power on/off
- [CC, 23/24, 33]
> 0x23 on, 0x24 off
> 
### Modes/Brightness/ intensity
- [9E, 00, mode, speed, intensity, 00, e9]
- modes from 0 - 255
- 