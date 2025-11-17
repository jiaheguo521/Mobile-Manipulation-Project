# Mobile-Manipulation-Project

**Institution:** KTH Royal Institute of Technology

**Course:** Introduction to Robotics

## Project Overview

This repository contains the solutions for Assignment 5, the final project for the KTH Introduction to Robotics course. The project's goal is to implement a robust, high-level mission planner for a **TIAGo robot** operating in a simulated apartment environment using **Gazebo**.

The robot is tasked with a complete mobile manipulation pipeline: autonomously navigating the apartment, perceiving an object (an Aruco cube), picking it up from one table, and placing it on a second table.

This repository includes the solutions for all three mission levels:

- **Task E:** Pick & Place using a State Machine with predefined poses.
- **Task C:** Pick & Place using a Behavior Tree with visual sensing.
- **Task A:** The most advanced level, adding robust localization and error handling to the Behavior Tree.

## Task E: Pick & Carry & Place without Sensory Input

This is the foundational task, implemented as a **State Machine (SM)** in `sm_students.py`.

The robot executes a fixed sequence of actions based on known (hardcoded) poses. The cameras and laser scanner are not used for object detection or localization.

**Mission Logic:**

1. **Pick:** Move to a predefined pose and execute the picking sequence for a cube at a known location on Table 1.
2. **Carry:** Navigate from Table 1 to a predefined pose at Table 2.
3. **Place:** Execute the placing sequence at a known location on Table 2.

## Task C: Pick & Carry & Place with Visual Sensing

This task introduces perception and more complex logic using a **Behavior Tree (BT)** in `bt_students.py`.

The robot must now use its head-mounted camera to find the Aruco cube before picking it and must verify the placement.

**Mission Logic:**

1. **Detect Cube:** Use the camera to locate the Aruco cube on Table 1.
2. **Pick:** Execute the picking task based on the detected cube's pose.
3. **Carry:** Navigate to Table 2.
4. **Place:** Execute the placing task.
5. **Verify:** Check if the cube was successfully placed. If not, the robot returns to the initial state to try again.

## Task A: Pick & Carry & Place with Sensing and Navigation

This is the most advanced task, building on the Task C Behavior Tree with robust localization and failure recovery.

The robot must handle an unknown starting position and potential failures during its mission.

**Mission Logic:**

1. **Localization:** The robot starts in an unknown pose. It spins in place to gather sensor data for the **AMCL (particle filter)**, waiting for the particle distribution to converge before proceeding.
2. **Navigation to Pick:** Once localized, the robot navigates to a predefined pose in front of the first table.
3. **Perception:** It uses its head-mounted camera to detect the Aruco cube on the table.
4. **Manipulation (Pick):** The robot executes the picking sequence using **MoveIt!** to grasp the cube.
5. **Navigation to Place:** The robot transports the cube to the second table, navigating through the apartment.
6. **Manipulation (Place):** The robot places the cube on the second table.
7. **Verification:** The robot checks to confirm the cube was successfully placed.

### Robustness & Error Handling

A key requirement of Task A is handling real-world failures. This BT implementation is robust to:

- **Robot Kidnapping:** If the robot is manually moved during navigation (simulating localization failure), the BT detects the large discrepancy in pose and triggers re-localization (spinning) to recover its position.
- **Failed Placement:** If the cube is dropped or knocked from the gripper during navigation, the final placement verification will fail. The BT handles this by returning to the start of the mission to attempt the task again.

## Technology Stack

- **ROS (Robot Operating System)**
- **Gazebo:** 3D robotics simulator
- **RViZ:** 3D visualization
- **MoveIt!:** Motion planning for manipulation
- **ROS Navigation Stack:** (AMCL, `move_base`) for localization and navigation
- **State Machines:** (`smach`) for high-level task logic
- **Behavior Trees:** (`py_trees`) for high-level mission logic
- **Python 3**

## Setup and Installation

These instructions are based on the KTH lab environment setup.

1. **Clone the Repository:**
Clone this repository into your Catkin workspace's `src` directory.
    
    ```
    cd ~/catkin_ws/src
    git clone [your-repo-url]
    
    ```
    
2. **Add Gazebo Model Database URI:**
Ensure the Gazebo model database is added to your `.bashrc`.
    
    ```
    echo 'export GAZEBO_MODEL_DATABASE_URI=http://models.gazebosim.org/' >> ~/.bashrc
    source ~/.bashrc
    
    ```
    
3. **Build the Project:**
Build your Catkin workspace.
    
    ```
    cd ~/catkin_ws
    catkin_make -DPYTHON_EXECUTABLE=/usr/bin/python3
    source devel/setup.bash
    
    ```
    

## How to Run

The simulation must be launched in two separate terminals.

1. **Terminal 1: Launch Gazebo & RViZ**
Wait for the Gazebo world and RViZ to fully load.
    
    ```
    roslaunch robotics_project gazebo_project.launch
    
    ```
    
2. **Terminal 2: Launch the Mission Planner**
Once the simulation is running, deploy the system and start the mission.
    
    ```
    roslaunch robotics_project launch_project.launch
    
    ```
    
    **Note:** The `launch_project.launch` file must be configured to run the correct script (`sm_students.py` for Task E, `bt_students.py` for Task C/A).
    

## Key Files

- `sm_students.py`: Contains the **State Machine** implementation for the **Task E** solution.
- `bt_students.py`: Contains the **Behavior Tree** implementation for both **Task C** and **Task A** solutions.
- `launch_project.launch`: The main ROS launch file used to configure and start all necessary nodes for the selected mission.

## Acknowledgements

This project was completed for the Introduction to Robotics course at KTH. A special thanks to the course instructors and TAs for providing the comprehensive assignment framework, robot simulation, and support.
