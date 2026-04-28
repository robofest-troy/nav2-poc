# ROS2 + Nav2 Local Workspace Setup Guide (Beginner Friendly)

## Overview
This guide helps you:
- Use shared ROS2/Nav2 install
- Create your own workspace
- Modify Nav2 safely
- Run full system with 4 terminals

---

## Workspace Structure
```
/opt/ros/humble   (shared)
~/ros2_ws/nav2
  ├── src/
  │   ├── my_nav2_bringup
  │   ├── my_robot_description
  │   ├── my_robot_navigation
```

---

## Step 1: Create Workspace
```bash
mkdir -p ~/ros2_ws/nav2/src
cd ~/ros2_ws/nav2
```

---

## Step 2: Copy Nav2 Defaults
```bash
cd ~/ros2_ws/nav2/src
cp -r /opt/ros/humble/share/nav2_bringup my_nav2_bringup
```

---

## Step 3: Make Package
```bash
ros2 pkg create my_nav2_bringup --build-type ament_cmake
```

Copy needed folders:
```bash
cp -r /opt/ros/humble/share/nav2_bringup/launch my_nav2_bringup/
cp -r /opt/ros/humble/share/nav2_bringup/params my_nav2_bringup/
cp -r /opt/ros/humble/share/nav2_bringup/maps my_nav2_bringup/
cp -r /opt/ros/humble/share/nav2_bringup/rviz my_nav2_bringup/
```

---

## Step 4: Use Local Files
Edit:
```bash
nano ~/ros2_ws/nav2/src/my_nav2_bringup/launch/navigation_launch.py
```

Change:
```python
get_package_share_directory('nav2_bringup')
```

To:
```python
get_package_share_directory('my_nav2_bringup')
```

---

## Step 5: Build
```bash
cd ~/ros2_ws/nav2
source /opt/ros/humble/setup.bash
colcon build
source install/setup.bash
```

---

## Step 6: Run System

### Terminal 1 (Gazebo)
```bash
source /opt/ros/humble/setup.bash
export TURTLEBOT3_MODEL=burger
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

### Terminal 2 (SLAM)
```bash
source /opt/ros/humble/setup.bash
ros2 launch slam_toolbox online_async_launch.py use_sim_time:=true
```

### Terminal 3 (Nav2)
```bash
source /opt/ros/humble/setup.bash
source ~/ros2_ws/nav2/install/setup.bash

ros2 launch my_nav2_bringup navigation_launch.py \
params_file:=~/ros2_ws/nav2/src/my_nav2_bringup/params/nav2_params.yaml \
use_sim_time:=true
```

### Terminal 4 (RViz)
```bash
source /opt/ros/humble/setup.bash
source ~/ros2_ws/nav2/install/setup.bash

rviz2 -d ~/ros2_ws/nav2/src/my_nav2_bringup/rviz/nav2_default_view.rviz
```

---

## TF Tree
```
map → odom → base_footprint → base_link
```

---

## Debug Commands
```bash
ros2 topic list
ros2 node list
ros2 topic echo /odom
ros2 run tf2_tools view_frames
ros2 run tf2_ros tf2_echo odom base_footprint
```

---

## References
https://docs.nav2.org/getting_started/index.html
https://docs.nav2.org/setup_guides/sensors/mapping_localization.html
https://docs.ros.org/en/humble/Tutorials/Beginner-Client-Libraries/Creating-A-Workspace/Creating-A-Workspace.html
