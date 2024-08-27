# [ROS1-notes](https://wiki.ros.org/ROS/Tutorials)

## [Navigating the ROS Filesystem](https://wiki.ros.org/ROS/Tutorials/NavigatingTheFilesystem)

- `rospack find [package_name]` - find the path of installed ros package in local fs
- `roscd [package-or-stack]` - change directory directly to a package or a stack
- `echo $ROS_PACKAGE_PATH` - to see the ros package path, other tools only find packages within the directories of this path
- `roscd log` - go to log folder, yields an error if no ros program is run yet
- `rosls <package-or-stack>[/subdir]` - ls directly in a package by name rather than by absolute path

## [Create a Catkin Workspace](https://wiki.ros.org/catkin/Tutorials/create_a_workspace)

~~~bash
source /opt/ros/noetic/setup.bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/
catkin_make               # only for ROS noetic
echo "source devel/setup.bash" > system_bring_up.sh && chmod +xrw ./system_bring_up.sh
./system_bring_up.sh
~~~

## [Creating a ROS Package](https://wiki.ros.org/ROS/Tutorials/CreatingPackage)

Any catkin package has to contain CMakeLists.txt and package.xml. Simple workspace layout:
~~~bash
workspace_folder/             --- WORKSPACE
    src/                      --- SOURCE SPACE
        CMakeLists.txt        --- 'Toplevel' CMake file, provided by catkin
        package_1/
            CMakeLists.txt    --- CMakeLists.txt file for package_1
            package.xml       --- Package manifest for package_1
        ...
        package_n/
            CMakeLists.txt    --- CMakeLists.txt file for package_n
            package.xml       --- Package manifest for package_n
~~~
Create a catkin package:
~~~bash
cd ~/catkin_ws/src
catkin_create_pkg <package_name> [depend1] [depend2] [depend3]    # create package with dependencies
cd ~/catkin_ws
catkin_make          # build the environment
source devel/setup.bash
~~~
- `rospack depends1 <package-name>` - list direct dependencies
- `rospack depends1 <package-name-dependency>` - list indirect dependencies
- `rospack depends <package-name>` - list all dependencies

## [Building a ROS Package](https://wiki.ros.org/ROS/Tutorials/BuildingPackages)

~~~bash
cd ~/catkin_ws/
catkin_make
catkin_make install    # (optional)
~~~

## [ROS Nodes](https://wiki.ros.org/ROS/Tutorials/UnderstandingNodes)
#### Concepts:
- Nodes: A node is an executable that uses ROS to communicate with other nodeos.
- Messages: ROS data type used when subscribing or publishing to a topic.
- Topics: Nodes can publish messages to a topic as well as subscribe to a topic to receive messages
- Master: Name service for ROS (i.e. helps nodes find each other)
- rosout: ROS equivalent of stdout/stderr
- roscore: Master + rosout + parameter server
#### Commands:
- `roscore` - start master node
- `rosnode list` - list running nodes
- `rosnode info /<node-name>` - get info about running node
- `rosrun [package_name] [node_name]` - run a node within a package
- `rosrun [package_name] [node_name] __name:=<new_name>` - assign a new name to node
- `rosnode cleanup` - clean the rosnode list from nodes closed through `ctrl-c`
- `rosnode ping [node_name]` - test if node is up (node closed through `ctrl-c` doesn't respond)

## [ROS Topics](https://wiki.ros.org/ROS/Tutorials/UnderstandingTopics)
- `rosrun rqt_graph rqt_graph` - visualize nodes and topics, nodes are highlighted in blue and green colors, topics are highlighted in red color
- `rostopic -h` - help option to get the available sub-commands for rostopic
- `rostopic echo [topic]` - show the data published on a topic
- `rostopic list -v` - show the list of topics in verbose mode (includes number of publishers and subscribers in each topic)
- `rostopic type [topic]` - show the message type of the topic
- `rosmsg show [message_type]` - see the details of the message type
- `rostopic pub [topic] [msg_type] [args]` - publish data onto a topic. Example:
    - `rostopic pub -1 /turtle/cmd_vel geometry_msgs/Twist -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, 1.8]'` - publish 1 message. `--` needed to tell that following are not options, mainly used when the following arguments contain dash '-'.
    - `rostopic pub /turtle/cmd_vel geometry_msgs/Twist -r 1 -- '[2.0, 0.0, 0.0]' '[0.0, 0.0, -1.8]'` - publish commands at a rate of 1 Hz onto the topic.
- `rostopic hz [topic]` - see the rate of data being published
- `rosrun rqt_plot rqt_plot` - plot the data being published on topics
## [ROS Services and Parameters](https://wiki.ros.org/ROS/Tutorials/UnderstandingServicesParams)
- `rosservice list` - print information about active services
- `rosservice call [service] [args]` - call the service with the provided args
- `rosservice type [service]` - print the service type
- `rosservice find [service_type]` - find services by service type
- `rosservice uri` - print service ROSRPC uri

rossrv is a command-line tool for displaying information about ROS Service types.
- `rossrv show` - show service description
- `rossrv list` - list all services
- `rossrv package` - list services in a package
- `rossrv packages` - list packages that contain services
- `rssrv <command> -h` - for more detailed usage

rosparam allows you to store and manipulate data on the ROS Parameter Server
- `rosparam set [param_name] [value]` - set parameter, have to call service `/clear` for change to take effect
- `rosparam get [param_name]` - get parameter, replace param_name with `/` to get all parameters in the server
- `rosparam load [file_name] [namespace]` - load parameters from file, namespace optional
- `rosparam dump [file_name] [namespace]` - dump parameters to file, namespace optional


