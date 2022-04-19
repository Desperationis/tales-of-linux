# SLAM with Turtlebot3
Using Nav2's SLAM mapping with Turtlebot3 was a pain in the butt due to this small bug. 

## The Issue
I was using [this tutorial](https://navigation.ros.org/tutorials/docs/navigation2_with_slam.html) to run navigation with SLAM mapping for the turtlebot3. However, for some weird reason, the turtlebot3 would always turn clockwise for half a second for every waypoint set. This happens even if the waypoint is directly in front of the robot. 

## The Solution
I tried many, many things. I reinstalled OpenCR firmware, switched the wiring of the motors (don't do this), but eventually I found the solution when I was trying to change the param file of the turtlebot3 navigation. 

When you run `ros2 launch nav2_bringup navigation_launch.py`, [you aren't using the param file of the turtle](https://github.com/ros-planning/navigation2/blob/main/nav2_bringup/launch/navigation_launch.py) [unlike in](https://github.com/ROBOTIS-GIT/turtlebot3/blob/galactic-devel/turtlebot3_navigation2/launch/navigation2.launch.py) `ros2 launch turtlebot3_navigation2 navigation2.launch.py map:=$HOME/map.yaml`. I found this out when I was changing every conceivable parameter in [burger.yaml](https://github.com/ROBOTIS-GIT/turtlebot3/blob/galactic-devel/turtlebot3_navigation2/param/burger.yaml), even deleting some, just to see if it did anything. What actually loads is [this file](https://github.com/ros-planning/navigation2_tutorials/blob/master/sam_bot_description/config/nav2_params.yaml), which has some major differences that lead to that weird behaviour.

After digging through tons of source code, I found [this file](https://github.com/ros-planning/navigation2/blob/main/nav2_bringup/launch/navigation_launch.py) that told me the exact parameter I needed to set for it to work. Thus, `ros2 launch nav2_bringup navigation_launch.py params_file:=[PATH_TO_BURGER_YAML]` is the CORRECT way to run the file and ALL other nav2 files. 

After using the correct param file, the costmap looked significally better (problem one had enourmous inflation) and the weird behaviour never appeared again. 
