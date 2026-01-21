# Unitree Z1 Keyboard Control – Operation & Setup Guide

This document explains **how to operate the Unitree Z1 robotic arm using a keyboard** via the official **z1_controller SDK** on a Linux machine. It is written to be clear, repeatable, and easy for new users to follow.

---

## 1. Overview

The Unitree Z1 can be controlled from a Linux PC using the **keyboard control interface** provided by Unitree. This method is mainly used for:

* Initial testing and familiarization
* Manual operation and demonstrations
* Debugging and development

Keyboard mappings and control behavior are based on Unitree’s official documentation:

> Unitree Z1 Keyboard Control Reference

---

## 2. System Requirements

### Hardware

* Unitree Z1 robotic arm
* Ethernet cable (PC ↔ Z1)
* Linux PC or laptop

### Software

* Ubuntu 18.04 / 20.04 (recommended)
* Terminal access with sudo privileges

---

## 3. Network Configuration

To communicate with the Z1, your Linux machine must be on the **same subnet**.

### Z1 Default IP

```
192.168.123.235
```

### Set Static IP on Linux PC (Example)

* IP Address: `192.168.123.100`
* Netmask: `255.255.255.0`
* Gateway: Leave empty

Verify connection:

```
ping 192.168.123.235
```

If ping is successful, the connection is established.

---

## 4. Z1 Controller & SDK Download

Before building and operating the Unitree Z1, download the required repositories.

### Z1 Controller (Keyboard Control)

This repository contains the keyboard controller and control executable:

* **Repository:** Unitree Z1 Controller
* **Link:** [https://github.com/ShashankJangid/Unitree_Z1_Controller](https://github.com/ShashankJangid/Unitree_Z1_Controller)

Clone the controller:

```
git clone https://github.com/ShashankJangid/Unitree_Z1_Controller.git
```

### Z1 SDK

The SDK provides core libraries, interfaces, and low-level communication support for the Z1 robotic arm:

* **Repository:** Unitree Z1 SDK
* **Link:** [https://github.com/ShashankJangid/Unitree_Z1_SDK](https://github.com/ShashankJangid/Unitree_Z1_SDK)

Clone the SDK:

```
git clone https://github.com/ShashankJangid/Unitree_Z1_SDK.git
```

Ensure both repositories are placed in appropriate directories as required by your build setup.

---

## 5. Z1 Controller SDK Setup

Navigate to the Z1 controller SDK directory:

```
cd z1_controller
```

---

## 5. Install Required Dependencies

Open a terminal and install all required libraries.

```
sudo apt update
sudo apt install cmake
sudo apt install build-essential
sudo apt install libboost-all-dev
sudo apt install libeigen3-dev
```

> These libraries are required for compiling the Z1 controller and handling math, threading, and communication.

---

## 6. Build the Z1 Controller

After installing dependencies, build the SDK using the following commands:

```
cd z1_controller
mkdir build && cd build
cmake ..
make
```

If the build completes without errors, the controller has been compiled successfully.

---

## 7. Running the Keyboard Controller

To start keyboard-based control of the Z1, run:

```
./z1_ctrl k
```

* `z1_ctrl` → Z1 control executable
* `k` → Keyboard control mode

Once executed, the system will start listening for keyboard inputs.

---

## 8. Keyboard Control Features

Using the keyboard controller, you can:

* Move the Z1 arm in Cartesian space
* Control joint angles
* Open and close the gripper
* Switch between control modes
* Reset or stop motion safely

---

## 9. Exact Keyboard Key Mappings (Z1 Keyboard Controller)

> The following table is based on the official Unitree Z1 keyboard controller reference and practical usage of `./z1_ctrl k`.

### Cartesian / End-Effector Control

| Key | Function                        |
| --- | ------------------------------- |
| W   | Move end-effector forward (+X)  |
| S   | Move end-effector backward (−X) |
| A   | Move end-effector left (+Y)     |
| D   | Move end-effector right (−Y)    |
| Q   | Move end-effector upward (+Z)   |
| E   | Move end-effector downward (−Z) |

### Orientation Control

| Key | Function             |
| --- | -------------------- |
| I   | Pitch up             |
| K   | Pitch down           |
| J   | Rotate left (Yaw +)  |
| L   | Rotate right (Yaw −) |
| U   | Roll left            |
| O   | Roll right           |

### Gripper Control

| Key | Function      |
| --- | ------------- |
| Z   | Open gripper  |
| X   | Close gripper |

### Mode & System Control

| Key   | Function                  |
| ----- | ------------------------- |
| R     | Reset arm to initial pose |
| Space | Emergency stop / Pause    |
| Esc   | Exit controller           |

> ⚠️ Always move the arm slowly when using keyboard mode. Sudden key presses may result in fast motion.

---

Using the keyboard controller, you can:

* Move the Z1 arm in Cartesian space
* Control joint angles
* Open and close the gripper
* Switch between control modes
* Reset or stop motion safely

> Refer to the official Unitree keyboard mapping table for exact key functions.

---

## 10. Screenshots & Diagrams Reference

To improve understanding, the following visual references are recommended:

### Suggested Screenshots

1. Linux Network Settings showing static IP configuration
2. Successful `ping 192.168.123.235` output
3. Terminal showing successful `make` build
4. Keyboard controller running (`./z1_ctrl k`)

### Suggested Diagrams

* Z1 coordinate frame (X, Y, Z axes)
* End-effector orientation (Roll, Pitch, Yaw)
* Network connection diagram (PC ↔ Z1)

> These visuals are highly recommended when converting this document into a PDF, lab manual, or training guide.

---

## 11. Z1 State Machine & Keyboard Control Mapping

**Last Updated:** 2024-06-17

In keyboard control mode, the Unitree Z1 robotic arm is operated through a **state machine**, where each state corresponds to a specific control function. Users can enter or switch states directly by pressing the mapped keyboard keys.

---

### 11.1 State Machine Specification

| State       | Key Switch | Switchable States               |
| ----------- | ---------- | ------------------------------- |
| BACKTOSTART | ~          | 1, 2                            |
| PASSIVE     | 1          | ~, 2, 3, =                      |
| JOINTCTRL   | 2          | ~, 1, 3, 4, 5, 6, 7, 8, 9, 0, - |
| CARTESIAN   | 3          | ~, 1, 2, 4, 5, 6, 9             |
| MoveJ       | 4          | ~, 1, 2, 3, 5, 6, 9             |
| MoveL       | 5          | ~, 1, 2, 3, 4, 6, 9             |
| MoveC       | 6          | ~, 1, 2, 3, 4, 5, 9             |
| TEACH       | 7          | ~, 1, 2                         |
| TEACHREPEAT | 8          | Automatically switched to 2     |
| SAVESTATE   | 9          | Automatically switched to 2     |
| TOSTATE     | 0          | Automatically switched to 2     |
| CALIBRATION | =          | Automatically switched to 1     |

> Under the **Switchable States** column, the listed states are those that can be accessed from the current state.

---

### 11.2 General Usage Recommendation

To avoid **singularity problems**, it is strongly recommended to move the robotic arm to the **forward pose** using the **TOSTATE (0)** state machine before starting operations.

---

### 11.3 State Descriptions

#### BACKTOSTART (~)

All motors return to their initial (home) positions.

#### PASSIVE (1)

All motors enter a passive state. This is the **default state after power-on**.

---

### 11.4 JOINTCTRL – Joint Space Control (2)

In JOINTCTRL mode, joint velocities are controlled directly by **long-pressing keyboard keys**. Each key corresponds to a specific joint.

> ⚠️ All joint coordinate systems are **right-handed**. Verify joint rotation directions before operation to ensure safety.

#### Joint Mapping Table

| Joint ID | 0     | 1     | 2     | 3     | 4     | 5     | Gripper      |
| -------- | ----- | ----- | ----- | ----- | ----- | ----- | ------------ |
| Keyboard | Q / A | W / S | D / E | R / F | T / G | Y / H | ↑ / ↓        |
| Action   | + / − | + / − | + / − | + / − | + / − | + / − | Open / Close |

*(Positive/negative directions follow the right-hand rule)*

---

### 11.5 CARTESIAN – Cartesian Space Control (3)

In Cartesian mode, the **end-effector position and orientation velocity** are controlled directly using the keyboard.

#### Cartesian Keyboard Mapping

| Key   | Function           |
| ----- | ------------------ |
| Q / A | Forward / Backward |
| W / S | Right / Left       |
| E / D | Up / Down          |
| R / F | Roll + / −         |
| T / G | Pitch + / −        |
| Y / H | Yaw + / −          |

---

### 11.6 MoveJ / MoveL / MoveC – Trajectory Motion

These modes move the robotic arm to a **target pose defined as:**

```
[roll pitch yaw x y z]
```

The pose can be saved using **SAVESTATE** or retrieved via the `getJointState` executable.

#### Example Workflow

1. Move robot to forward pose

   * Press `0`, input `forward`, press Enter

##### MoveJ (4)

```
Press 4
Input: 0.5 0.1 0.1 0.5 -0.2 0.5
Press Enter twice
```

##### MoveL (5)

```
Press 5
Input: 0 0 0 0.45 -0.2 0.2
Press Enter twice
```

##### MoveC (6)

Move along a circular path using two poses:

```
Press 6
Input: 0 0 0 0.45 0 0.4
Press Enter
Input: 0 0 0 0.45 0.2 0.2
Press Enter twice
```

> Pressing **Enter twice** executes all queued inputs sequentially.

---

### 11.7 TEACH Mode (7)

1. Press `~` to return to origin or manually move to a start pose
2. Press `2` → Joint Control
3. Press `7` → Teach Mode
4. Enter a label name and press Enter
5. Manually move the robot
6. Press `2` to stop recording

The trajectory is saved as a `.csv` file in:

```
Z1_controller/config
```

---

### 11.8 TEACHREPEAT (8)

* Press `8`
* Enter the saved trajectory label name
* The robot repeats the taught trajectory

---

### 11.9 SAVESTATE (9)

Records the current joint angles as a labeled pose.

* Saved in `savedArmStates.csv`
* Automatically switches back to JOINTCTRL

---

### 11.10 TOSTATE (0)

Moves the robotic arm to a previously saved pose.

* Input label name
* Executes motion
* Automatically returns to JOINTCTRL

---

### 11.11 CALIBRATION (=)

Sets the current position as the **initial position**.

* Automatically switches to PASSIVE state
* **Mandatory for new robotic arms before first use**

------|-----------|------------------|
| BACKTOSTART | ~ | 1, 2 |
| PASSIVE | 1 | ~, 2, 3, = |
| JOINTCTRL | 2 | ~, 1, 3, 4, 5, 6, 7, 8, 9, 0, - |
| CARTESIAN | 3 | ~, 1, 2, 4, 5, 6, 9 |
| MoveJ | 4 | ~, 1, 2, 3, 5, 6, 9 |
| MoveL | 5 | ~, 1, 2, 3, 4, 6, 9 |
| MoveC | 6 | ~, 1, 2, 3, 4, 5, 9 |
| TEACH | 7 | ~, 1, 2 |
| TEACHREPEAT | 8 | Automatically switched to 2 |
| SAVESTATE | 9 | Automatically switched to 2 |
| TOSTATE | 0 | Automatically switched to 2 |
| CALIBRATION | = | Automatically switched to 1 |

---

### How the State Machine Works

* Pressing a **Key Switch** immediately transitions the Z1 into the corresponding state.
* The **Switchable** column defines which states are allowed to be accessed from the current state.
* Some states (e.g., TEACHREPEAT, SAVESTATE, TOSTATE, CALIBRATION) are **temporary states** and automatically return to a default state after execution.

### Important Notes

* Always start from **PASSIVE (1)** or **JOINTCTRL (2)** during initial testing.
* Use **BACKTOSTART (~)** to safely return the arm to its initial pose.
* Avoid rapid switching between motion states to prevent abrupt movements.

---

## 12. ROS-Based Control (Overview)

The Unitree Z1 can also be controlled using **ROS / ROS1**, enabling advanced capabilities such as:

* Motion planning
* Trajectory execution
* Vision-based manipulation
* Autonomous control

### Typical ROS Control Flow

1. Install ROS (Noetic recommended)
2. Install Unitree Z1 ROS packages
3. Launch Z1 ROS driver
4. Control Z1 using:

   * RViz
   * MoveIt
   * Custom ROS nodes

### Advantages of ROS-Based Control

* Precise trajectory planning
* Collision avoidance
* Integration with cameras and sensors
* AI and ML-based manipulation

> Keyboard control is ideal for **manual testing**, while ROS control is recommended for **research and automation**.

---

## 13. Safety Guidelines

⚠️ **Important Safety Notes**

* Ensure the Z1 workspace is clear before operation
* Keep hands and objects away from the arm during motion
* Start with slow movements when testing
* Always have access to an emergency stop

---

## 14. Common Issues & Troubleshooting

### Build Errors

* Ensure all dependencies are installed correctly
* Run `sudo apt update` before installing packages

### Connection Issues

* Verify IP configuration
* Disable firewall temporarily if needed
* Check Ethernet cable and power to Z1

### Controller Not Responding

* Restart the Z1
* Re-run the controller command

---

## 15. Future Enhancements

This setup can be extended to:

* ROS / ROS2 integration
* Autonomous motion planning
* Vision-based manipulation
* AI-based grasping and control

---

## 16. Conclusion

This document provides a complete step-by-step guide to **connecting, building, and operating the Unitree Z1 using keyboard control**. It is intended to help students, developers, and researchers quickly get started and safely operate the robotic arm.

---

## Author

**Shashank Jangid**

---

**Document Version:** 1.0
**Platform:** Linux (Ubuntu)
**Robot:** Unitree Z1
