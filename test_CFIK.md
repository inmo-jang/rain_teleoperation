# Quantitative Performance Test

## (1) Only with Rviz (Without a Gazebo robot) 

Load the robot and launch a Rviz Window:
```
roslaunch relaxed_ik load_info_file.launch
roslaunch relaxed_ik rviz_viewer.launch
```

Run a IK solver, and the marker interface:
```
roslaunch relaxed_ik relaxed_ik_rust.launch
rosrun relaxed_ik marker_ikgoal_driver.py
```

**Set your parameters for (1) the IK; (2) Obstacle info; (3) Virtual teleoperator.**
  - For (2) & (3): See [https://github.com/inmo-jang/rviz_marker_publisher](https://github.com/inmo-jang/rviz_marker_publisher)
  
Then, run the followings:

```
rosrun rviz_marker_publisher gen_obstacles.py
rosrun rviz_marker_publisher virtual_teleoperator.py
```

Do postprocessing for `PubRate_XXX.txt` results (at the folder where output txt files are avilable):
```
python postprocess_PubRate.py
```

## (2) With a Gazebo robot

Launch a Gazebo robot
```
roslaunch rain_gazebo ur5_robotiq.launch
```

```
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'pos_based_pos_traj_controller'
strictness: 2"
```

Load the robot and launch a Rviz Window:
```
roslaunch relaxed_ik load_info_file.launch
roslaunch relaxed_ik rviz_viewer_no_pub_norsp.launch
```

Run a IK solver, and the marker interface:
```
roslaunch relaxed_ik relaxed_ik_rust.launch
rosrun relaxed_ik marker_ikgoal_driver.py
```

**Set your parameters for (1) the IK; (2) Obstacle info; (3) Virtual teleoperator.**
  - For (2) & (3): See [https://github.com/inmo-jang/rviz_marker_publisher](https://github.com/inmo-jang/rviz_marker_publisher)
  
Then, run the followings:

```
rosrun rviz_marker_publisher gen_obstacles.py
rosrun rviz_marker_publisher virtual_teleoperator.py
```

Do postprocessing for `PubRate_XXX.txt` results (at the folder where output txt files are avilable):
```
python postprocess_PubRate.py
```



# Usability Test

Follow the instruction for (2) until `rosrun relaxed_ik marker_ikgoal_driver.py`.

**Set your parameters for usability study** in `usability_tester.py`
  - `num_test`: the number of tests 
  
Then, run the followings:

```
rosrun rviz_marker_publisher usability_tester.py
```

