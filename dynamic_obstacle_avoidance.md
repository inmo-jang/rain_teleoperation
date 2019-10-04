# Demo: Dynamic Obstacle Avoidance

## Preparation

- Install: On top of the packages for [Leap Motion Teleoperation](https://github.com/inmo-jang/rain_teleoperation/blob/master/leapmotion_teleop.md), you need to install the following packages:

   - [dope](https://github.com/inmo-jang/Deep_Object_Pose): object detection algorithm
   - [ros_astra_camera_OLD](https://github.com/inmo-jang/ros_astra_camera_OLD): ROS package for Astra Orbbec
   - [rain_ai_assist](https://github.com/inmo-jang/rain_ai_assist): generates obstacle avoidance modulation 
   
* Copy `demo_dyanmic_obstacle_avoidance.rviz` to `/.rviz`. 


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

* Note: Due to instability of rospy in terms of time, it was not easy to get the angular velocity of a human operator (e.g. it often happens that del_time = 0. I don't know why -_-;). So I decided to use "joint_group_position_controller" at the moment. 

### jog_arm

* You should set some parameters in "leapmotion_to_twist.py", for example, scaling factors.  
```
roslaunch moveit_jog_arm jog_with_leapmotion.launch
rosrun moveit_jog_arm leapmotion_to_twist.py $(option = ROS)$ (Wiill be included in jog_with_leapmotion.launch)
```
* In ROS option, the node will take a leap motion input in ROS coordinate. Otherwise, it assume that an input in Unity coordinate will be used. 

* For this demo, just use `rosrun moveit_jog_arm leapmotion_to_twist.py ROS`

### Pose Estimation by DOPE

* Launch a Astra Camera and match its coordinate with the UR's coordinate (hacky)
```
roslaunch astra_camera astra.launch
rosrun tf2_ros static_transform_publisher 1.45 -0.05 0.135 3.141592 0 0 world camera_astra_link 
```

* Run DOPE and match the coordination of pose estimation result with the UR5's coordinate (TODO: I have done it in a hacky way)
```
roslaunch dope dope.launch
rosrun rain_ai_assist dope_to_obstacle.py
```

* Run Rviz
```
rosrun rain_ai_assist rviz_modulation.py
rosrun rviz rviz -d ~/.rviz/demo_dyanmic_obstacle_avoidance.rviz
```

### Leap Motion with Dynamic Obstacle Avoidance

* Genenate a fake leapmotion input, which will then be modulated by the dynamic obstacle avoidancce algorithm. 

```
rosrun rain_ai_assist demo_dynamic_obstacle.py
```


