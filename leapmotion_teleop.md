# How to Control the Robot by Leap Motion

------------------------

## Installation

- Follow the instruction in ["How to control the robot by a Xbox Joystick"](https://github.com/inmo-jang/rain_teleoperation/edit/master/xbox_teleop.md). 

-------------------------
## Execution

### Step (1)-(3): UR5 + ros_control + Moveit

- Follow Step (1)-(3) of the instruction in ["How to control the robot by a Xbox Joystick"](https://github.com/inmo-jang/rain_teleoperation/edit/master/xbox_teleop.md). 

- In summary, these steps are as follows (for real robot case):

```
roslaunch ur_modern_driver ur5_ros_control.launch robot_ip:=172.22.22.2
```

```
roslaunch rain_moveit_config moveit_planning_execution.launch
```

```
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 2"
```


### (4) jog_arm

* You should set some parameters in "leapmotion_to_twist.py", for example, scaling factors.  
```
roslaunch moveit_jog_arm jog_with_leapmotion.launch
rosrun moveit_jog_arm leapmotion_to_twist.py (Wiill be included in jog_with_leapmotion.launch)
```


### (5) Leap Motion

* Genenate a fake leapmotion input for test purpuse

```
rosrun rain_test gen_fake_leap.py
```


