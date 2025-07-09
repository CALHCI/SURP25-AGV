FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble

# Set up locale
RUN apt update && apt install -y locales &&     locale-gen en_US en_US.UTF-8 &&     update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8
ENV LANG en_US.UTF-8

# Install base tools
RUN apt update && apt install -y wget curl gnupg2 lsb-release software-properties-common sudo

# Add ROS 2 repository and key
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key | apt-key add - &&     echo "deb http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2-latest.list

# Add Gazebo (Ignition Fortress) repo and key
RUN echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" > /etc/apt/sources.list.d/gazebo-stable.list &&     wget http://packages.osrfoundation.org/gazebo.key -O - | apt-key add -

# Install required packages
RUN apt update && apt install -y \
    ros-humble-turtlebot4-simulator \
    ros-humble-irobot-create-nodes \
    ros-dev-tools \
    ignition-fortress \
    python3-colcon-common-extensions \
    python3-rosdep \
    git

# Initialize rosdep
RUN rosdep init && rosdep update

# Create workspace and clone turtlebot4_simulator
RUN mkdir -p /turtlebot4_ws/src
WORKDIR /turtlebot4_ws/src
RUN git clone -b humble https://github.com/turtlebot/turtlebot4_simulator.git

# Build the workspace
WORKDIR /turtlebot4_ws
RUN /bin/bash -c "source /opt/ros/humble/setup.bash && rosdep install --from-path src -yi && colcon build --symlink-install"

# Set up sourcing in shell
RUN echo 'source /opt/ros/humble/setup.bash' >> ~/.bashrc && \
    echo 'source /turtlebot4_ws/install/setup.bash' >> ~/.bashrc

# Default command
CMD ["bash"]
