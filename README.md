# offboard_velocity_control(PID)
OFFBOARD control is dangerous.

If you are operating on a real vehicle be sure to have a way of gaining back manual control in case something goes wrong.

## Installation
```
git clone https://github.com/ducanh1699/offboard_control.git
```

## Getting started
1. Launch a quadrotor with px4 and mavros in gazebo 
```
roslaunch px4 mavros_posix_sitl.launch 
```

2. Run the offboard_velocity_control
```
rosrun offboard_control offb_node
rosrun offboard_control setmode_offb
```

3. Run detect Marker
```
rosrun offboard_control MarkerDetection.py
```

## PID Controller
### Basis theory

PID controller is a proportional–integral–derivative controller (PID controller or three-term controller) is a control loop mechanism employing feedback loop. 
![A block diagram of a PID controller in a feedback loop.](Picture1.png)

This based on the current position and target position to have velocity to control UAV. 
```
  x----------------------------x
current                      target
```
To understand clearly, you can refer to the wiki: https://en.wikipedia.org/wiki/PID_controller

And watch the video: https://www.youtube.com/watch?v=wkfEZmsQqiA&ab_channel=MATLAB

Necessary library include:
```
#include "offboard_control/pid_controller_base.h"
```
1. set-up PID coefficient
```
double kp = 0.4, ki = 0.0, kd = 3.4;
```

2. create pid command and pass the values above 
```
PidControllerBase pid_cmd(kp, ki, kd);
```
**Note: each pid_command have only use in one axis**

3. set-up max and min the velocity to fly UAV
```
pid_cmd.setUMax(max_v);
pid_cmd.setUMin(min_v);
```

## Add marker to gazebo
### Realsense gazebo plugin d435: [github](https://github.com/pal-robotics/realsense_gazebo_plugin)
1. git clone above link
```
mkdir -p ~/camera_ws/src
cd ~/camera_ws/src
git clone https://github.com/pal-robotics/realsense_gazebo_plugin
```

2. build package 
```
cd ..
catkin build
```
**Note: After catkin build will create file librealsense_gazebo_plugin.so**

3. copy file
```
sudo cp librealsense_gazebo_plugin.so /opt/ros/melodic/lib/
```
### Modify model of gazebo:
1. copy file
```
cd ~/catkin_ws
mv ./simulation/nested_tags_visual_marker <path PX4-Autopilot_clone>/Tools/sitl_gazebo/models
```

2. add to file world (e.g /Tools/sitl_gazebo/worlds/empty.world)
```
<world name="default">
  ...
  <include>
    <uri>model://nested_tags_visual_marker</uri>
    <pose>2 2 0.0 0.0 0.0 0.0</pose>
  </include>
  ...
</world>
```
