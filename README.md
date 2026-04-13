# TurtleBot3 Custom Environment Mapping

This project was completed for the **Autonomous Vehicle Practical Course** task **“Mapping Your Custom Environment”**.  
The aim of the task was to create a custom Gazebo world, implement an autonomous mapping node for TurtleBot3, and generate a map of the environment using ROS 2 and SLAM.

## Project Overview

The project includes:
- a custom Gazebo world
- an autonomous mapping node (`mapping.py`)
- launch files for running the simulation
- generated map files

## Requirements

This project was made using:
- Ubuntu 22.04
- ROS 2 Humble
- Gazebo
- TurtleBot3 packages
- Cartographer
- Navigation2
- Docker classroom environment

## Required Packages

Install the required packages:

```bash
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

```bash
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
echo 'source /usr/share/gazebo/setup.sh' >> ~/.bashrc
echo 'export TURTLEBOT3_MODEL=burger' >> ~/.bashrc
source ~/.bashrc
```

## Workspace Setup

Clone the TurtleBot3 simulation package into the workspace:

```bash
cd ~/ros2_ws/
git submodule add -b humble https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git src/turtlebot3_simulations
```

Then enter Docker and build the workspace:

```bash
./docker_terminal.sh
cd ~/ws
. build_ws.sh
```

## Custom World Setup

The custom world was created in Gazebo Building Editor and saved inside:

```bash
~/ws/src/my_robot_controller/worlds/
```

Example:

```text
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
## Build the Workspace

After making the changes, rebuild the workspace:

```bash
cd ~/ws
. build_ws.sh
```

## Running the Mapping Process

### 1. Launch the custom world

```bash
ros2 launch my_robot_controller turtlebot3_world.launch.py
```

### 2. Launch Cartographer SLAM

```bash
ros2 launch turtlebot3_cartographer cartographer.launch.py use_sim_time:=True
```

### 3. Run the mapping node

```bash
ros2 run my_robot_controller mapping
```

## Save the Generated Map

After the robot has explored the environment, save the map with:

```bash
ros2 run nav2_map_server map_saver_cli -f ~/ws/my_robot_controller/maps/my_map
```

This saves the generated map files in the `maps` folder.

## Author

Sandra Põllu
