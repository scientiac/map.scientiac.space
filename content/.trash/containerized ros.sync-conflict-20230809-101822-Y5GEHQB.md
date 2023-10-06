---
title: "containerized ros"
date: 2023-07-24
tags:
- points
---

The Robot Operating System (ROS) is a set of software libraries and tools for building robot applications.

### OS Requirement

1. OS: Linux
2. Distro: Ubuntu/RHEL
3. Shell: Bash

## Installation Procedure:

### My OS Specification

1. OS: Linux
2. Distro: Fedora 38
3. Shell: Bash

### Used Tools

Packages used to run [ROS2](https://docs.ros.org/en/iron/The-ROS2-Project.html) on [distributions](https://docs.ros.org/en/rolling/Releases.html) other than the supported ones.

```
Distrobox
Podman
Ubuntu 22.04 Container
```

### Installation Procedure

1. Install `docker`, `podman`, and `distrobox`.
2. Run the following command to make a distro container for Ubuntu 22.04:
```bash
distrobox-create --name ros --image ubuntu:22.04
```
3. Log inside the created container:
```bash
distrobox enter ros
```
4. Install the ROS2 [distribution](https://docs.ros.org/en/rolling/Releases.html) following the [instruction](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html) for ubuntu 22.04 inside the container.

### Example Usage

1. Log inside the ubuntu container we just created by:
```bash
distrobox enter ros
```
2. In one ros container terminal, source the setup file and then run a C++ `talker`:
```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_cpp talker
```
3. In another ros container terminal, source the setup file and then run a Python `listener`:
```bash
source /opt/ros/humble/setup.bash
ros2 run demo_nodes_py listener
```

You should see the `talker` saying that it’s `Publishing` messages and the `listener` saying `I heard` those messages. This verifies both the C++ and Python APIs are working properly. yayyyy!

### Additional Setup

1. ROS requires the `setup.bash` file to be sourced every time before running it. This can be automated by adding the following line in your `.bashrc` .
```bash
source /opt/ros/humble/setup.bash
```
2. This might cause error on the host terminal because the `/opt/ros/humble/setup.bash` is only on the container.
```bash
# Error: bash: /opt/ros/humble/setup.bash: No such file or directory
```
3. To fix that add the following snippet at the end of your `.bashrc` file instead of the above mentioned line.
```bash
# ROS
if [ "$(grep Ubuntu /etc/issue | awk '{print $1}')" == "Ubuntu" ]; then
	source /opt/ros/humble/setup.bash
fi
```
4. The `clear` command might not work inside the container, to fix that run the following command on your terminal. (It adds `export TERM=linux` in your `.bash_profile`)
```bash
echo 'export TERM=linux' >> ~/.bash_profile
```
5. We need to log into the container every time we launch a new terminal so it's better to make a shortcut to log straight to the container terminal.
```bash
# For most terminals the shortcut can be set as:
<terminal-name> -e distrobox enter ros2
```
