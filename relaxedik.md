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
    git clone https://github.com/inmo-jang/relaxed_ik-origin.git -b -dev
    ```
    
    If the above cloning doesn't work, then use this alternatively:
    ```
    git clone https://github.com/inmo-jang/relaxed_ik-origin.git
    git checkout dev
    ```
    
    
- You also need to set some parameters in `start_here.py` in `relaxed_ik` package. 
- NOTE: You should rename the source folder `relaxed_ik-origin` to `relaxed_ik`. 

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
    * Rust-version (recommended) : `roslaunch relaxed_ik relaxed_ik_rust.launch`
    * Python-version: `roslaunch relaxed_ik relaxed_ik_python.launch`

* Rviz (Rviz takes `\joint_states` from the robot and provides new joint position command via `/joint_group_position_controller/command`)
```
roslaunch relaxed_ik rviz_viewer_no_pub_norsp.launch 
rosrun relaxed_ik marker_ikgoal_driver.py
```

-------------------------
## Teleoperation

* (Update: 25 Nov 2019) Now, we can use another user interface when using `marker_ikgoal_driver.py`. In the code, you need to define `user_interface`, and make sure the appropriate input device node is running. 

    * Joystick 
        1. Make sure the joystick connected: `rosparam set joy_node/dev "/dev/input/js1"`
        2. Run joy_node: `rosrun joy joy_node`
        
        
    * HTC Vive
        0. Turn on HTC vive controllers.
        1. Launch the SteamVR's vrserver: `roslaunch vive_ros server_vr.launch` 
        2. Launch the node: `roslaunch vive_ros vive.launch`
        * Note: To close the node you can Ctrl+C. To close the vr server you have to kill the process. For convenience: `rosrun vive_ros close_servervr.sh`
        

    * Execution with Unity (using `Twist` msg)
        1. Launch rosbridge: `roslaunch rain_unity ur5_robotiq_unity_real.launch`
        2. Run a Unity application. 
        

### Results
* Real UR5: [https://www.youtube.com/watch?v=e6j5HR66wDg](https://www.youtube.com/watch?v=e6j5HR66wDg)
* Gazebo UR5: [https://youtu.be/1DjfZrxbu0s](https://youtu.be/1DjfZrxbu0s)
* Precise mode vs Smooth mode: [https://www.youtube.com/watch?v=Kw83MVIgj4Q](https://www.youtube.com/watch?v=Kw83MVIgj4Q)

