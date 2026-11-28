# PAN, WPAN and Bluetooth

PAN connects personal devices, e.g. USB phone-to-laptop. WPAN is wireless; Bluetooth Classic (BR/EDR) commonly supports audio, BLE supports low-power sensors and wearables. A piconet has central and peripheral roles; exact behavior differs by Classic and LE. Bluetooth Mesh is an additional network capability, not every Bluetooth connection. Wireless USB is a historical technology. Range varies with radio, power, environment and topology.

```mermaid
flowchart TD
    C[Central device] --- P1[Peripheral headset]
    C --- P2[Peripheral watch]
    C --- P3[Peripheral keyboard]
```
Pairing negotiates security; bonding retains keys. BLE GATT exposes services and characteristics to authorized clients. Use authenticated pairing appropriate to device capability, encryption, updates and limited discoverability; remove unneeded pairings. Not all devices support identical roles.

Windows Settings > Bluetooth & devices; use Add device only for your own equipment. Read-only reference: Get-PnpDevice -Class Bluetooth (PowerShell). Ubuntu physical machine: BlueZ is free/open-source; [official source](https://github.com/bluez/bluez), [Bluetooth SIG](https://www.bluetooth.com/learn-about-bluetooth/tech-overview/), [LE primer](https://www.bluetooth.com/bluetooth-le-primer/). Optional package: sudo apt install bluez. bluetoothctl show reports local controller; bluetoothctl devices lists known devices. No scanning or pairing is required. WSL2 may not expose a Bluetooth controller; use Windows host or supported dedicated hardware.

Original example: authorized temperature sensor reports measurements to a phone, which sends approved data to an application. Minimize personal data, secure commissioning and gateway traffic. UniTrack should validate scanner/reader identity regardless of radio technology; ProTrack-MX should not trust proximity as authorization. No device accessed or install performed.
