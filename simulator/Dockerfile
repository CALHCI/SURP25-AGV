FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble

# Locale
RUN apt update && apt install -y locales &&     locale-gen en_US en_US.UTF-8 &&     update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG en_US.UTF-8

# ROS 2 and Ignition Fortress sources
RUN apt update && apt install -y     curl gnupg2 lsb-release wget software-properties-common

RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key | apt-key add - &&     echo "deb http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2-latest.list

RUN echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" > /etc/apt/sources.list.d/gazebo-stable.list &&     wget http://packages.osrfoundation.org/gazebo.key -O - | apt-key add -

RUN apt update && apt install -y     ros-humble-ros-base     ros-humble-turtlebot4-simulator     ros-humble-irobot-create-nodes     ros-dev-tools     ignition-fortress     colcon-common-extensions     git     x11vnc xvfb fluxbox supervisor

# Create and build workspace
RUN mkdir -p /turtlebot4_ws/src
WORKDIR /turtlebot4_ws/src
RUN git clone -b humble https://github.com/turtlebot/turtlebot4_simulator.git

WORKDIR /turtlebot4_ws
RUN rosdep init && rosdep update &&     rosdep install --from-path src -yi &&     . /opt/ros/humble/setup.sh &&     colcon build --symlink-install

# Source setup in bash
RUN echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc &&     echo "source /turtlebot4_ws/install/setup.bash" >> ~/.bashrc

# Copy supervisord config
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

EXPOSE 5901 6080

CMD ["/usr/bin/supervisord"]
