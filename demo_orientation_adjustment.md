# Demo: Dynamic Orientation Adjustment

-------------------------
## Preparation

- Install: On top of the packages for [Leap Motion Teleoperation](https://github.com/inmo-jang/rain_teleoperation/blob/master/leapmotion_teleop.md), you need to install the following packages:

   - [dope](https://github.com/inmo-jang/Deep_Object_Pose): object detection algorithm
   - [ros_astra_camera_OLD](https://github.com/inmo-jang/ros_astra_camera_OLD): ROS package for Astra Orbbec
   - [rain_ai_assist](https://github.com/inmo-jang/rain_ai_assist): generates obstacle avoidance modulation 
   
* Copy `demo_orientation_adjustment.rviz` to `/.rviz`. 


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

- You should set some parameters in "leapmotion_to_twist.py", for example, scaling factors.  
```
roslaunch moveit_jog_arm jog_with_leapmotion.launch
rosrun moveit_jog_arm leapmotion_to_twist.py $(option = ROS)$ (TODO: Will be included in jog_with_leapmotion.launch)
```

- In ROS option, the node will take a leap motion input in ROS coordinate. Otherwise, it assume that an input in Unity coordinate will be used. 

- That is, for this demo, just use `rosrun moveit_jog_arm leapmotion_to_twist.py ROS`


### (5) Pose Estimation by DOPE

- Launch a Astra Camera and match its coordinate with the UR's coordinate 
   - The parameters `1.45 -0.05 0.135 3.141592 0 0` should be appropriately modified according to your test environment (i.e. the relative positions of the camera and the robot). 
```
roslaunch astra_camera astra.launch
rosrun tf2_ros static_transform_publisher 1.45 -0.05 0.135 3.141592 0 0 world camera_astra_link 
```

   
   
- Run DOPE and match the coordination of pose estimation result with the UR5's coordinate 
   - **TODO**: The pose estimation results from DOPE are not matched with the UR5's cooridnate. At the moment, these are addressed in a hacky way in `dope_to_obstacle.py`, wihch should be refactored later. 


```
roslaunch dope dope.launch
rosrun rain_ai_assist dope_to_obstacle.py
```


- Run Rviz
   - `rviz_modulation.py`: Publishes some Marker topics to visualise additional information. 
   
```
rosrun rain_ai_assist rviz_modulation.py
rosrun rviz rviz -d ~/.rviz/demo_orientation_adjustment.rviz
```

### (6) Leap Motion with Dynamic Obstacle Avoidance

- Genenate a fake leapmotion input, which will then be modulated by the dynamic obstacle avoidancce algorithm. 
   - **TODO**: `demo_dynamic_obstacle.py` should be separated to a node generating a fake leap motion input and another node that addresses dynamic obstacle avoidance. 
   
```
rosrun rain_ai_assist demo_dynamic_obstacle.py
```


