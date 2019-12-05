# Miscellaneous tests

## ros_control

  1. `/joint_group_position_controller/command` makes a robot go to the input position.
  
      - Launch a Gazebo robot: `roslaunch rain_gazebo ur5_robotiq.launch`
      - Switch controllers: 
      ```
      rosservice call /controller_manager/switch_controller "start_controllers:
      - 'joint_group_position_controller'
      stop_controllers:
      - 'pos_based_pos_traj_controller'
      strictness: 2"
      ```
      - You can test: `rostopic pub --once /joint_group_position_controller/command std_msgs/Float64MultiArray "data: [0,0,0,0,0,0]"`
  2. 
