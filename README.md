# ros_overlay_on_gentoo_prefix
ROS Kinetic: [![Build Status](https://dev.azure.com/12719821/12719821/_apis/build/status/awesomebytes.ros_overlay_on_gentoo_prefix_32b?branchName=master)](https://dev.azure.com/12719821/12719821/_build/latest?definitionId=6)
ROS Melodic: [![Build Status](https://dev.azure.com/12719821/12719821/_apis/build/status/awesomebytes.ros_overlay_on_gentoo_prefix_32b?branchName=melodic)](https://dev.azure.com/12719821/12719821/_build/latest?definitionId=6)

This project answers the following questions:

* Do you have a system (computer or robot) where you can't update the software (ROS or its dependencies) anymore?
* Do you have a system where you lack permissions to install or update software?
* Do you want a system where you don't depend on any software of the platform itself but you can still use the platform software if you want?

**Gentoo Prefix** provides an Operating System in userspace (you can place it in any folder that you have write permissions) which requires no root privileges to install or run software.a

**ros-overlay** provides recipes to build different ROS versions on Gentoo systems.

This project builds upon both by building **nightly** binary releases of a full Gentoo Prefix + ROS (Kinetic & Melodic) on x86 (32bitS).

Building ROS over Gentoo Prefix (on `/tmp/gentoo` for x86 over a Docker image of Ubuntu 16.04 32bits) thanks to [ros-overlay](https://github.com/ros/ros-overlay).

This is a project closely related to [Gentoo Prefix CI 32b](https://github.com/awesomebytes/gentoo_prefix_ci_32b).

Builds page on Azure Pipelines: https://dev.azure.com/12719821/12719821/_build?definitionId=6

Ready-to-use releases: https://github.com/awesomebytes/ros_overlay_on_gentoo_prefix_32b/releases

Ready-to-use built Dockers:
* ros-kinetic/ros_base: `docker pull awesomebytes/roogp_32b_ros_kinetic_ros_base`
* ros-kinetic/desktop: `docker pull awesomebytes/roogp_32b_ros_kinetic_desktop_3`
* ros-melodic/ros_base: `docker pull awesomebytes/roogp_32b_ros_melodic_ros_base`
* ros-melodic/desktop: `docker pull awesomebytes/roogp_32b_ros_melodic_desktop_3`

You can get into the dockers by doing: `docker run -it awesomebytes/IMAGE_NAME /bin/bash`

You'll find

# Currently building

* ros-kinetic/ros_base
* ros-kinetic/desktop
* ros-melodic/ros_base
* ros-melodic/desktop

# Try it

Go to https://github.com/awesomebytes/ros_overlay_on_gentoo_prefix/releases and download the latest release of your choice (ros-kinetic/ros_base is 2GB~), it's divided in 1GB parts (GitHub Releases accepts files up to 2GB only) that are named like `gentoo_on_tmp_with_ros-kinetic_ros_base-amd64_2020-03-08T21at41plus00at00.tar.gz.part-00`.

Put the parts together and extract (4.4GB~):
```bash
# Put the .part-XX files together
cat gentoo_on_tmp* > gentoo_on_tmp.tar.lzma
# Extract the 'gentoo' folder that contains the OS
tar xvf gentoo_on_tmp.tar.lzma
# Probably delete the intermediate files
rm gentoo_on_tmp*
# Just enter the Gentoo Prefix environment
./gentoo/startprefix
# To use ROS you'll need to source ROS and setup a couple of environment variables
source /tmp/gentoo/opt/ros/<ROS_VERSION_HERE>/setup.bash
export CATKIN_PREFIX_PATH=/tmp/gentoo/opt/ros/<ROS_VERSION_HERE>
export ROS_IP=<YOUR_IP_HERE>
```

ros-kinetic/desktop is ~4GB, extracted ~6GB.

The `startprefix` script is used to drop the user in a shell in the Gentoo Prefix environment. It creates a softlink to `/tmp/gentoo` if it does not exist in order to work.

# Work in progress & Future work
Contributions more than welcome!

* ros-kinetic/desktop_full: it worked at some point, needs some love to bring it back to life.
* ros2: should work easily, haven't put the time.
* Support for MacOS: Azure Pipelines has MacOS VMs to use, but I have never used MacOS so I've never put the time.
* Support for ARM: Need an emulator or a Raspberry Pi or similar setup somewhere to use as buildfarm.
* Support for Android: for ARM needs work to setup an emulator, but for x86 Gentoo Prefix needs to be bootstrapped in `/data/local/tmp` and that's pretty much it.
