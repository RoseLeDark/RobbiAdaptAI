# RobbiAdaptAI - Adaptive Speed Adjustment AI

## Overview

RoboAdaptAI is an adaptive AI module designed to dynamically adjust the speed of a robot based on the terrain type it is driving on. The AI assesses the terrain's surface and adjusts the speed of the robot on a scale from **1** to **9**, where **1** represents smooth surfaces and **9** represents extremely steep or untraversable terrain. The higher the value, the slower and more cautious the robot becomes to avoid damage or instability.

This lightweight AI model (approx. 10 KB) processes input data via serial communication and returns the appropriate speed adjustment in real-time.


## Features

- **Adaptive Speed Control**: The robot's speed is adjusted dynamically based on terrain conditions, ranging from **1** (smooth surface) to **9** (impassable terrain).
- **Real-time Processing**: Input data is processed via serial communication, and the corresponding speed adjustment is calculated and returned in real-time.
- **Lightweight Model**: The AI is efficient and compact, with a footprint of approximately 10 KB.
- **Easy Integration**: Designed for integration with robotics projects using Arduino and ESP32 platforms.

## Communication Structure
Both the **Input** and **Output** messages follow the same structure and are communicated through serial between the devices (Arduino, ESP32) and the base station. This structure allows for flexible and consistent communication. In this Project sending terrain data or receiving the speed adjustment.

### Input/Output Format

The structure used for both input and output is as follows:

```c
struct {
    union {
        uint8_t cmd;            // Command byte indicating the operation
        const char arg[16];     // Argument as a string (CSV format or command-specific)
        char hash[32];          // Murmur hash to ensure integrity of the data
    };
    void* message;              // Raw data message exchanged between devices
} __attribute__((aligned(64)));
```
- cmd: A single byte command code indicating the action. For example:
  - MESSAGE_COMMAND_KI: AI should process terrain data.
  - MESSAGE_COMMAND_SCALE: AI has returned the speed scale value.
- arg: A string in CSV format containing the relevant data:
  - For terrain data: "x;y;z;temp", where:
    - x, y, z: Sensor data representing the current tilt angles (in degrees) of the robot.
    - temp: The temperature reading from a DHT11 sensor. If the temperature is below freezing (frost), the speed scale is increased by 1.
- hash: hash: A Murmur hash generated from the cmd and arg fields to ensure data integrity and prevent tampering.
- message: Raw data passed between the Arduino and ESP32

### Example

*Input* : "10;5;0;22"
This represents a tilt of 10° in the X-axis, 5° in the Y-axis, 0° in the Z-axis, and a temperature of 22°C.

*Output*: "5"
In this example, the robot receives terrain data, calculates the appropriate speed adjustment, and returns the speed scale value ("5" in this case) to the controlling system.


## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/RoseLeDark/RobbiAdaptAI.git
2. Upload the AI code to your Arduino Uno R4 or ESP32 using your favorite IDE.
3. Connect the robot's sensors to the input pins and configure the communication via serial for data exchange.
4. Run your robot, and the AI will automatically adapt the speed based on the surface it drives on.

## Using

The AI adjusts the robot's speed on a scale from 1 (slowest) to 9 (fastest) based on terrain characteristics.
Input data is sent via serial, and the AI calculates the optimal speed in real-time.

## License

This project is licensed under the GPL (General Public License) or EUPL (European Union Public License).
You are free to use, modify, and distribute the code as long as any derived works are also licensed under GPL or EUPL.

## Contributing
We welcome contributions! If you'd like to improve this AI model or suggest new features, feel free to fork the project, make your changes, and submit a pull request.

## Acknowledgments
Arduino Uno R4 for providing the hardware platform.
ESP32 for providing additional processing power.
