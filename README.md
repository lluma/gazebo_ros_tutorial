# Ros with Gazebo

## Table of Contents

[TOC]

## Beginners Guide

If you are a total beginner to this, start here!

1. Prepare your environment, fisrt make sure you have install the following compenents [ROS]( http://wiki.ros.org/kinetic/Installation/Ubuntu) (We use Kinetic distro), [Gazebo](http://gazebosim.org/tutorials?tut=install_ubuntu&cat=install) on Ubuntu 16.04.
2. Install the extra package for [gazebo_ros](http://gazebosim.org/tutorials?tut=ros_installing).
3. After your environment has prepared, then you can next build up your ros workspace. Please reference to our custom ros packages -> [ros-control-driver](https://github.com/lluma/gazebo_ros_env).

Testing on well installation
---

Check the below command whether work successfully.
```bash
cd ~/catkin_ws/src
roslaunch tm_gazebo testing_tm700.launch
```
If everything goes no error, the final result will look like this figure.
![](https://i.imgur.com/c2idr0w.png)

World : Build up your own world
===
You can create your own world by generating a .world file in ${your ROS workspace}/src/tm_gazebo/worlds

In a .world file, the architecture of if will be like to gazebo sdf file. You can reference to testing_world.world .
```xml
<?xml version="1.0" ?>
<sdf version="1.4" >
  <world name="default">

<!--    Add A New Model -->
<!--    <include>-->
<!--      <uri>model://${MODEL_NAME}</uri>-->
<!--      <name>${MODEL_DISPLAY_NAME}</name>-->
<!--      <pose>${POSE: tx, ty, tz, rx, ry, rz}<pose>-->
<!--    </include>-->

    <include>
      <uri>model://cafe_table</uri>
      <name>cafe_table</name>
      <pose>0 0 0 0 0 0</pose>
    </include>

<!--    Add A New Sensor-->
<!--    This new sensor will be released later...-->
    
    <!-- Camera -->
    <model name="camera">
      <static>true</static>
      <pose>-1 0 2 0 1 0</pose>
      <link name="link">
        <visual name="visual">
          <geometry>
            <box>
              <size>0.1 0.1 0.1</size>
            </box>
          </geometry>
        </visual>
        
        <sensor name="my_camera" type="camera">
          <camera>
            <save enabled="true">
              <path>/tmp/gazebo_camera_images</path>
            </save>
            <image>
              <width>1920</width>
              <height>1080</height>
            </image>
            <clip>
              <near>0.1</near>
              <far>100</far>
            </clip>
          </camera>
<!--	  <plugin name="camera_dump" filename="libcamera_dump.so"/>-->
          <always_on>1</always_on>
          <update_rate>30</update_rate>
        </sensor>
      </link>
    </model>
  </world>
</sdf>
```

Robot : Control your own robot
===


Sensor : Camera
===

## Camera
Use a normal camera in gazebo, below, we create a camera as a model with a visuable box and a virtual sensor. 

As you want to visualize the camera in gazebo, then you need to add `<visual>` tag in your model. And make the visuable object to have ability of sensoring the view it has seen, we need `<sensor>` to construct the configurations for camera.

Other tags like,
- In `<model>` :
  - `<static>` can specified the camera is non-movable or not.
  - `<pose>` can specified the initial pose of the camera.
- In `<sensor>` :
  - `<camera>` can specified some camera features. 
    - Its `<clip>`, is always power on `<always_on>`
    - The rate of capturing the view `<update_rate>`
  
```xml
    <model name="camera">
      <static>true</static>
      <pose>-1 0 2 0 1 0</pose>
      <link name="link">
        <visual name="visual">
          <geometry>
            <box>
              <size>0.1 0.1 0.1</size>
            </box>
          </geometry>
        </visual>
        
        <sensor name="my_camera" type="camera">
          <camera>
            <save enabled="true">
              <path>/tmp/gazebo_camera_images</path>
            </save>
            <image>
              <width>1920</width>
              <height>1080</height>
            </image>
            <clip>
              <near>0.1</near>
              <far>100</far>
            </clip>
          </camera>
<!--	  <plugin name="camera_dump" filename="libcamera_dump.so"/>-->
          <always_on>1</always_on>
          <update_rate>30</update_rate>
        </sensor>
      </link>
    </model>
```

In details, due to this lines, we can dump the images that the camera captures at 30 frames per second and the resolution is 1920 x 1080 specified by the `<width>` and the `<height>` in the `<image>` tag.

```xml
    <save enabled="true">
      <path>/tmp/gazebo_camera_images</path>
    </save>
    <image>
      <width>1920</width>
      <height>1080</height>
    </image>
         
```

Moreover, the `<plugin>` tag gives an another way to dump the images by c++ script. You can change the above config on camera like this.

```xml
    <model name="camera">
      <static>true</static>
      <pose>-1 0 2 0 1 0</pose>
      <link name="link">
        <visual name="visual">
          <geometry>
            <box>
              <size>0.1 0.1 0.1</size>
            </box>
          </geometry>
        </visual>
        
        <sensor name="my_camera" type="camera">
          <camera>
            <image>
              <width>1920</width>
              <height>1080</height>
            </image>
            <clip>
              <near>0.1</near>
              <far>100</far>
            </clip>
          </camera>
	  <plugin name="camera_dump" filename="libcamera_dump.so"/>
          <always_on>1</always_on>
          <update_rate>30</update_rate>
        </sensor>
      </link>
    </model>
```

## Logical Camera
