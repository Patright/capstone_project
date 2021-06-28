# Capstone Project in Udacity's Self-Driving Car Nanodegree.

[//]: # (Image References)


[image1]: ./examples/project-ros-graph.png
[image2]: ./examples/waypoint-updater-ros-graph.png
[image3]: ./examples/dbw-node-ros-graph.png

## Goal    
For this project, I wrote ROS nodes to implement core functionality of the autonomous vehicle system, control, and waypoint following. I tested the code using a simulator that mimics the functionality on Carla.    

 
## System Architecture Diagram    

The following is a system architecture diagram showing the ROS nodes and topics used in the project.    

![alt text][image1]   

## Code Structure   

The ROS nodes can be found in the `https://github.com/Patright/capstone_project/tree/master/ros/src` directory.

### Waypoint Updater

`.../ros/src/waypoint_updater/`

This package contains the waypoint updater node: `waypoint_updater.py`. The purpose of this node is to update the target velocity property of each waypoint based on traffic light and obstacle detection data. This node will subscribe to the `/base_waypoints`, `/current_pose`, `/obstacle_waypoint`, and `/traffic_waypoint` topics, and publish a list of waypoints ahead of the car with target velocities to the `/final_waypoints` topic.   
![alt text][image2]   


### Twist Controller   

Carla is equipped with a drive-by-wire (dbw) system, meaning the throttle, brake, and steering have electronic control. This package contains the files that are responsible for control of the vehicle: the node `dbw_node.py` and the file `twist_controller.py`, along with a pid and lowpass filter that you can use in your implementation. The dbw_node subscribes to the `/current_velocity` topic along with the `/twist_cmd` topic to receive target linear and angular velocities. Additionally, this node will subscribe to `/vehicle/dbw_enabled`, which indicates if the car is under dbw or driver control. This node will publish throttle, brake, and steering commands to the `/vehicle/throttle_cmd`, `/vehicle/brake_cmd`, and `/vehicle/steering_cmd` topics.   

![alt text][image3]


### Helper Packages
   
`.../ros/src/styx/` contains a server for communicating with the simulator, and a bridge to translate and publish simulator messages to ROS topics.   

`.../ros/src/styx_msgs/` includes definitions of the custom ROS message types used in the project.   

`.../ros/src/waypoint_loader/` loads the static waypoint data and publishes to `/base_waypoints`.   

`.../ros/src/waypoint_follower/` contains code from Autoware which subscribes to `/final_waypoints` and publishes target vehicle linear and angular velocities in the form of twist commands to the `/twist_cmd topic`.   

## Project Development

At first I worked on the waypoint updater node `waypoint_updater.py`. It subscribes to `/base_waypoints` and `/current_pose` and publishes to `/final_waypoints`. Those final waypoints are used to guide the car on the track, the waypoint_follower node can now start publishing messages to the `/twist_cmd` topic. That input is prossesed in the drive by wire node `dbw_node.py`. Now the car is able to drive in the simulator but without paying attention to the traffic lights.   
The traffic light information (location and status "green", "yellow", "red") is given by `/vehicle/traffic_lights` and can directly be used in the ROS node `tl_detector.py` to publish it as waypoint indices.
Then I completed the waypoint updater node. I used `/traffic_waypoint` to change the waypoint target velocities before publishing to `/final_waypoints`.   
Now the car stops at red traffic lights and moves when they are green.
