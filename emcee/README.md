<a href="http://www.youtube.com/watch?feature=player_embedded&v=CdpFUFFlBOw
" target="_blank"><img src="http://img.youtube.com/vi/CdpFUFFlBOw/0.jpg" 
alt="youtube demo" width="240" height="180" border="10" /></a>



# emcee_description
The __Package overview__ gives a brief description of what everything is, so that you can orient yourself and start to learn how it works.  A lot of this is launch files.

[See below](#Launching) for how to launch stuff.
NOTE: Gazebo does not run well on VMs. It also crashes a lot on some computers. It is best with an NVIDIA graphics card.  If it crashes a lot, it sometimes helps to without the gui (gui:=0) and then `gzclient` in another terminal to see what is going on; if you don't need to the Gazebo gui, you can use rviz and an *image* visualization to see 1st person view.

# Dependencies
You may need to run these commands to get things going.

``` bash
sudo apt-get install ros-kinetic-gazebo-ros-pkgs ros-kinetic-gazebo-ros-control
sudo apt-get install ros-kinetic-joint-state-controller
sudo apt-get install ros-kinetic-effort-controllers
sudo apt-get install ros-kinetic-diff-drive-controller
sudo apt-get install ros-kinetic-hector-gazebo-plugins
```


# Package overview
emcee_control and emcee_description are key for running the sim.

## emcee_control
This contains controllers that drive the robot motors and actuators.  We are using a diff_drive_controller for the wheels and 3 position controllers for sled,arms,bucket.  The nice thing about these is that you only have to post a simple message and it tells the simulation how to move the robot (e.g. you send diff_drive_controller a Twist message to /cmd_vel topic).  See `joy_teleop.cpp` in src for an example of publishing to the controllers.  Google ros_control for more information on how this works.

### config directory
This contains files that are loaded by the launch files in this directory.  the important ones are the `emcee_control.yaml` and `emcee_localization.yaml`. Read them for more info.

A few of them were optional, like the ps3 and xbox stuff. These just make it really easy to change what controller you are using when you launch (see `joy_teleop.launch`).

## emcee_description
This contains files the meshes (3D models of parts), urdf, and gazebo world files.

## emcee_moveit_generated
This package was generated by MoveIt!, a motion planning library.  I have not done anything with it yet, but if anyone finds anything cool to use it for, let me know.


## emcee_navigation
This package is implementing the nagivation stack.  I started following the [tutorials for setting it up](http://wiki.ros.org/navigation/Tutorials/RobotSetup), but have not yet finished.

In the `src/` directory, there is code that can move and rotate the robot a given distance or angle, using odometry data.  I figure something like this is how we will move the robot in the competition.  We might use move_base though.



# Launching
To view the urdf in RViz, use the display.launch
```bash
roslaunch emcee_description display.launch
```

To view in Gazebo
```bash
roslaunch emcee_description gazebo.launch
```

To drive the robot with keyboard
```bash
roslaunch emcee_control drive.launch
```
Focus the terminal window and use the arrow keys.


To drive with Xbox (or PS3) controllers.  Should be the same controls as the real robot.  I think I messed up some direction though, so please fit it if you can.
```bash
roslaunch emcee_control joy_teleop.launch
```
For a different input (default js0)
```bash
roslaunch emcee_control joy_teleop.launch input:=js1
```
For ps3 (default xbox)
```bash
roslaunch emcee_control joy_teleop.launch controller:=ps3
```



## Tweaking start position of the urdf in Gazebo
This command puts the robot in the left bin, right quadrant, facing right.
```bash
rostopic pub -r 20 /gaze/set_model_state gazebo_msgs/ModelState '{model_name: emcee, pose: { position: { x: -2.1, y: 2.8, z: 2 }, orientation: {x: 0, y: 0, z: 0, w: 1 } }, twist: { linear: { x: 0, y: 0, z: 0 }, angular: { x: 0, y: 0, z: 0}  }, reference_frame: world }'
```
