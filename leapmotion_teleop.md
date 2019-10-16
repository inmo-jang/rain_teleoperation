# How to Control the Robot by Leap Motion

------------------------

## Preparation

- Install the packages for ["How to control the robot by a Xbox Joystick"](https://github.com/inmo-jang/rain_teleoperation/edit/master/xbox_teleop.md). 

- One top of that, you also need to install [rain_test](https://github.com/inmo-jang/rain_test), which contains a node providing a fake leap motion input. 

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
rosrun moveit_jog_arm leapmotion_to_twist.py $(option = ROS)$ (TODO: Will be included in jog_with_leapmotion.launch)
```
- In ROS option, the node will take a leap motion input in ROS coordinate. Otherwise, it assume that an input in Unity coordinate will be used. 

- That is, for this demo, just use `rosrun moveit_jog_arm leapmotion_to_twist.py ROS`

### (5) Leap Motion

* Genenate a fake leapmotion input for test purpuse

```
rosrun rain_test gen_fake_leap.py
```

------------------------

## Execution with Unity (Demo: Bare-Hand Teleoperation)

### (6) Bridge ROS and Unity

* You make sure that you run `rosrun moveit_jog_arm leapmotion_to_twist.py demo1`. Here, depending on your robot's position, you may need to do with `ROS` option or customise additional option. 

* Launch the following launch file (rosbridge):

```
 roslaunch rain_unity ur5_robotiq_unity_real.launch
```

* Then, in the Unity side, run the scene (Scene_191016.unity) with rosbridge.

### (7) Run Robotiq Gripper

* Run the gripper controller node (Only being activated in "MODE_1" of `\rain\status\teleoperation_mode`)

``` 
rosrun robotiq_s_model_control SModelTcpNode.py 192.168.1.11
rosrun robotiq_s_model_control SModelController_vr.py [gazebo or real]
```

* Result is : [https://www.youtube.com/watch?v=TQSg8v2cMcE](https://www.youtube.com/watch?v=TQSg8v2cMcE)


