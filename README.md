# echo-bot
## Project Overview
This GitHub project focuses on building a bot using ROS2 and simulate it in Gazebo. The goal of this project is to successfully build a 2D Map of a space using SLAM(Simultaneous Localization and Mapping) and navigate autonomusly using NAV2. Furthermore, the bot can also be controlled by a joystick to navigate manually. 

## Table of Contents
- Pre-requisites
- Robot 3D Model
- Simulating Robot in Gazebo
- Sensor Integration
- Control using ROS2 Control
- Navigation using Joystick
- Mapping using SLAM
- Navigation using NAV2
- Conclusion
- Future Scope
- How to Use the code
- Acknowledgement
## Pre-requisites
-  Install ROS - [Read here](http://wiki.ros.org/ROS/Installation)
-  Create Workspace - [Read here](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Creating-A-Workspace/Creating-A-Workspace.html)
-  Install XACRO and joint state publisher gui
```sh
$ sudo apt install ros-<distro-name>-xacro ros-<distro-name>-joint-state-publisher-gui
```
- Install Gazebo
```sh
$ sudo apt install ros-<distro-name>-gazebo-ros-pkgs
```
- Install ROS2 Control
```sh
$ sudo apt install ros-<distro-name>-ros2-control ros-<distro-name>-ros2-controllers ros-<distro-name>-gazebo-ros2-controller
```
- Install SLAM toolbox
```sh
$ sudo apt install ros-<distro-name>-slam-toolbox
```
- Install NAV2 
```sh
$ sudo apt install ros-<distro-name>-navigation2 sudo apt install ros-<distro-name>-nav2-bringup sudo apt install ros-<distro-name>-twist-mux
```

## Robot 3D Model
Create a differential drive robot model using multiple configuration files in the URDF (Unified Robot Description Format) and processing them together using Xacro, a macro language for URDF. The final URDF file generated is then passed into Robot State Publisher, facilitating the availability of the URDF data in the `robot_description` topic. To manipulate the robot's joints, Robot State Publisher expects input from the `joint_state` topic. In the initial phase of the project, the Joint State Publisher GUI is utilized to provide simulated joint values to the `joint_state` topic.<br />

![Screenshot from 2024-03-05 16-36-56](https://github.com/sghatak5/echo-bot/assets/149153121/f445f8d7-c301-4615-a743-13796c73a220)

## Simulating Robot in Gazebo
 To simulate the robot in the Gazebo environment using ROS (Robot Operating System) a simulation is initiated using the `launch_sim.launch.py` script, which orchestrates the launch of essential components including Robot State Publisher, Gazebo simulator, and the robot model spawned within Gazebo. To enable locomotion control, a Gazebo control plugin is utilized to publish values to the left and rigth wheel transform, while user input from the keyboard is translated into velocity commands published to the `cmd_vel` topic.<br />

![Untitleddesign-ezgif com-video-to-gif-converter](https://github.com/sghatak5/echo-bot/assets/149153121/69c11f11-90a7-495d-87ac-c2dad680c52e)

## Sensor Integration
### LiDAR (Light Detection and Range)
 To intigrate a 2D LiDAR, `lidar.xacro` file is used. LiDAR publish messages to `sensor_msgs/LaserScan`
 
 ![Untitleddesign1-ezgif com-video-to-gif-converter](https://github.com/sghatak5/echo-bot/assets/149153121/a345bf0c-aced-4a7f-92d0-9b97d14416ef)

### Camera
 To integrate a camera, `camera.xacro` file is used. Camera publish messages to `sensor_msgs/Images`

 ![Untitleddesign2-ezgif com-video-to-gif-converter](https://github.com/sghatak5/echo-bot/assets/149153121/0c3c1518-f2fe-4b02-8883-821109aeda55)

### Depth Camera
 To integrate a camera, `depth_camera.xacro` file is used.

 ![Untitleddesign3-ezgif com-video-to-gif-converter](https://github.com/sghatak5/echo-bot/assets/149153121/0de99781-9662-4436-a8eb-240d50dd30c6)

## Control using ROS2 Control
 ROS2 Control is a framework designed for real-time control of robots utilizing the Robot Operating System 2 (ROS2). Its primary objective is to streamline the integration of new hardware and address certain limitations present in previous versions. In the context of simulation environments like Gazebo, the integration of ROS2 Control is facilitated through the utilization of the `ros2_control.xacro file`. However, it's noteworthy that Gazebo incorporates its own controller manager, which operates using the `my_controller.yaml` configuration file. To deploy and utilize controllers within Gazebo, a `spawner.py` script is employed within the launch file.

## Navigation using Joystick
 Teleoperation is a method of operating a system remotely, often involving the transmission of command signals and the reception of feedback from the system. In this project, teleoperation specifically involves the control of velocity using a joystick interface. The joystick driver publishes messages to the `sensor_msgs/joy` topic. To facilitate this process, a launch file named  `joystick.launch.py` and parameters file `joystick.yaml` are created specifically for configuring the joystick interface. <br />
**Note:** Navigation using Joystick is an optional feature, and if using a keyboard instead, this functionality can be skipped. 

## Mapping using SLAM
Simultaneous Localization and Mapping (SLAM) is a computational challenge focused on generating or updating a map of an unfamiliar environment while concurrently determining the location of an agent within it. In this particular project, Grid SLAM employing a 2D LiDAR sensor is implemented in an Online Asynchronous mode. Parameters essential for this operation are provided through the `mapper_param_online_asny.yaml` configuration file. Within this configuration file, the mode can be specified as either mapping or localization, thereby defining the primary objective of the SLAM process. Upon configuring the parameters, the SLAM system can be executed by running the `launch_sim.launch.py` launch file:
 ```sh
ros2 launch slam_toolbox online_async_launch.py slam_params_file:=/path/to/your/mapper_params_online_async.yaml use_sim_time:=true
```
## Navigation using NAV2
Navigation in robotics entails the planning and execution of a safe trajectory from an initial pose to a target pose, representing a fundamental autonomous capability of robots. In the context of the echo-bot project, navigation is achieved using the NAV2 stack, enabling the robot to autonomously navigate to a specified target location. The navigation system relies on SLAM (Simultaneous Localization and Mapping) for accurate robot position estimation. A static map generated from SLAM, combined with data from LiDAR, is utilized to update the map dynamically, enabling the robot to respond to live changes in its environment. This dynamic map, known as the cost map, assigns costs to different areas of the environment, with high costs indicating areas to be avoided and low costs indicating safe spaces for navigation. Execution of the NAV2 system involves launching the `launch_sim.launch.py` launch file and the SLAM toolbox, followed by 
```sh
ros2 launch nav2_bringup navigation_launch.py use_sim_time:=true
```

## Conclusion
In conclusion, this GitHub project has successfully demonstrated the integration of ROS2 and Gazebo to create a versatile robot capable of performing various tasks. The primary objective was to develop a 2D mapping solution using SLAM (Simultaneous Localization and Mapping) and achieve autonomous navigation using NAV2. Through meticulous implementation and configuration, the robot effectively builds and updates a map of its environment while autonomously navigating to predefined destinations. Additionally, the project includes a manual control feature facilitated by a joystick interface, allowing for manual navigation when desired. Overall, this project showcases the capabilities of ROS2 and Gazebo in developing sophisticated robotic systems capable of both autonomous and manual operation in simulated environments.

## Future Scope
The next phase of this project entails the implementation of object tracking functionality followed by autonomous navigation through tracked objects. Incorporating object tracking capabilities will enable the robot to identify and track objects of interest within its environment, thus enhancing its situational awareness. By integrating this functionality with the existing navigation system, the robot will be able to dynamically adjust its trajectory to navigate through or around tracked objects while maintaining its intended path. This advancement will significantly augment the robot's capabilities, allowing it to navigate more effectively in dynamic environments with moving obstacles or objects of interest. Overall, this future development will further enhance the versatility and adaptability of the robot, making it more proficient in real-world scenarios requiring complex navigation and interaction capabilities. 

## Acknowledgement
I acknowledge Articulated Robotics for detailed description and explanation on different topics used in this project. 
