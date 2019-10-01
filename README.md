# rain_teleoperation

This repo describes how to setup a Universal Robot for teleoperatoin. The environment I am using is Ubuntu 16.04 and ROS Kinetic version. 

## Installation

### univesral_robot
- Install universal_robot using apt, as recommended in https://github.com/ros-industrial/universal_robot: 
       "NOTE: please prefer using the binary release (see previous section) over building from source where possible. Source installs will not be automatically updated by new package releases and require more work to setup."

```
    sudo apt-get install ros-$ROS_DISTRO-universal-robot
```

- replace `$ROS_DISTRO` with `hydro`, `indigo` or `kinetic`, depending on which ROS version you have installed.



### ur_modern_driver (https://github.com/inmo-jang/ur_modern_driver)

- Install ur_modern_driver (kinetic-devel), 

```
    git clone https://github.com/inmo-jang/ur_modern_driver.git -b kinetic-devel
```   

### Moveit (Kinetic)

```
sudo apt-get install ros-kinetic-moveit
```

### Gazebo_ros_plug_ing (for gazebo operatoin) 

- http://gazebosim.org/tutorials?tut=ros_installing&cat=connect_ros

```
sudo apt-get install ros-kinetic-gazebo-ros-pkgs ros-kinetic-gazebo-ros-control
```

### jog_arm (https://github.com/inmo-jang/moveit_jog_arm)

- Tutorial: https://ros-planning.github.io/moveit_tutorials/doc/arm_jogging/arm_jogging_tutorial.html
- Repo: https://github.com/ros-planning/moveit/tree/master/moveit_experimental/moveit_jog_arm

```
git clone https://github.com/inmo-jang/moveit_jog_arm.git
```


### rain_ros (https://github.com/inmo-jang/rain_ros)
```
git clone https://github.com/inmo-jang/rain_ros.git
```

### ros_control (http://gazebosim.org/tutorials/?tut=ros_control)



## Execution

### UR5 

* Real
```
roslaunch ur_modern_driver ur5_ros_control.launch robot_ip:=172.22.22.2
```

* Gazebo

```
roslaunch rain_gazebo ur5_robotiq.launch
```

### Moveit

```
roslaunch rain_moveit_config moveit_planning_execution.launch
roslaunch rain_moveit_config moveit_rviz.launch config:=true
```

- Move the robot to a normal position using Moveit (Otherwise, due to singularity, jog_arm does not work)


### Change ros_controller

* (Option 1) position-based controller : You will send Cartesian delta information as a input
```
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 2"
```

* (Option 2 - Only available for real robot) velocity-based controller : You will send Cartesian derivative information as a input (As of 1 Oct 2019, "gazebo_ros_control" is not fully support "VelocityJointInterface" - http://gazebosim.org/tutorials/?tut=ros_control)

```
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_vel_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 2"
```

### jog_arm

- This is a launch file to use a xbox joystick for controlling the robot. 
- Set 'ur_config.yaml' depending on the controller you use, and run below 

```
rosparam set joy_node/dev "/dev/input/jsX" (if necessary)
roslaunch moveit_jog_arm jog_with_xbox.launch
```

- Note: You need to install `joy` package: http://wiki.ros.org/joy/Tutorials/ConfiguringALinuxJoystick. 



