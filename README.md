# Custom Ackermann Controller for ROS2

This ROS2 package provides an enhanced Ackermann steering controller with a smooth bicycle kinematic model, designed for accurate robot simulation and navigation in ROS2 Jazzy. It handles numerous edg[...] 

## Key Features

- **Smooth Bicycle Kinematics**: Implements `δ = atan(L*ω/v)` for accurate, Nav2-compatible steering. The controller logic is designed to prevent jerks and ensure smooth motion.
- **Robust Edge Case Handling**: 
    - **Turning in Place**: Manages commands with only an `angular.z` component (and `linear.x` is zero), allowing the robot to remain stationary.
    - **Velocity Deadzone**: Velocities below `0.001` are treated as zero to prevent minor drift from joystick noise.
    - Handles various other scenarios to ensure stable and predictable behavior.
- **Persistent Steering**: Maintains the current steering angle even when the robot is stopped, allowing for precise maneuvering.
- **Command Timeout with Auto-Reset**: If no commands are received for 2 seconds, the steering angle gradually and smoothly returns to the center position.
- **Teleoperation Options**:
    - **PS4 DualShock 4 Controller** (Recommended): Uses `joy` and `teleop_twist_joy` for responsive control.
    - **Standard Keyboard**: Uses the standard `teleop_twist_keyboard` for basic control.

## Demonstration

Below is a demonstration of the Ackermann controller in action within a Gazebo simulation, controlled using a PS4 controller.

*(Insert your demonstration video or GIF here)*

## Dependencies

- ROS2 Jazzy Jalisco
- `ros-jazzy-teleop-twist-joy`
- `ros-jazzy-joy`

To install dependencies:
```bash
sudo apt-get update
sudo apt-get install ros-jazzy-teleop-twist-joy ros-jazzy-joy
```

## Installation

1.  Navigate to your ROS2 workspace's `src` directory:
    ```bash
    cd ~/prototype_autonomous_bot_cir_ws/src
    ```
2.  (If not already present) Clone the repository:
    ```bash
    # git clone <your-repo-url>
    ```
3.  Build the package using `colcon`:
    ```bash
    cd ~/prototype_autonomous_bot_cir_ws
    colcon build --packages-select custom_ackermann_controller
    ```
4.  Source the workspace:
    ```bash
    source install/setup.bash
    ```

## Usage

To run the full simulation, open three separate terminals.

**Terminal 1: Launch Robot Simulation**
```bash
source ~/prototype_autonomous_bot_cir_ws/install/setup.bash
ros2 launch bot_description gazebo.launch.py
```

**Terminal 2: Run the Ackermann Controller**
```bash
source ~/prototype_autonomous_bot_cir_ws/install/setup.bash
ros2 run custom_ackermann_controller ackermann_twist_controller
```

**Terminal 3: Choose a Teleoperation Method**

### Option A: PS4 Controller (Recommended)

Ensure your PS4 controller is connected via Bluetooth or USB. You may need to set permissions for the joystick device:
```bash
sudo chmod 666 /dev/input/js0
```

Launch the joystick teleop node:
```bash
source ~/prototype_autonomous_bot_cir_ws/install/setup.bash
ros2 launch custom_ackermann_controller joystick_teleop.launch.py
```

**PS4 Controller Mappings:**
| Control | PS4 Input | Function |
|---|---|---|
| **Movement** | Left Stick Up/Down | Forward/Backward (linear.x) |
| **Steering** | Left Stick Left/Right | Turn Left/Right (angular.z) |
| **Enable** | L1 Button | **Must hold to activate movement** |
| **Turbo** | R1 Button | Hold for faster movement |

### Option B: Standard Keyboard Teleop

This uses the standard ROS2 keyboard teleop but does not support simultaneous forward and turning commands.

```bash
source ~/prototype_autonomous_bot_cir_ws/install/setup.bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

## Nodes & Scripts

### `ackermann_twist_controller`
The core node that subscribes to `/cmd_vel` (Twist messages) and converts them into Ackermann steering and velocity commands. It publishes `Float64MultiArray` messages to the `/forward_position_contro[...] 

### `test_ps4_controller.py`
A diagnostic script to test the PS4 controller. It subscribes to the `/joy` topic and displays crucial real-time information, including the raw data received from the controller and the corresponding [...]

## Launch Files

### `joystick_teleop.launch.py`
Launches the `joy_node` for joystick communication and the `teleop_twist_joy` node with pre-configured parameters for a PS4 DualShock 4 controller.
