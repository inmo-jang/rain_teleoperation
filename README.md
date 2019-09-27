# rain_teleoperation

This repo describes how to setup a Universal Robot for teleoperatoin. The environment I am using is Ubuntu 16.04 and ROS Kinetic version. 

## Installation
1) univesral_robot
- Install universal_robot using apt, as recommended in https://github.com/ros-industrial/universal_robot. 

```
    sudo apt-get install ros-$ROS_DISTRO-universal-robot
```

- replace `$ROS_DISTRO` with `hydro`, `indigo` or `kinetic`, depending on which ROS version you have installed.

- NOTE: please prefer using the binary release (see previous section) over building from source where possible. Source installs will not be automatically updated by new package releases and require more work to setup.


2) Moveit (Kinetic)

```
sudo apt-get install ros-kinetic-moveit
```

3) Gazebo_ros_plug_ing (for gazebo operatoin) 

- http://gazebosim.org/tutorials?tut=ros_installing&cat=connect_ros

```
sudo apt-get install ros-kinetic-gazebo-ros-pkgs ros-kinetic-gazebo-ros-control
```


## Run

```
roslaunch rain_gazebo ur5_robotiq.launch
roslaunch rain_moveit_config moveit_planning_execution.launch sim:=true
roslaunch rain_moveit_config moveit_rviz.launch config:=true
```

* Move the robot to a normal position using Moveit (Otherwise, due to singularity, jog_arm does not work)
* Change ros_controller

```
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 2"
```

* Then run jog_arm

```
rosparam set joy_node/dev "/dev/input/jsX"
roslaunch jog_arm jog_with_xbox.launch
```
