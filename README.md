# Team FLIP IT Final Project: Pancake Manipulator Arm
This repository contains software developed for ME 495 Embedded Systems in Robotics course. Written and maintained by James Avtges, Anna Garverick, Jackson Levine, Andru Liu, and Sarah Ziselman. 

![team](pancake_pkg/img/team.jpg)

## Overview
The objective of this project is to command a Franka Emika Panda arm to pour pre-mixed pancake batter onto a griddle, flip the pancake using a modified spatula, and lift the pancake off the griddle onto a plate. This process is done entirely autonomously utilizing open-source computer vision and motion planning software packages.

![flip1](pancake_pkg/img/flip-1.gif)
**_NOTE:_** This video is x2.5 sped up

The `pancake_pkg` package contains the following nodes:
* `pancake_control` - This node is responsible for implementing the `MoveIt` motion planner on the Franka Emika Panda robot. It sends a series of commands to allow for path planning and execution in order to carry out the robot's desired functionality.
* `pancake_vision` - This node is responsible for image processing using data acquired from the RealSense camera. It allows the camera to gain information about the environment such as frame transformations, object poses, and more.

## Software Requirements
The following steps will need to be executed to fulfil the software requirements needed to run this project.

1. Make sure ROS packages are most recent and up-to-date
```
sudo apt update
sudo apt upgrade
```
2. Install pre-requisites packages for the Franka Emika Panda Robot
```
sudo apt install \
     ros-noetic-franka-ros\
     ros-noetic-moveit \
     ros-noetic-moveit-visual-tools \
     ros-noetic-panda-moveit-config
```
3. Add the `panda_moveit_config` package to the custom workspace
```
cd ~/custom_ws/src
git clone https://github.com/ros-planning/panda_moveit_config 
cd panda_moveit_config
git checkout c2eeb1f
cd ~/custom_ws
catkin_make
```
4. Install the RealSense SDK
```
# Install intel's keys
sudo apt-key adv --keyserver keyserver.ubuntu.com \
     --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE

# Add the apt repository
sudo add-apt-repository \
     "deb https://librealsense.intel.com/Debian/apt-repo $(lsb_release -cs) main"

# Update the package list
sudo apt update

# Install the packages
sudo apt install librealsense2-dkms librealsense2-utils \
 librealsense2-dev librealsense2-dbg librealsense2-gl-dev \
 ros-noetic-realsense2-camera ros-noetic-realsense2-description

# Install the python wrapper
pip3 install pyrealsense2
```
5. Install MoveIt
```
sudo apt install ros-noetic-moveit
```
6. Cloning this repository
```
mkdir -p ~/ws/src
cd ws/src
git clone https://github.com/ME495-EmbeddedSystems/final-project-group-5.git
cd ~/ws
catkin_make
source devel/setup.bash
```
Once the following software has been installed, the following packages are ready to be used!

## Hardware Requirements
This project requires the following hardware components:
* RealSense Camera (information on connecting the RealSense can be found [here](https://nu-msr.github.io/me495_site/realsense.html))
* Franka Emika Panda Robot (information on connecting to the Panda can be found [here](https://nu-msr.github.io/me495_site/franka.html))
* Non-stick griddle
* Modified heat-proof spatula
* Pancake Mix with water added
* Food storage bottle

## Usage
First, the hardware must be set up by connecting the RealSense camera (via USB cable) and the Franka Emika Panda arm (via Ethernet cable) to the user's computer and turning on the griddle to 375 degrees Fahrenheit. All april tags and objects must be appropriately placed in the work space. All observers must be outside the boundaries of the robot's workspace.

Once the hardware has been set up, the software is ready to be launched. Open a terminal and navigate to the workspace containing the `pancake_pkg`. Use the following command to launch the project,
```
source devel/setup.bash
roslaunch pancake_pkg make_pancakes.launch use_sim:=false
```
This command will launch the `pancake_control` and `pancake_vision` nodes and display the Franka Emika Panda robot in `MoveIt` using `Rviz`. 

## Configuration

Each of the three main functionalities of the project are implemented through a single ROS service `make_pancakes`. 

### Pour

![Pour](pancake_pkg/img/pour.gif)

The first step is to pour batter onto the heated griddle. This part of the service will command the robot to grab the food storage bottle containing the mixed pancake batter, flip the bottle spout-side down above the griddle, squeeze the bottle for twelve seconds allowing for pancake batter to be released, then flip the bottle spout-side up and place it back at its starting location.

### Bubble Detection

![Bubble Detection](pancake_pkg/img/bubble_detection.gif)

The robot will then wait for a signal to flip the pancake. Flip time is determined by computer vision counting the number of contours on the pancake and their rate of change. Once the number of bubbles are no longer increasing, the pancake is ready to be flipped. 

### Flip

![Flip](pancake_pkg/img/flip.gif)

This part of the service will command the robot to grab the modified heat-proof spatula, maneuver it to get the pancake situated on the spatula using a location determined by computer vision, flip the pancake back onto the griddle, then move the robot to its home posiiton.

### Lift

![Lift](pancake_pkg/img/lift.gif)

A 20 second wait time ensures the second side of the pancake is perfectly cooked and ready to be handed off to the hungry user. The last part of the service will command the robot to maneuver the modified heat-proof spatula to get the pancake situated on the spatula using a position determined by computer vision, then lift it off of the griddle and flip it onto a user's plate.

## Running the Service
Open a new terminal and run the following commands
```
cd ~/ws
source devel/setup.bash
rosservice call /make_pancakes
```
Upon successful completion of all the services, the user will have delicious, robot-made pancakes courtesy of the Franka Emika Panda robot and team FLIP IT. 

![flip-3](pancake_pkg/img/flip-3.gif)
**_NOTE:_** This video is x2.5 sped up

## Testing
This package also contains testing applications to ensure that ROS implementation of the position of objects and the Python package pose calculations are correct. 

Use a terminal to run the following commands depending on the test to be run. Use the first to run all tests; however, ROS testing will required connection to the robot. Use the second command to run the Python package calculations.
```
catkin_make run_tests
catkin_make run_tests_pancake_pkg_nosetests_test
```

## Future Works
For future works our team would be interested in further developing the autonomous functionality of the robot. This could include training the system's vision to be more precise, obtaining more information about the environment from the RealSense camera, improving the trajectory planning and execution of the robot's motion planner, etc. Our team would also like to explore increasing the capability of the robot by developing software to command it to pick up other objects (like whisks, plates, etc.) and perform other tasks like mixing batter, adding non-liquid ingredients, and creating different pancake shapes. Lastly, a more complicated development would be to train the robot to flip a pancake with just a fry pan and without the use of a spatula. This would require a more intensive approach that may include machine learning and a deeper understanding of the system's dynamics.


![chocolate chips](pancake_pkg/img/chocolate_chips.GIF)
**_NOTE:_** This video is x2.5 sped up

*Chocolate chips must be placed in the center of the pancake while the batter is still pouring to avoid disrupting the computer vision!*
