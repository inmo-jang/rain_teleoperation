# How to Control the Robot with RelaxedIK

The method is basically similar to ["How to control the robot by a Xbox Joystick"](https://github.com/inmo-jang/rain_teleoperation/edit/master/xbox_teleop.md), but replacing Moveit with RelaxedIK. Currently, the user interface is Rviz Marker.

------------------------

## Preparation

- Install the following packages according to ["How to control the robot by a Xbox Joystick"](https://github.com/inmo-jang/rain_teleoperation/edit/master/xbox_teleop.md). 
    - `universal_robot`
    - `ur_modern_driver`
    - `ros_control`

- One top of that, you also need to install the followings:
    ```
    git clone https://github.com/inmo-jang/relaxed_ik.git -b -dev
    ```
    
- You also need to set some parameters in `start_here.py` in `relaxed_ik` package. 

-------------------------
## Execution (after all settings in `start_here.py`)

* Load a robot (Real or Gazebo)

    * Real UR5: `roslaunch ur_modern_driver ur5_ros_control.launch robot_ip:=172.22.22.2`
    * Gazebo UR5: `roslaunch rain_gazebo ur5_robotiq.launch`


* Change ros_control   
           
```
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 2"
```
    
* Load the robot info file (it automatically accesses to `/joint_states` and use it as `starting_config`)
```   
roslaunch relaxed_ik load_info_file.launch
```            

* Run Relaxed IK solver
```   
roslaunch relaxed_ik relaxed_ik_python.launch
```

* Rviz (Rviz takes `\joint_states` from the robot and provides new joint position command via `/joint_group_position_controller/command`)
```
roslaunch relaxed_ik rviz_viewer_no_pub_norsp.launch 
rosrun relaxed_ik marker_ikgoal_driver.py
```


### Results
* Real UR5: [https://www.youtube.com/watch?v=e6j5HR66wDg](https://www.youtube.com/watch?v=e6j5HR66wDg)
* Gazebo UR5: [https://youtu.be/1DjfZrxbu0s](https://youtu.be/1DjfZrxbu0s)


