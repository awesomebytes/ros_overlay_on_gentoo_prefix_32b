# ros_overlay_on_gentoo_prefix_32b
[![Build Status](https://dev.azure.com/12719821/12719821/_apis/build/status/awesomebytes.ros_overlay_on_gentoo_prefix_32b)](https://dev.azure.com/12719821/12719821/_build/latest?definitionId=6)

Building ROS Kinetic (ROS Kinetic on [master branch](https://github.com/awesomebytes/ros_overlay_on_gentoo_prefix_32b/tree/master), check [melodic branch](https://github.com/awesomebytes/ros_overlay_on_gentoo_prefix_32b/tree/melodic) for ROS Melodic) over Gentoo Prefix (on `/tmp/gentoo` for x86 over a Docker image of Ubuntu 16.04) thanks to [ros-overlay](https://github.com/ros/ros-overlay).

This is a project closely related to [Gentoo Prefix CI 32b](https://github.com/awesomebytes/gentoo_prefix_ci_32b).

Builds page on Azure Pipelines: https://dev.azure.com/12719821/12719821/_build?definitionId=6

Ready-to-use releases: https://github.com/awesomebytes/ros_overlay_on_gentoo_prefix_32b/releases

# Currently building

* ros-kinetic/ros_base
* ros-kinetic/desktop

# Try it

Go to https://github.com/awesomebytes/ros_overlay_on_gentoo_prefix_32b/releases and download the latest release of your choice (ros-kinetic/ros_base is 2GB~), it's divided in 1GB parts.

Put the parts together and extract (4.4GB~):
```
cd ~
cat gentoo_on_tmp* > gentoo_on_tmp.tar.gz
tar xvf gentoo_on_tmp.tar.gz
# Probably delete the intermediate files
rm gentoo_on_tmp*
# Just enter the Gentoo Prefix environment
./gentoo/startprefix
```

ros-kinetic/desktop is ~4GB, extracted ~6GB.

# WIP

* ros-kinetic/desktop_full