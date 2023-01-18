# ur3e_moveit_configB
ROS1 MoveIt package for The Construct's Master Class Phase 2 Project

`build` shell
(note below: if you've done a clean, you need to build `phase2_custom_interfaces` and `linear_algebra_tools` individually and source before running `colcon build`)
```
cd ros2_ws
colcon build
```
`gz` shell - must start twice
```
source ~/simulation_ws/devel/setup.bash
roslaunch rb1_base_gazebo warehouse_ur3e.launch
```
Make sure the gripper_controller is launched in the `ros1` info shell
```
source ~/catkin_ws/devel/setup.bash
rostopic list
```
You should see:
```
...
/gripper_controller/gripper_cmd/cancel
/gripper_controller/gripper_cmd/feedback
/gripper_controller/gripper_cmd/goal
/gripper_controller/gripper_cmd/result
/gripper_controller/gripper_cmd/status
/joint_group_position_controller/command
...
```
Also, in the `gz` shell, you should see
```
Controller Spawner: Loaded controllers: gripper_controller
Started ['scaled_pos_joint_traj_controller'] successfully
```
If the` gripper_controller` hasn't launched, simply restart gazebo and everything else,
but ideally you should have launched it twice from the get go to avoid this problem.


`param bridge` shell
```
source ~/catkin_ws/devel/setup.bash
roslaunch load_params load_params_arm.launch

source /opt/ros/foxy/setup.bash
ros2 run ros1_bridge parameter_bridge
```

In `ros2 info`  shell verify
```
ros2 topic list
/clock
/joint_states
/parameter_events
/rosout
/tf
/tf_static
/wrist_rgbd/depth/camera_info
/wrist_rgbd/depth/points
```

`action bridge` shell
```
source /opt/ros/noetic/setup.bash
source /opt/ros/foxy/setup.bash
source ~/ros2_ws/install/setup.bash
ros2 launch start_bridge start_bridge.launch.py
```
in `ros2 info`  shell verify
```
ros2 action list
/gripper_controller/gripper_cmd
/scaled_pos_joint_traj_controller/follow_joint_trajectory
```

## Step 0.5   Open/Close the gripper
### Real robot
To open the gripper
```
ros2 action send_goal /gripper_controller/gripper_cmd control_msgs/action/GripperCommand "command:
 position: 70.0
 max_effort: 0.0
"
```
To close
```
ros2 action send_goal /gripper_controller/gripper_cmd control_msgs/action/GripperCommand "command:
 position: 14.0
 max_effort: 0.0
"
```
### In simulation:
`gripper action` shell
To open the gripper
```
ros2 action send_goal /gripper_controller/gripper_cmd control_msgs/action/GripperCommand "command:
 position: 0.3
 max_effort: 0.0
"
```
To close
```
ros2 action send_goal /gripper_controller/gripper_cmd control_msgs/action/GripperCommand "command:
 position: -0.45
 max_effort: 0.0
"
```

This fails in the real lab - it hangs in `action bridge`
```
[action_bridge-2] [INFO] [1661419781.543245041] [bridge2.ros_bridge]: Sending goal
```
in `gripper`
```
user:~$ ros2 action send_goal /gripper_controller/gripper_cmd control_msgs/action/GripperCommand "command:
>  position: 70.0
>  max_effort: 0.0
> "
Waiting for an action server to become available...
Sending goal:
     command:
  position: 70.0
  max_effort: 0.0

Goal accepted with ID: 39e2c770a9f7452b9407d9d0095841d4
```
## Step 1   Configure MoveIt package
After creating the MoveIt package, edit `catkin_ws/src/ur3e_moveit_configB/config/ros_controller.yaml` to
```
ur_manipulator_controller:

type: scaled_pos_joint_traj_controller/JointTrajectoryController
```

Launch MoveIt (ros1)
```
. ~/catkin_ws/devel/setup.bash
. ~/simulation_ws/devel/setup.bash
roslaunch ur3e_moveit_configB my_planning_execution.launch

```
