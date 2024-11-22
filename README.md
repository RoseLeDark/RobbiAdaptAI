# RobbiAdaptAI - Adaptive Speed Adjustment AI

## Overview

RoboAdaptAI is an adaptive AI module designed to dynamically adjust the speed of a robot based on the terrain type it is driving on. The AI assesses the terrain's surface and adjusts the speed of the robot on a scale from **1** to **9**, where **1** represents smooth surfaces and **9** represents extremely steep or untraversable terrain. The higher the value, the slower and more cautious the robot becomes to avoid damage or instability.

This lightweight AI model (approx. 10 KB) processes input data via serial communication and returns the appropriate speed adjustment in real-time.

The KI will be run of the ESP32-S3.

## Features

- **Adaptive Speed Control**: The robot's speed is adjusted dynamically based on terrain conditions, ranging from **1** (smooth surface) to **9** (impassable terrain).
- **Real-time Processing**: Input data is processed via serial communication, and the corresponding speed adjustment is calculated and returned in real-time.
- **Lightweight Model**: The AI is efficient and compact, with a footprint of approximately 10 KB.
- **Easy Integration**: Designed for integration with robotics projects using Arduino and ESP32 platforms.

## Communication Structure
The structure used for both input and output is defined using a `union`, which allows both a structured data format and a raw data message format. This enables flexibility when sending either structured data (`cmd`, `arg`, `hash`) or raw data (through `message`).


### Input/Output Format

The structure used for both input and output is as follows:

```c
typedef union {
    struct {
        uint8_t cmd;            // Command byte indicating the operation
        const char arg[16];     // Argument as a string (CSV format or command-specific)
        char hash[32];          // Murmur hash to ensure integrity of the data
    };
    void* message;              // Raw data message exchanged between devices (can carry additional data)
} robbi_cmd_t __attribute__((aligned(64)));
```
- cmd: A single byte command code indicating the action. For example:
  - MESSAGE_COMMAND_KI: AI should process terrain data.
  - MESSAGE_COMMAND_SCALE: AI has returned the speed scale value.
- arg: A string in CSV format containing the relevant data:
  - For terrain data: "x;y;z;temp", where:
    - x, y, z: Sensor data representing the current tilt angles (in degrees) of the robot.
    - temp: The temperature reading from a DHT11 sensor. If the temperature is below freezing (frost), the speed scale is increased by 1.
  - Example input (CSV format): "10;5;0;22" This represents a tilt of 10° in the X-axis, 5° in the Y-axis, 0° in the Z-axis, and a temperature of 22°C. 
- hash: hash: A Murmur hash generated from the cmd and arg fields to ensure data integrity and prevent tampering.
- message: Raw data passed between the Arduino and ESP32

### Example

- Input (terrain data):
```c
union {
    struct {
        cmd: MESSAGE_COMMAND_KI,
        arg: "10;5;0;22",          // Tilt values and temperature
        hash: "a1b2c3d4e5f6...",    // Murmur hash of cmd and arg
    };
    message: pointer, // The same data with above - this will send and recive
}
```
- Output (speed adjustment level)
```c
union {
    struct {
        cmd: MESSAGE_COMMAND_SCALE,
        arg: "5",                   // Adjusted speed scale (1 to 9)
        hash: "e4d3c2b1a0f9...",    // Murmur hash of cmd and arg
    };
    message: pointer, // The same data with above - this will send and recive
}
```
In this example, the robot receives terrain data, calculates the appropriate speed adjustment, and returns the speed scale value ("5" in this case) to the controlling system.

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/RoseLeDark/RobbiAdaptAI.git
2. Upload the AI code to your Arduino Uno R4 or ESP32 using your favorite IDE.
3. Connect the robot's sensors to the input pins and configure the communication via serial for data exchange.
4. Run your robot, and the AI will process terrain data and adjust the robot's speed based on the surface.

## License
`SPDX-License-Identifier: GPL-3.0-or-later or EUPL-1.2`

The RobbiAdaptAI project is licensed under different licenses based on the geographical location of use. The European Union Public License (EUPL) applies within the European Union (EU), while the GNU General Public License (GPL) applies worldwide. This dual licensing ensures that users in the EU benefit from the legal clarity and protections offered by the EUPL, while users worldwide are bound by the terms of the GPL.

### EU License: European Union Public License (EUPL)

For users within the **European Union (EU)**, the **RobbiAdaptAI** project is licensed under the **European Union Public License (EUPL)**. The EUPL is compatible with other open-source licenses like the GPL and LGPL, and it is specifically tailored to meet the legal framework of the European Union. 

#### Key Points:
- The **EUPL** allows you to freely use, modify, and distribute the **RobbiAdaptAI** code within the EU, under the conditions laid out in the license.
- If you modify the code and distribute it, you must also make the source code of your modifications available under the same **EUPL** license.
- The EUPL offers legal certainty for users and contributors within the EU and is recognized by European institutions.

You can find the full terms of the **EUPL** at:  
[European Union Public License (EUPL)](LICENSE-EUPL) and online: https://eupl.eu/

### Global License: GNU General Public License (GPL)

For users **outside of the European Union (EU)**, the **RobbiAdaptAI** project is licensed under the **GNU General Public License (GPL)**. The **GPL** is one of the most widely used open-source licenses worldwide and provides the freedom to use, modify, and distribute the software under specific terms.

#### Key Points:
- The **GPL** allows users to use, modify, and distribute the **RobbiAdaptAI** code, as long as any derivative works are also distributed under the **GPL**.
- Modifications to the **RobbiAdaptAI** code must be made available under the same **GPL** license when redistributed.
- The **GPL** ensures that all users have the freedom to use the software, but also requires that any modified code is made available to the public under the same license.

You can find the full terms of the **GPL** at:  
[GNU General Public License (GPL)](LICENSE-GPL) and online: https://www.gnu.org/licenses/


### License Compatibility

The **RobbiAdaptAI** project is released under **dual licensing**:
- **EUPL** applies within the **European Union (EU)**.
- **GPL** applies to all regions **outside the European Union**.

This approach allows for legal clarity and flexibility for users in different regions while ensuring the project stays open-source and accessible to all.

### Conclusion

By using **RobbiAdaptAI**, you agree to the terms and conditions of the respective licenses based on your geographical location. If you're in the **EU**, the **EUPL** applies. If you're outside the EU, the **GPL** applies.

Thank you for contributing to or using **RobbiAdaptAI**!


## Contributing
We welcome contributions! If you'd like to improve this AI model or suggest new features, feel free to fork the project, make your changes, and submit a pull request.

