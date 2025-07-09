FROM ubuntu:22.04

ENV LANG=en_US.UTF-8
ENV DEBIAN_FRONTEND=noninteractive
ENV ROS_DISTRO=humble

# Set up locale
RUN apt update && apt install -y locales && \
    locale-gen en_US en_US.UTF-8 && \
    update-locale LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8

# Install essential utilities and LXDE desktop with better terminal support
RUN apt update && apt install -y \
    wget curl gnupg2 lsb-release sudo software-properties-common \
    python3-pip git \
    lxsession lxde-core lxde-common lxterminal xfce4-terminal xterm \
    x11vnc xvfb supervisor novnc websockify

# Install ROS tools using pip (compatible with ARM64)
RUN pip3 install -U colcon-common-extensions rosdep

# Add ROS 2 Humble sources
RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key | apt-key add - && \
    echo "deb http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" > /etc/apt/sources.list.d/ros2-latest.list

# Add Ignition Fortress source
RUN echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" > /etc/apt/sources.list.d/gazebo-stable.list && \
    wget http://packages.osrfoundation.org/gazebo.key -O - | apt-key add -

# Install ROS and TurtleBot-related packages
RUN apt update && apt install -y \
    ros-humble-ros-base \
    ros-humble-turtlebot4-simulator \
    ros-humble-turtlesim \
    ros-humble-irobot-create-nodes \
    ros-humble-teleop-twist-keyboard \
    ros-humble-turtlebot4-navigation \
    ros-humble-slam-toolbox \
    ros-humble-nav2-bringup \
    ros-humble-rviz2 \
    ros-dev-tools \
    ignition-fortress

# Initialize rosdep
RUN mkdir -p /etc/ros/rosdep/sources.list.d && rosdep init && rosdep update

# Clone TurtleBot 4 simulator
RUN mkdir -p /turtlebot4_ws/src
WORKDIR /turtlebot4_ws/src
RUN git clone -b humble https://github.com/turtlebot/turtlebot4_simulator.git

# Build workspace
WORKDIR /turtlebot4_ws
RUN /bin/bash -c "source /opt/ros/humble/setup.bash && rosdep install --from-path src -yi && colcon build --symlink-install"

# Add ROS environment setup to bashrc
RUN echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc && \
    echo "source /turtlebot4_ws/install/setup.bash" >> ~/.bashrc

# Copy supervisord configuration
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Expose ports for VNC and noVNC
EXPOSE 5900 6080

ENV IGN_GAZEBO_RESOURCE_PATH=/turtlebot4_ws/install/turtlebot4_ignition_bringup/share/turtlebot4_ignition_bringup/worlds

# Start everything with supervisord
CMD ["/usr/bin/supervisord"]

# Create Desktop folder
RUN mkdir -p /root/Desktop
# Create 3D simulator desktop shortcut
RUN echo '[Desktop Entry]\n\
Name=TurtleBot4 3D Simulation\n\
Exec=lxterminal -e "bash -c '\''source /opt/ros/humble/setup.bash && source /turtlebot4_ws/install/setup.bash && ros2 launch turtlebot4_ignition_bringup turtlebot4_ignition.launch.py world:=maze'\''"\n\
Icon=utilities-terminal\n\
Terminal=false\n\
Type=Application\n\
Categories=Application;' > /root/Desktop/start_3d_sim.desktop && \
    chmod +x /root/Desktop/start_3d_sim.desktop

# Create 2D simulator desktop shortcut
RUN echo '[Desktop Entry]\n\
Name=Turtlesim 2D\n\
Exec=lxterminal -e "bash -c '\''source /opt/ros/humble/setup.bash && ros2 run turtlesim turtlesim_node'\''"\n\
Icon=utilities-terminal\n\
Terminal=false\n\
Type=Application\n\
Categories=Application;' > /root/Desktop/start_2d_sim.desktop && \
    chmod +x /root/Desktop/start_2d_sim.desktop

# Create teleop control desktop shortcut
RUN echo '[Desktop Entry]\n\
Name=Keyboard Teleop 3D\n\
Exec=lxterminal -e "bash -c '\''source /opt/ros/humble/setup.bash && ros2 run teleop_twist_keyboard teleop_twist_keyboard '\''"\n\
Icon=utilities-terminal\n\
Terminal=false\n\
Type=Application\n\
Categories=Application;' > /root/Desktop/teleop_keyboard.desktop && \
    chmod +x /root/Desktop/teleop_keyboard.desktop

# Create teleop control desktop shortcut
RUN echo '[Desktop Entry]\n\
Name=Keyboard Teleop 2D\n\
Exec=lxterminal -e "bash -c '\''source /opt/ros/humble/setup.bash && ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -r /cmd_vel:=/turtle1/cmd_vel'\''"\n\
Icon=utilities-terminal\n\
Terminal=false\n\
Type=Application\n\
Categories=Application;' > /root/Desktop/teleop_keyboard.desktop && \
    chmod +x /root/Desktop/teleop_keyboard.desktop

# Create RViz2 desktop shortcut
RUN echo '[Desktop Entry]\n\
Name=LRViz2\n\
Exec=lxterminal -e "bash -c '\''source /opt/ros/humble/setup.bash && rviz2'\''"\n\
Icon=utilities-terminal\n\
Terminal=false\n\
Type=Application\n\
Categories=Application;' > /root/Desktop/rviz2.desktop && \
    chmod +x /root/Desktop/rviz2.desktop

# Set desktop wallpaper
RUN mkdir -p /root/.config/pcmanfm/LXDE && \
    echo "[*]\nwallpaper_mode=stretch\nwallpaper=/usr/share/backgrounds/DSC2943_by_kcpru.jpg" \
    > /root/.config/pcmanfm/LXDE/desktop-items-0.conf
