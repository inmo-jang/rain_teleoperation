# HRI Test Setup for "Teleoperation of a robotic manipulator"

This instruction is to set up the HRI test. 

(1) Bring the robot: 
  * UR5
    - Connect to a UR5: 
       - Real Robot: `roslaunch ur_modern_driver ur5_ros_control.launch robot_ip:=172.22.22.2`
       - Gazebo: `roslaunch rain_gazebo ur5_robotiq.launch` (You should click the play button afterwards)
       
    - Switch ros_control:
      ```
      rosservice call /controller_manager/switch_controller "start_controllers:
      - 'joint_group_position_controller'
      stop_controllers:
      - 'pos_based_pos_traj_controller'
      strictness: 2"
      ```
    - Load the robot info file (automatically accessing to `/joint_states` and use it as `starting_config`): 
      ```
      roslaunch relaxed_ik load_info_file.launch
      ```
    - Run RelaxedIK solver: `roslaunch relaxed_ik relaxed_ik_python.launch`
    
    - Rviz (Rviz takes `\joint_states` from the robot and provides new joint position command via `/joint_group_position_controller/command`)
      ```
      roslaunch relaxed_ik rviz_viewer_for_hri_test.launch
      rosrun relaxed_ik marker_ikgoal_driver_for_hri_test.py
      ```
     - Using the marker in Rviz, move the arm slightly, which activates the rest of the process (Needs confirmation).

  * (TODO) Gripper: ``
  
(2) Camera
  * Run a RGB-D Camera: `roslaunch astra_camera astra.launch`
  * (TODO) Set the camera coordinate system: `rosrun tf2_ros static_transform_publisher 1.53 0.08 0.285 3.141592 0 0 world camera_astra_link`
  * Downsampling the pointcloud: `roslaunch pcl_ros voxel_grid_filter.launch gui:=false`
  
  * Run webcams: `roslaunch rain_test usb_cam_demo.launch`
     - options:`cam1:=true cam2:=true`. By default, `cam0` will launch. 
  
(3) Open a socket to Unity: `roslaunch rain_unity ur5_robotiq_unity_real.launch`

(4) Run a Unity Application:
  * Virtual Marker: After running the app, you should push "m" (which initialises the market position) and "p" (which starts publishing)
