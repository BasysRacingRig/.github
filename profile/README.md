# BasysRacingRig

## Overview

**BasysRacingRig** is a student project designed for the Digital Electronics Systems 2 course. This project aims to create a racing simulator controller system consisting of a steering wheel and pedals, which will interface with a computer using an FPGA-based controller. The project utilizes the **Basys3 FPGA board** featuring a **Xilinx Artix-7** FPGA and implements a communication system between the FPGA and a computer using **UART** (Universal Asynchronous Receiver/Transmitter). Additionally, the project incorporates a Python program that handles telemetry data and generates input signals for the game or simulation.

The FPGA logic is developed using **SystemVerilog**, a hardware description language, to ensure high-performance handling of input signals from the encoders and control of the VGA display for telemetry visualization.

This project is developed as part of a university course on **Digital Electronics Systems 2**.

## Features

- **Steering Wheel and Pedals:**
  - The steering wheel and pedals are equipped with encoders that provide precise measurements of their positions. These signals are processed by the FPGA and transmitted to the computer, enabling interaction with a racing game or simulation.
  
- **UART Communication:**
  - The Basys3 FPGA board communicates with the computer over the UART interface, transmitting the current positions of the steering wheel and pedals. This allows the game to receive real-time input from the user.

- **Telemetry Display on VGA:**
  - The FPGA is used to manage the VGA output, displaying telemetry data such as vehicle speed, RPM, engine temperature, and other racing parameters on the connected VGA monitor. This adds a layer of immersion, allowing the user to monitor the performance of the vehicle during the simulation.

- **Python Program:**
  - A Python-based program will be responsible for managing the communication between the FPGA and the computer, handling input signals, and displaying telemetry data. The program will also read telemetry information from the racing game and display it on the VGA screen.

## Technical Details

### Hardware
- **Basys3 FPGA Board:** The core of the system, running the SystemVerilog-based logic to interface with the encoders, manage UART communication, and control the VGA display.
- **Encoders:** Used for measuring the positions of the steering wheel and pedals.
- **VGA Display:** Used to visualize telemetry data, providing real-time feedback to the user during the racing simulation.
  
### Software
- **SystemVerilog:** The FPGA logic is implemented using SystemVerilog to handle real-time signal processing and communication with the computer.
- **Python:** A Python program is used to interface with the FPGA over UART, process incoming telemetry data from the game, and display it on the VGA screen. The program will also generate input signals for the game based on the steering wheel and pedal positions.

### Communication
- **UART Interface:** A serial communication protocol used to send data from the FPGA to the computer and vice versa. This enables real-time interaction with the simulation.

## Project Goals

- **Real-time Interaction:** The system will allow for real-time control of the vehicle in the racing simulation using the steering wheel and pedals. The data from the encoders will be processed by the FPGA and sent to the computer with minimal delay.
  
- **Telemetry Visualization:** The project will provide a visual representation of the racing parameters (such as speed, RPM, and engine temperature) on the VGA display, adding immersion to the experience.

- **Modular Design:** The system is designed to be expandable, allowing for future upgrades such as additional controls, Force Feedback for the steering wheel, or integration with other telemetry systems.

## How It Works

1. **Steering Wheel and Pedals:**
   - The steering wheel and pedals are equipped with encoders that track their positions. The FPGA processes the encoder signals to determine the current position of each input device (e.g., the angle of the steering wheel or the position of the accelerator/brake pedals).
   
2. **Communication with the Computer:**
   - The FPGA sends the position data of the steering wheel and pedals to the computer via UART. The computer receives this data and processes it as input signals for the racing game or simulation software.
   
3. **Telemetry Data Display:**
   - The FPGA is responsible for driving the VGA display, showing real-time telemetry data from the racing simulation (e.g., vehicle speed, RPM, temperature). The Python program will interface with the game to extract these values and send them to the FPGA for display.

4. **Python Program:**
   - The Python program is responsible for managing the communication between the FPGA and the computer. It will interface with the UART to receive input data from the steering wheel and pedals, and it will also handle reading telemetry data from the racing game and displaying it on the VGA screen.

## Installation

### Requirements

- **Basys3 FPGA Board**
- **Xilinx Vivado** (for synthesizing and programming the FPGA)
- **Python 3.x** (for the Python program)
- **VGA Monitor** (for displaying telemetry data)

### Steps

This project requires correct startup order between **Assetto Corsa**, the **FPGA board**, and the **Python driver**.  

## Requirements
- **FPGA**: Basys3 board + Xilinx Vivado (to synthesize and program the SystemVerilog design).  
- **Python 3.x** with required dependencies:  
  ```bash
  python -m venv .venv
  .venv\Scripts\activate        # Windows
  pip install -r requirements.txt
  ```
- **Assetto Corsa** installed on Windows PC.

## Program the FPGA
1. Open the project in **Xilinx Vivado**.  
2. Synthesize and program the **Basys3** board.  
   This configures the encoder (steering), input capture, and output logic.  

## Configure the Python Driver (UART)
The Python driver handles UART communication with the FPGA.  

- Manual setup is required: open the driver script (e.g. `driver.py`) and set the correct values for:  
  - **COM port** (check in Windows Device Manager, e.g. `COM5`)  
  - **Baud rate** (e.g. `921600`)  
  - Any other UART parameters (8N1, timeout, etc.)  
- These settings are **defined in the Python source file**, not passed as command-line arguments.  

Example snippet inside `driver.py`:
```python
import serial

ser = serial.Serial(
    port="COM5",       # <- set your COM port here
    baudrate=921600,   # <- set your baudrate here
    bytesize=8,
    parity="N",
    stopbits=1,
    timeout=0.1
)
```

## Configure Assetto Corsa
1. Launch **Assetto Corsa**.  
2. Open **Controls settings** and configure your steering wheel profile (axis and button mapping).  
3. **Do not start the Python driver yet** – see startup order below.  

⚠️ **Important:** Assetto Corsa will crash if the Python driver connects to the game’s shared memory before the race session is loaded. Always follow the sequence below.

## Startup Order (critical)
1. Program the FPGA (see above).  
2. Launch Assetto Corsa and load into the **pre-race screen** (session loaded, but not driving yet).  
3. Start the Python driver:  
   ```bash
   .venv\Scripts\activate
   python driver.py
   ```
   (UART settings must already be correct in the script.)  
4. Once the driver reports a successful connection, return to Assetto Corsa and press **Drive**.  
5. To restart: exit the driving session → stop the Python driver → (optionally) exit the game → then repeat the same order.

## Notes & Tips
- Always edit the **Python driver file** to match your hardware COM port and baudrate.  
- If the game crashes or freezes:  
  - Make sure the Python driver was **not started before loading the pre-race screen**.  
  - Check that no other program is accessing the game’s shared memory.  
  - Confirm the correct COM port in Device Manager.  

## Future Work and Extensions

- **Force Feedback:** Implementing force feedback in the steering wheel for a more immersive experience by simulating resistance based on the track conditions.
  
- **Additional Controls:** Expanding the controller setup to include more buttons, switches, or additional pedals (e.g., a clutch).

- **Advanced Telemetry:** Extending the telemetry display to include more vehicle parameters or creating a more complex telemetry dashboard.

## Conclusion

The **BasysRacingRig** project demonstrates the integration of FPGA-based hardware with a computer for a real-time racing simulation system. By combining **SystemVerilog** and **Python**, the project offers both high-performance hardware for input processing and flexible software for telemetry display and game interaction. This project serves as a practical application of digital electronics principles learned in the **Digital Electronics Systems 2** course, and it is designed with modularity and future expansion in mind.

---

**Note:** This project is a student assignment for the course **Digital Electronics Systems 2** and may be subject to future enhancements or modifications based on course feedback or additional features.
