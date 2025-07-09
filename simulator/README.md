# TurtleBot 4 Simulator in Docker 

This Docker setup provides a ready-to-use environment for simulating the TurtleBot 4 using ROS 2 Humble and Ignition Fortress.

## 🚀 Features

- Ubuntu 22.04 + ROS 2 Humble
- TurtleBot 4 Simulator (Ignition-based)
- GUI via VNC and noVNC
- ROS workspace with pre-built packages

## 🔧 Build the Docker Image

```bash
docker build -t turtlebot4_sim .
```

## ▶️ Run the Simulator with GUI (VNC)

```bash
docker run -it --rm -p 5901:5901 -p 6080:6080 --name turtlebot4 turtlebot4_sim
```

Then open your browser to:

- `http://localhost:6080` (web-based VNC viewer)
- or connect with a VNC client to `localhost:5901` (password: `vncpassword`)

## 🧠 What's Inside

The container:

- Installs all required ROS 2 and Ignition packages
- Clones and builds the TurtleBot 4 simulator
- Automatically launches the simulator in a virtual display
