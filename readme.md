# baxter_dqn 
### Work in progress


Year 4 project work
Title: Multi-Modal End-to-End Visuomotor Control Policies

Based on baxter_dqn_ros (https://github.com/powertj/baxter_dqn.git)

Reinforcement learning in a simulated environment for the control of Baxter robot manipulator.

`BaxterEnv.lua` interfaces with the Atari DQN to provide a custom environment conforming to the following [API](https://github.com/Kaixhin/rlenvs). Passes a resized 7x60x60 tensor (one RGB image from Baxter built-in camera, one RGB image from an external camera, and motor position information in the 4th channel) from the simulator into the DQN, and in return passes commands back to simulator.

A coloured sphere, cylinder or box is spawned at a random orientation at start and reset. The baxter robot attempts to navigate it's arm to pick up the object. Currently movement on the arm is limited to a rotation at the wrist and shoulder, as well as the ability to extend the reach while forcing the gripper to be facing downwards. 

An attempt to pickup the object results in termination, as unsuccessful attempts often throw the object out of reach. The success of the task is gauged by checking that the pose of the object is approxiamtely the pose of the end-effector at the end of the pickup action. A partial reward is given if the robot comes into contact with the object. At termination the environment is reset.

## Requirements
- ROS indigo w gazebo2
- [baxter simulator installation](http://sdk.rethinkrobotics.com/wiki/Simulator_Installation)
- Torch7
- torch-ros
- rgbd_launch
- qtlua (to use test.lua for network input validation)

## Installation
Clone [Atari](https://github.com/Kaixhin/Atari), place BaxterNet.lua and BaxterEnv.lua in Atari folder.

Place baxter_dqn_ros package in ros workspace alongside baxter simulator installation.

While in ros workspace, rebuild by running
```
source ./devel./setup.bash
catkin_make
catkin_make install
```
## Use
Launch baxter_gazebo with 
```
./baxter.sh sim
roslaunch baxter_gazebo baxter_world.launch
```
Once loaded, in a new terminal run
```
source ./devel/setup.bash
rosrun baxter_dqn_ros torch_control.py
```

Navigate to the Atari directory and run `th main.lua -env BaxterEnv -modelBody BaxterNet -doubleQ false -duel false -bootstraps 0 -PALpha 0 `

## Known issues
The origin of the objects is not always at their centre. This results in some variation in pose with random orientation. This means that it is sometimes not possible to pick up the object - this should be solved when increasing number of actions.

Lastest addition of end-effector to object distance based reward detection is still in process of developement.

## Acknowledgements
[Kai Arulkumaran](https://github.com/Kaixhin) for his assistance
[Thomas Power] (https://github.com/powertj) for his code
