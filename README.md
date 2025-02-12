# ROS Docker files

> ROS-in-a-box: a containerized version of various ROS nodes.

This repository provides `Dockerfile` files to run [ROS](https://ros.org) inside [Docker](https://www.docker.com/) containers:

## Base ROS image

[![](https://img.shields.io/docker/v/gramaziokohler/ros-base?sort=date)](https://hub.docker.com/r/gramaziokohler/ros-base)
[![](https://img.shields.io/docker/image-size/gramaziokohler/ros-base?sort=date)](https://microbadger.com/images/gramaziokohler/ros-base)

    $ docker pull gramaziokohler/ros-base

Contains ROS and tools to use it over websockers with `rosbridge-suite`.

## X11 NoVNC display container

[![](https://img.shields.io/docker/v/gramaziokohler/novnc?sort=date)](https://hub.docker.com/r/gramaziokohler/novnc)
[![](https://img.shields.io/docker/image-size/gramaziokohler/novnc?sort=date)](https://microbadger.com/images/gramaziokohler/novnc)

    $ docker pull gramaziokohler/novnc

Display X11 applications (e.g. `RViz`) from other containers directly in the browser.

## ROS + MoveIt! ABB

[![](https://img.shields.io/docker/v/gramaziokohler/ros-abb-planner?sort=date)](https://hub.docker.com/r/gramaziokohler/ros-abb-planner)
[![](https://img.shields.io/docker/image-size/gramaziokohler/ros-abb-planner?sort=date)](https://microbadger.com/images/gramaziokohler/ros-abb-planner)

    $ docker pull gramaziokohler/ros-abb-planner

Contains MoveIt! configured with the ABB packages.

## ROS + MoveIt! Universal Robots

[![](https://img.shields.io/docker/v/gramaziokohler/ros-ur-planner?sort=date)](https://hub.docker.com/r/gramaziokohler/ros-ur-planner)
[![](https://img.shields.io/docker/image-size/gramaziokohler/ros-ur-planner?sort=date)](https://microbadger.com/images/gramaziokohler/ros-ur-planner)

    $ docker pull gramaziokohler/ros-ur-planner

Contains MoveIt! configured with the Universal Robots packages.

## ROS + MoveIt! Franka Emika Panda

[![](https://img.shields.io/docker/v/gramaziokohler/ros-panda-planner?sort=date)](https://hub.docker.com/r/gramaziokohler/ros-panda-planner)
[![](https://img.shields.io/docker/image-size/gramaziokohler/ros-panda-planner?sort=date)](https://microbadger.com/images/gramaziokohler/ros-panda-planner)

    $ docker pull gramaziokohler/ros-panda-planner

Contains MoveIt! configured with the flagship MoveIt! robot: Franka Emika's
Panda.

## ROS + MoveIt! Kuka IIWA

[![](https://img.shields.io/docker/v/gramaziokohler/ros-kuka-iiwa-planner?sort=date)](https://hub.docker.com/r/gramaziokohler/ros-kuka-iiwa-planner)
[![](https://img.shields.io/docker/image-size/gramaziokohler/ros-kuka-iiwa-planner?sort=date)](https://microbadger.com/images/gramaziokohler/ros-kuka-iiwa-planner)

    $ docker pull gramaziokohler/ros-kuka-iiwa-planner

Contains MoveIt! configured with the Kuka IIWA 7 & 14 robots.

## ROS + MoveIt! for development

[![](https://img.shields.io/docker/v/gramaziokohler/ros-moveit-dev?sort=date)](https://hub.docker.com/r/gramaziokohler/ros-moveit-dev)
[![](https://img.shields.io/docker/image-size/gramaziokohler/ros-moveit-dev?sort=date)](https://microbadger.com/images/gramaziokohler/ros-moveit-dev)

    $ docker pull gramaziokohler/ros-moveit-dev

Contains MoveIt! with an empty catkin workspace to be mounted on the host for a fast development cycle.

## How to use

First, make sure [Docker](https://www.docker.com/) is installed on your system.

### Building the docker images

These images are built and published by Github Actions on every push.
If a push includes a tag, the images will also be tagged with it.

### Using the containers

#### Web UI example

1. Copy & Paste the following content into a `docker-compose.yml` file in your computer:

    ```yaml
    version: '2'
    services:
      ros-master:
        image: gramaziokohler/ros-base
        container_name: ros-master
        ports:
          - "11311:11311"
        command:
          - roscore

      ros-bridge:
        image: gramaziokohler/ros-panda-planner
        container_name: ros-bridge
        environment:
          - "ROS_HOSTNAME=ros-bridge"
          - "ROS_MASTER_URI=http://ros-master:11311"
        ports:
          - "9090:9090"
        depends_on:
          - ros-master
        command:
          - roslaunch
          - --wait
          - rosbridge_server
          - rosbridge_websocket.launch

      panda-demo:
        image: gramaziokohler/ros-panda-planner
        container_name: panda-demo
        environment:
          - ROS_HOSTNAME=panda-demo
          - ROS_MASTER_URI=http://ros-master:11311
          - DISPLAY=gui:0.0
        depends_on:
          - ros-master
          - gui
        command:
          - roslaunch
          - --wait
          - panda_moveit_config
          - demo.launch

      gui:
        image: gramaziokohler/novnc:latest
        ports:
          - "8080:8080"
    ```

2. Start it all up with:

       $ docker-compose up -d

3. Open the following URL in your browser:

       http://localhost:8080/vnc.html?resize=scale&autoconnect=true

![](rviz.png)

#### More examples

Check the [examples](examples) folder for several examples of `docker-compose` files.

## Notes

These images are maintained by Gramazio Kohler Research
[@gramaziokohler](https://github.com/gramaziokohler>)

They are used by the [COMPAS FAB](https://gramaziokohler.github.io/compas_fab) framework
to provide a [containerized ROS backend for planning and execution](https://gramaziokohler.github.io/compas_fab/latest/backends/ros.html#ros-on-docker-1).

## Credits

These docker images are only possible thanks to the huge contribution of the ROS and ROS-I community. Besides ROS itself, the following open source projects are built and included in them:

- [MoveIt: ROS Motion Planning Framework](https://github.com/ros-planning/moveit)
- [ROS Bridge Suite](https://github.com/RobotWebTools/rosbridge_suite/)
- [ROS Sharp file server](https://github.com/siemens/ros-sharp/tree/master/ROS/file_server)
- [ROS-I ABB & ABB Experimental](https://github.com/ros-industrial/abb)
- [ROS-I UR](https://github.com/ros-industrial/universal_robot)
- [ROS-I UR Modern Driver](https://github.com/ros-industrial/ur_modern_driver)
- [ROS Panda planning](https://github.com/ros-planning/panda_moveit_config)
- [ROS IIWA Stack](https://github.com/IFL-CAMP/iiwa_stack)
- [NoVNC client](https://github.com/novnc/noVNC)
