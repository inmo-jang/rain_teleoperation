# How to Control the Robot by Leap Motion


## Execution

### UR5 

* Real
```
roslaunch ur_modern_driver ur5_ros_control.launch robot_ip:=172.22.22.2
```

* Gazebo

```
roslaunch rain_gazebo ur5_robotiq.launch $(option)$
```

   Available options: 
   
     - "camera:=true" - Provides 3 virtual cameras. (Default: false) 
     
     - "gripper:=robotiq_3f" - Use a 3-finger RobotiQ gripper. (Default: none)

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

* You should set some parameters in "leapmotion_to_twist.py". 
```
roslaunch moveit_jog_arm jog_with_leapmotion.launch
rosrun moveit_jog_arm leapmotion_to_twist.py (Wiill be included in jog_with_leapmotion.launch)
```


### Leap Motion

* Genenate a fake leapmotion input for test purpuse

```
rosrun rain_test gen_fake_leap.py
```


