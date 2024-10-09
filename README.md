### Steps to Set Up the Multi-TurtleBot Teleoperation:

#### 1. Create a New ROS2 Package
Start by creating a new package in your ROS2 workspace. This package will hold the necessary launch files and parameters for multi-robot control.

```bash
$ cd ~/turtlebot3_ws/src
$ ros2 pkg create my_tb3_launcher
```

Now, create two directories: one for launch files and one for parameter files.

```bash
$ cd ~/turtlebot3_ws/src/my_tb3_launcher
$ mkdir launch
$ mkdir param
```

Create a new bringup launch file:

```bash
$ cd launch
$ touch my_tb3_bringup.launch.py
```

#### 2. Modify the Launch File
Copy the contents of `robot.launch.py` from the `turtlebot3_bringup` package and paste them into the newly created `my_tb3_bringup.launch.py` file.

For example:
```bash
$ cp ~/turtlebot3_ws/src/turtlebot3/turtlebot3_bringup/launch/robot.launch.py ~/turtlebot3_ws/src/my_tb3_launcher/launch/my_tb3_bringup.launch.py
```

Then, modify the launch file to set unique namespaces for each TurtleBot (e.g., `tb3_0`, `tb3_1`):

In `my_tb3_bringup.launch.py`:
```python
from launch import LaunchDescription
from launch_ros.actions import Node

def generate_launch_description():
    return LaunchDescription([
        Node(
            package='turtlebot3_bringup',
            executable='robot',
            namespace='tb3_0',  # Namespace for the first robot
            output='screen',
            parameters=[{'use_sim_time': False}],
        ),
        Node(
            package='turtlebot3_bringup',
            executable='robot',
            namespace='tb3_1',  # Namespace for the second robot
            output='screen',
            parameters=[{'use_sim_time': False}],
        )
    ])
```

#### 3. Modify the Parameter YAML File
You need to modify the parameter file so that each TurtleBot runs under a different namespace. 

Copy the `burger.yaml` file from `turtlebot3_bringup` and modify it:

```bash
$ cp ~/turtlebot3_ws/src/turtlebot3/turtlebot3_bringup/param/burger.yaml ~/turtlebot3_ws/src/my_tb3_launcher/param/
```

In the copied `burger.yaml`, add the `namespace` for each node:

```yaml
tb3_0:
  turtlebot3_node:
    ros__parameters:
      use_sim_time: false

tb3_1:
  turtlebot3_node:
    ros__parameters:
      use_sim_time: false
```

Modify your launch file to load these YAML files for each namespace.

#### 4. Modify the `CMakeLists.txt` File
Now, go to your `CMakeLists.txt` in the `my_tb3_launcher` package and add installation commands for the launch and param directories:

```bash
install(DIRECTORY launch/
  DESTINATION share/${PROJECT_NAME}/launch
)

install(DIRECTORY param/
  DESTINATION share/${PROJECT_NAME}/param
)
```

#### 5. Compile and Run!
Finally, compile your package and launch the nodes for both TurtleBots with their unique namespaces.

```bash
$ cd ~/turtlebot3_ws
$ colcon build
$ source install/setup.bash
$ ros2 launch my_tb3_launcher my_tb3_bringup.launch.py
```

### Visualizing the Data
You can visualize the pose and LIDAR scan from each TurtleBot in RViz or by echoing the respective topics:

```bash
$ ros2 topic echo /tb3_0/odom   # Echo odometry data for tb3_0
$ ros2 topic echo /tb3_1/odom   # Echo odometry data for tb3_1
```

You can also visualize the laser scan by running RViz and setting the appropriate namespaces.


### Acknowledgments

I would like to express my heartfelt gratitude to professors Jorge Sales Gil and Enric Cervera Mateu for their invaluable guidance and support throughout this project. Their expertise in Multi-Robot Systems has significantly enriched my understanding of the subject. I also appreciate the resources and collaborative environment provided by the University of Jaume I, which facilitated the successful completion of this project.
