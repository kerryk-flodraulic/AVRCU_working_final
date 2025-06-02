
# [OFFICIAL] AARCOMM Virtualized RCU CAN Interface

This Flutter application provides a virtual interface for simulating digital remote control systems using CAN-based communication over Bluetooth Low Energy (BLE). Designed for embedded testing and hardware simulation, it emulates real-time CAN frame generation based on digital control states. It interfaces with devices such as the **Puisi MCBox** via BLE and provides full logging and filtering of CAN messages.

---

## Features

- Digital button groups for **Engine, Boom, Vacuum, Water Pump, Door, Wand, and more**
- Live CAN frame generation and transmission using structured 8-byte payloads
- BLE scanning, filtering, and connection via **flutter_blue_plus**
- Transmits CAN messages using the **Axiomatic CAN-to-Bluetooth Converter**
- Displays both **live and deduplicated (fixed)** CAN frame logs
- Multi-column filtering (Channel, ID, DLC, Data, Time, Direction, Contents)
- Live status indicator, stopwatch timer, and frame replay options
- Support for real CAN monitoring using **Kvaser CANking** or **PCAN-View**

---

## CAN Byte Mapping

The app encodes digital button states into specific byte/bit positions of an 8-byte CAN payload:

| Control Name                      | Byte | Bit | Description                       |
|----------------------------------|------|-----|-----------------------------------|
| Water Pump On                    | 1    | 0   | Activates water pump              |
| Water Pump Off                   | 1    | 1   | Deactivates water pump            |
| Engine Increase                  | 1    | 2   | Increases engine RPM              |
| Engine Decrease                  | 1    | 3   | Decreases engine RPM              |
| Water Pressure Increase          | 1    | 4   | Increases water pressure          |
| Water Pressure Decrease          | 1    | 5   | Decreases water pressure          |
| Boom Up                          | 2    | 0   | Raises the boom                   |
| Boom Down                        | 2    | 1   | Lowers the boom                   |
| Boom Retract                     | 2    | 2   | Retracts the boom                 |
| Boom Extend                      | 2    | 3   | Extends the boom                  |
| Boom Left                        | 2    | 4   | Moves boom left                   |
| Boom Right                       | 2    | 5   | Moves boom right                  |
| Vacuum On                        | 3    | 0   | Turns vacuum on                   |
| Vacuum Off                       | 3    | 1   | Turns vacuum off                  |
| Door Unlock                      | 3    | 3   | Unlocks door                      |
| Door Lock                        | 3    | 4   | Locks door                        |
| Dozer out(F4), Tank Raise        | 3    | 5   | Extends dozer/tank raise          |
| Dozer in(F4), Tank Lower         | 3    | 6   | Retracts dozer/tank lower         |
| Door Open                        | 3    | 7   | Opens door                        |
| Door Close                       | 4    | 0   | Closes door                       |
| Vibrator                         | 4    | 1   | Activates vibrator                |
| Wand Required                    | 8    | 0   | Wand required signal              |
| Wand Off                         | 8    | 1   | Wand turned off                   |
| Wand Dropped                     | 8    | 2   | Wand dropped indicator            |
| Wand Fault                       | 8    | 3   | Wand fault indicator              |

---

## Application Structure

- `BluetoothCanReader`: Main UI containing:
  - Bluetooth device scan/connect
  - Digital button controls
  - Filterable CAN frame log
  - Frame transmission logic
- `CanBluetooth`: BLE communication manager (connect, notify, write)
- `createCanFrame()`: Constructs frames using bit-mapped logic
- `getByteValues()`: Converts UI states to byte array
- `fixedCanHistoryMap`: Deduplicates logs by content
- `canHistory`: Live feed including all frames
- `groupedControls`: Logical grouping of buttons for UI layout

---

## Requirements

> [!NOTE]
>* Flutter 3.13 or later
>* **Axiomatic CAN-to-Bluetooth Converter** (Required)
>* **External power supply** to activate the CAN network (MCBOX)
>* CAN monitoring software:
>   * [Kvaser CANking](https://www.kvaser.com/downloads/)
>   * [PCAN-View](https://www.peak-system.com/PCAN-View.242.0.html)

> [!IMPORTANT]
> **Bluetooth and location permissions**
> This app uses:
> - `flutter_blue_plus`
> - `permission_handler`
> macOS:
> Add this to `macos/Runner/DebugProfile.entitlements`:
> ```
> <key>com.apple.security.device.bluetooth</key>
> <true/>
> ```

---


## Getting Started
> [!IMPORTANT]
>1. Connect the **Axiomatic Converter** to the **Puisi MCBox** and ensure the CAN network is powered and ensure to connect the Kvaser Hybrid CAN/LIN to the Windows device for mionitoring. Ensure to launch the application Kvaser CAN-king.

> - **Axiomatic Converter**


> <img width="301" alt="Screenshot 2025-05-30 at 14 48 30" src="https://github.com/user-attachments/assets/8b1c471a-b889-4d5d-9c65-98339b5b603d" />


> - **Kvaser Leaf Lite v2 device**

> <img width="301" alt="Screenshot 2025-05-30 at 14 48 30" src="https://github.com/user-attachments/assets/6c760d9d-4616-4787-af50-2446e00ef4fe" />


2. Launch the Flutter app on your device. *cross-platform support pending*

 _Flutter Application is launched via macOS "flutter run" then select '1' - This will soon be changed for cross platform usage_
![Screenshot 2025-06-02 at 16 26 16](https://github.com/user-attachments/assets/ee004bcc-8e30-4b42-9725-6b4d0c5f8c33)


3. Scan for nearby Bluetooth devices and connect to the Axiomatic device
![Screenshot 2025-06-02 at 16 27 16](https://github.com/user-attachments/assets/c0f47c23-fe2b-4cc0-b167-bc2634654ead)

4. Open Kvaser Can King (ensuring all the requiremnts above are met in terms of connection with the MCBOX and Axiomatic BT device as well as connecting the Kvaser Leaf lite to "Windows Device") then select _go on bus_.

![image](https://github.com/user-attachments/assets/69c10095-5f60-427d-8930-d157301fea46)


5.Use the virtual buttons to simulate control states from "AARCOM RCU Digital Control Buttons" and then select "Send to CanKing"
 ![Screenshot 2025-06-02 at 16 29 15](https://github.com/user-attachments/assets/f411767c-65b2-4195-80c9-6d48d3f8d891)

6. Open the Kvaser CAN Log output window and try generating frames to send to the AARCOM Virtualized RCU:
 ![image](https://github.com/user-attachments/assets/6cf73d00-8863-4ac6-aa5b-2593b270f51c)


7. Observe the behaviour of the Data in the "CAN Frame logs" in terms of data that was "Transmitted and Received" the logs are in _Fixed Format_ for readability:

![Screenshot 2025-06-02 at 16 30 43](https://github.com/user-attachments/assets/61bf82d0-4e19-4847-a7d4-f165134100d7)

8. Observe the Kvaser Can King Output window where you can see the most recent data sent populating to corresponding CAN ID and the information that was sent via the Universal Generator:

![image](https://github.com/user-attachments/assets/403779dc-e60f-4fde-88cd-305ea76c7963)



## License

© 2025 Kerry K @ Flodraulic. All rights reserved.
