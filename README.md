# ROS_Control
ROS Control 101


ROS Control is a set of packages and tools that allow you, basically, to send commands and communicate with the joints of your robot in order to be able to control them.

The main goal of this course is to teach you how to integrate this ros_control utility within a simulated environment, so you can use this tool to control the joints of your simulated robot.

1. Terminal #1
    roslaunch pi_robot_pkg pi_robot_control.launch

2. Terminal #2
    rostopic list | grep command
    
    rostopic pub -1 /pi_robot/head_tilt_joint_position_controller/command std_msgs/Float64 "data: 0.0"

    rostopic pub -1 /pi_robot/head_pan_joint_position_controller/command std_msgs/Float64 "data: 0.7"

Basically, we will learn about:

        Basics of ROS Control.
            
        How to configure ROS Control to work with your robot simulation.
            
        How to create a custom controller.

# The ros_control packages

The ros_control packages are a set of packages that include controller interfaces, controller managers, transmissions, hardware_interfaces, and the control_toolbox. All of these packages together allow you to interact and control the joints and actuators of your robot.

ROS controllers
The ros_control packages provide a set of controller plugins to interact in different ways with the joints of your robot. The list is as follows:

        - effort_controllers: Used when you want to send commands to an effort interface. This means that the joints you want to control accept effort commands
        - joint_effort_controller: The effort_controllers plugin accepts effort values as input.
        - joint_position_controller: The effort_controllers plugin accepts position values as input.
        - joint_velocity_controller: The effort_controllers plugin accepts velocity values as input.
        - position_controllers: Used when you want to send commands to a position interface. This means that the joints you want to control accept position commands.
        - joint_position_controller: The position_controllers plugin accepts only position values as input.
        - velocity_controllers: Used when you want to send commands to a velocity interface. This means, that the joints you want to control accept velocity commands.
        - joint_velocity_controller: The velocity_controllers plugin accepts only velocity values as input.
        - joint_state_controller: This plugin provides the state of the joints, publishing them into a topic called /joint_states. joint_state_controller

1. roscd pi_robot_pkg
2. cp config/pirobot_control.yaml /home/user/catkin_ws/src/ROS_Control


# Configure the Controllers

Configuring the URDF (Transmissions)
In order to be able to use the ros_control package in your simulation, you first need to add some things to the URDF file that defines your robot.

The first element you will need to define is the transmissions. The transmission elements are used to describe the relationship between a joint and an actuator. A transmission transforms efforts/flow variables such that their product - power - remains constant.

1. roscd rrbot_description/urdf/
2. cp rrbot.xacro /home/user/catkin_ws/src/ROS_Control

Explaining the file
As you have already seen in the file, the transmission tags have three main elements that you need to define:

        <type>: It defines the type of transmission that will be used. Currently, the only available option is transmission_interface/SimpleTransmission

        <joint>: It defines the joint connected to this transmission. The name of the joint must match a joint that is already defined for the robot (in its URDF files). You also need to define the hardware interface it will use, in this case, the EffortJointInterface.

        <actuator>: It defines the actuator connected to this transmission. Here you will define the hardware interface (as you did for the joint) and the mechanical reduction (this one is optional, but it is usually defined and set to 1).

Besides defining the transmission tags, you also need to add the gazebo ros control plugin to your URDF files. As we did before, follow the next exercise and its subsequent explanation in order to better understand how this works.

3. roscd rrbot_description/urdf/
4. cp rrbot.gazebo /home/user/catkin_ws/src/ROS_Control
5. Inside this <plugin> tag, there are some tags that you can define:

        <robotNamespace>: The namespace to be used by the instance of this plugin. By default, it will put the name of the robot that's in the URDF file.
        <controlPeriod>: The period of the controller update (in seconds). By default, it uses the Gazebo period. This tag is usually left to its default value, so it's not defined.
        <robotParam>: The location of the robot_description parameter on the parameter server. By default, it uses '/robot_description'. This tag is usually left to its default value, so it's not defined.
        <robotSimType>: The pluginlib name of a custom robot hardware interface to be used. By default, it uses 'DefaultRobotHWSim'.
6. In the default behavior, which is either not setting this tag or setting it to 'DefaultRobotHWSim', you will be able to use the following interfaces, which are the ones you saw in the previous chapter:

- JointStateInterface
- EffortJointInterface
- PositionJointInterface
- VelocityJointInterface
7. catkin_create_pkg my_robot_control rospy
8. create the launch folder and config folder
9. create a file named my_robot_control.yaml and put inside config folder
10. create a file named my_robot_control.launch and put inside launch folder
11. roslaunch my_robot_control my_robot_control.launch
12. Terminal #2
    rostopic list
13. Test the controler, Terminal #2
    
        rostopic pub -1 /rrbot/joint1_position_controller/command std_msgs/Float64 "data: 1.5"

        rostopic pub -1 /rrbot/joint2_position_controller/command std_msgs/Float64 "data: 1.0"
14. rosrun rqt_gui rqt_gui
15. On the 'Plugins' menu, select 'Topic' and 'Message Publisher.'
16. In the new window that will appear, choose the topic /rrbot/joint1_position_controller/command, which is the topic where we can send commands to the joint 1, and click the '+' button.
17. Now, enable the publisher in the below section by clicking on the check button that appears to the left of the topic name, and set the publish rate to 100.00.
18. Now, try to put the next expression in the 'expression' column: sin(i/100).
19. Next, add a plot display by going to the 'Plugins' menu, 'Visualization,' and 'Plot.' Add the topic /rrbot/joint1_position_controller/command and click the '+' button.
20. Finally, you are going to open the Dynamic Reconfigure tool, by going to 'Plugins,' 'Configuration,' and 'Dynamic Reconfigure.' This tool allows you to dynamically modify some configuration values of your robot. On the left panel, click on the 'Expand All' button and click on the pid option for the joint 1. Tune the pid values in order to make the two lines as close as possible


# Creating a Controller
1. Terminal #1
    catkin_create_pkg my_controller roscpp pluginlib controller_interface hardware_interface
2. Inside your package, create a new file named controller_plugins.xml
3. Updating the package.xml file
4. Updating the CMakeLists.txt file
5. catkin_make
6. Terminal #2
    rospack plugins --attrib=plugin controller_interface

    rospack plugins --attrib=plugin controller_interface | grep my_controller
7. Create config folder and the create my_controller.yaml inside the config folder
8. Create lanch folder and the then create my_controller.launch