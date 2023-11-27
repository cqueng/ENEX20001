# Week 4: Controlling Motors with L293D Motor Driver

## Overview
The L293D motor driver is a crucial component in robotics and electronic projects, enabling a microcontroller to control the rotation direction and speed of motors. It is designed to drive inductive loads such as relays, solenoids, DC and bipolar stepping motors.

### L293D Pinout Diagram

![L293D Pinout Diagram](https://lastminuteengineers.com/wp-content/uploads/arduino/L293D-Dual-H-Bridge-Motor-Driver-IC-Pinout.png)

### How the L293D Works
The L293D integrates two H-bridge circuits which can be used to drive two DC motors independently in any direction or a stepper motor. It can handle a current of up to 600mA per channel, and it has the ability to withstand peak currents of up to 1.2A.

## Pin Setup for Motor Control

For controlling motors with the L293D, various pins must be set to specific states:

### Motor A Pin Setup

| ENA (Enable) | IN1 (Input 1) | IN2 (Input 2) | Spinning Direction |
|--------------|---------------|---------------|--------------------|
| High         | Low (0)       | Low (0)       | Motor OFF          |
| High         | High (1)      | Low (0)       | Forward            |
| High         | Low (0)       | High (1)      | Backward           |
| High         | High (1)      | High (1)      | Motor OFF          |

**Note:** The ENA pin must be set high before setting IN1 and IN2 to control the motor's spin direction.

### Enabling the Motor Outputs

The ENA and ENB pins activate the motor control outputs, and by connecting these to microcontroller pins set high, you can control the motors' on and off states.

| Pin Name | Description |
|----------|-------------|
| ENA      | Enable pin for Motor A, should be connected to a PWM-capable pin for speed control. |
| IN1      | Direction control input 1 for Motor A. |
| IN2      | Direction control input 2 for Motor A. |


## Controlling Motor Enable State and Direction with Buttons

In this week's project, we will use one button to toggle the motors' enable state and two other buttons to control the direction of each motor.

### Motor Enable Control
- A single button is wired to a microcontroller pin, let's call it `BTN_EN`.
- Pressing this button toggles the enable state of both motors.
- When `BTN_EN` is pressed, `ENA` and `ENB` should be set high to enable the motors.

### Motor Direction Control
- Two buttons control the direction of each motor: `BTN_FWD` for forward and `BTN_BWD` for backward.
- Pressing `BTN_FWD` sets `IN1` high and `IN2` low for Motor A, and similarly uses `IN3` and `IN4` for Motor B to move the motors forward.
- Pressing `BTN_BWD` inverts the above logic levels for each motor, making them spin in the opposite direction.

### Pseudocode for Motor Control Logic

```c
if button BTN_EN is pressed:
    toggle ENA and ENB

if button BTN_FWD for Motor A is pressed:
    set IN1 high, IN2 low  // Forward direction for Motor A

if button BTN_BWD for Motor A is pressed:
    set IN1 low, IN2 high  // Backward direction for Motor A

if button BTN_FWD for Motor B is pressed:
    set IN3 high, IN4 low  // Forward direction for Motor B

if button BTN_BWD for Motor B is pressed:
    set IN3 low, IN4 high  // Backward direction for Motor B
```

**Note:** This pseudocode outlines the logic that should be implemented in the program for controlling the motors with buttons. The actual implementation will require configuring the microcontroller's pins as inputs for the buttons and outputs for the L293D control pins.


