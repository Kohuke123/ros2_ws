# TurtleBot3 Custom Environment Mapping & Autonomous Navigation

This repository contains the work completed for two tasks of the Autonomous Vehicle Practical Course: **"Mapping Your Custom Environment"** (Task 1) and **"Autonomous Navigation in Your Custom Map"** (Task 2). The first task focused on creating a custom Gazebo world and generating a map of it using SLAM, while the second task uses that same map to make the TurtleBot3 navigate autonomously through a sequence of predefined goal points using ROS 2 and Nav2.

## Project Overview

The project includes:

* a custom Gazebo world
* an autonomous mapping node (`mapping.py`)
* an autonomous navigation/mission node (`navigation.py`)
* launch files for running the simulation, mapping, and navigation
* generated map files

## Requirements

This project was made using:

* Ubuntu 22.04
* ROS 2 Humble
* Gazebo
* TurtleBot3 packages
* Cartographer
* Navigation2
* Docker classroom environment

## Required Packages

Install the required packages:

```
sudo apt install ros-humble-gazebo-*
sudo apt install ros-humble-cartographer
sudo apt install ros-humble-cartographer-ros
sudo apt install ros-humble-navigation2
sudo apt install ros-humble-nav2-bringup
sudo apt install ros-humble-turtlebot3-msgs
sudo apt install ros-humble-turtlebot3
```

## Environment Setup

Add the required environment variables:

```
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
echo 'source /usr/share/gazebo/setup.sh' >> ~/.bashrc
echo 'export TURTLEBOT3_MODEL=burger' >> ~/.bashrc
source ~/.bashrc
```

## Workspace Setup

Clone the TurtleBot3 simulation package into the workspace:

```
cd ~/ros2_ws/
git submodule add -b humble https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git src/turtlebot3_simulations
```

Then enter Docker and build the workspace:

```
./docker_terminal.sh
cd ~/ws
. build_ws.sh
```

## Custom World Setup

The custom world was created in Gazebo Building Editor and saved inside:

```
~/ws/src/my_robot_controller/worlds/
```

Example:

```
my_custom_world.world
```

To launch the custom world, the original `turtlebot3_world.launch.py` file was copied into the `launch` folder of `my_robot_controller` and modified to load the custom world.

## setup.py Changes

The `setup.py` file must include the package folders during installation:

```python
from glob import glob

data_files=[
    ('share/ament_index/resource_index/packages',
        ['resource/' + package_name]),
    ('share/' + package_name, ['package.xml']),
    ('share/' + package_name + '/launch', glob('launch/*.launch.py')),
    ('share/' + package_name + '/maps', glob('maps/*')),
    ('share/' + package_name + '/worlds', glob('worlds/*'))
],
```

The `navigation` node is also registered in `entry_points`:

```python
entry_points={
    'console_scripts': [
        'mapping = my_robot_controller.mapping:main',
        'navigation = my_robot_controller.navigation:main',
    ],
},
```

## Build the Workspace

After making the changes, rebuild the workspace:

```
cd ~/ws
. build_ws.sh
```

---

## Task 1 — Running the Mapping Process

1. Launch the custom world

```
ros2 launch my_robot_controller turtlebot3_world.launch.py
```

2. Launch Cartographer SLAM

```
ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True
```

3. Run the mapping node

```
ros2 run my_robot_controller mapping
```

### Save the Generated Map

After the robot has explored the environment, save the map with:

```
ros2 run nav2_map_server map_saver_cli -f ~/ws/my_robot_controller/maps/my_map
```

This saves the generated map files (`my_map.yaml` and `my_map.pgm`) in the `maps` folder. The same map is used as input for Task 2.

---

## Task 2 — Autonomous Navigation

In Task 2 the TurtleBot3 navigates autonomously through a sequence of predefined goal points inside the custom map generated in Task 1. The mission is fully automated by a single launch file and a custom Python node — no manual goal setting in RViz is required.

### Mission Node (`navigation.py`)

The node `navigation.py` is responsible for the mission logic. It:

* publishes the robot's starting pose to `/initialpose` so that AMCL can localize it,
* publishes each navigation target to `/goal_pose` for Nav2,
* listens to `/odom` to track the robot's position, and
* automatically publishes the next goal once the robot is within a small distance threshold of the current one.

### Running Task 2

After the workspace is built and sourced, the entire navigation pipeline is started with a single command:

```
ros2 launch my_robot_controller run_navigation.launch.py
```

This will:

1. open Gazebo with the custom world,
2. start RViz with the Nav2 navigation stack and the saved map,

---


# Task 3 / P3 — Autoware Autonomous Navigation

In Task 3, the Autoware autonomous driving stack is used to navigate an Ego vehicle in a simulated map. The task includes defining an initial vehicle pose, selecting at least three valid goal poses, creating a custom ROS2 node, and integrating the node with an Autoware launch file.

The main package used for this task is:

```bash
my_robot_controller
```

The custom Autoware navigation node is:

```bash
aw_navigation.py
```

The launch file for the full Autoware navigation task is:

```bash
car_nav.launch.py
```

---

## Autoware Environment Setup

The task was completed inside the Autoware Docker environment. Autoware terminal can be opened with:

```bash
cd ~/ros2_ws
./autoware_terminal.sh
```

## Ignoring TurtleBot Packages for Autoware

For Task 3, the TurtleBot packages are not needed. They can be ignored during the build process by creating `COLCON_IGNORE` files:

```bash
touch /ros2_ws/src/turtlebot3/COLCON_IGNORE
touch /ros2_ws/src/turtlebot3_simulations/COLCON_IGNORE
```

## Building Task 3

Inside the Autoware Docker terminal:

```bash
cd /ros2_ws

rm -rf build install log

source /opt/ros/humble/setup.bash
source /autoware/install/setup.bash

colcon build --symlink-install --packages-select my_robot_controller

source /ros2_ws/setup.bash
```

## Running Autoware Manually

To launch only the Autoware planning simulator:

```bash
ros2 launch autoware_launch planning_simulator.launch.xml \
map_path:=/autoware_map/sample-map-planning \
vehicle_model:=sample_vehicle \
sensor_model:=sample_sensor_kit
```

In a second Docker terminal, source the workspace and run the custom navigation node:

```bash
cd /ros2_ws
source /ros2_ws/setup.bash
ros2 run my_robot_controller av_nav
```

---

## Running Task 3 with One Launch File

The complete Task 3 pipeline can also be launched with:

```bash
cd /ros2_ws
source /ros2_ws/setup.bash
ros2 launch my_robot_controller car_nav.launch.py
```

This launch file starts:

1. the Autoware planning simulator
2. the sample planning map
3. the sample vehicle and sensor model
4. the custom `av_nav` navigation node

---


# Task 4 / P4 — AV Validation: Create and Simulate an Interactive Scenario

The parameterized scenario file is:

```bash
/scenario_4.yaml
```

## Scenario Setup

The scenario was created in the online TIER IV Scenario Editor: <https://scenario.ci.tier4.jp/scenario_editor>

The Kashiwanoha Lanelet2 map was imported from the Scenario Simulator V2 repository: <https://github.com/tier4/scenario_simulator_v2/blob/master/map/kashiwanoha_map/map/lanelet2_map.osm>

The Ego vehicle was given a route through the intersection. `Npc1` is time-triggered (added 1 s after simulation start) and crosses the intersection ahead of the Ego. `Npc2` is position-triggered — it is added once the Ego reaches a defined point along its lane, which makes the crossing conflict depend on how far the Ego has progressed.

## ScenarioModifiers (Parameter Sweep)

Two range variables were added at the top of the YAML file to mutate the scenario:

```yaml
ScenarioModifiers:
  ScenarioModifier:
    - { name: EGO_START_S,    start: 16, step: 2, stop: 34 }   # 10 values
    - { name: NPC2_TRIGGER_S, start: 36, step: 1, stop: 45 }   # 10 values
```

The cross-product gives **10 × 10 = 100 simulations**. The variables are then substituted into the Storyboard:

* `EGO_START_S` replaces the fixed `s:` value in the Ego `TeleportAction` (lane 34408).
* `NPC2_TRIGGER_S` replaces the fixed `s:` value in the `ReachPositionCondition` that launches `Npc2`.

## Running the Batch Simulation

Inside the Autoware + Scenario Simulator Docker container:

```bash
cd ~/ros2_ws
./autoware_terminal.sh
```

```bash
ros2 launch scenario_test_runner scenario_test_runner.launch.py \
  architecture_type:=awf/universe record:=false \
  scenario:='/autoware_map/scenarios/scenario_4.yaml' \
  sensor_model:=sample_sensor_kit vehicle_model:=sample_vehicle \
  output_directory:='/autoware_map/results' \
  global_real_time_factor:=5.0 launch_rviz:=false use_sim_time:=true
```

The scenario_test_runner reads the `ScenarioModifiers` block, generates the full cross-product, and runs each combination as `scenario_4_0`, `scenario_4_1`, ... `scenario_4_99`. If Autoware behaves unstably, the `global_real_time_factor` can be lowered to 2.0 or 3.0.

## Output Files

After the batch run finishes, the output directory contains:

* `result_junit.xml` — summary of all 100 iterations (failures, errors).
* `scenario_4_N.xosc` — one OpenSCENARIO file per iteration, containing the actual `EGO_START_S` and `NPC2_TRIGGER_S` values used in that run.

These two file types are joined together (by iteration index) to map each parameter combination to its outcome, which forms the basis of the domain analysis in the report.


## Author

Sandra Põllu
