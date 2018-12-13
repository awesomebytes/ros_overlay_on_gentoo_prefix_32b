# Pull ros-kinetic/ros_base already emerged
FROM awesomebytes/roogp_32b_ros_kinetic_ros_base

# To build qt successfully
RUN echo "# For ROS stuff that uses qt\n\
# dev-qt/qtgui udev\n\
dev-qt/qtgui -libinput" >> /tmp/gentoo/etc/portage/package.use

RUN echo ">=dev-libs/libpcre2-10.32 pcre16" >> /tmp/gentoo/etc/portage/package.use 


RUN echo "# required by media-gfx/graphviz-2.40.1-r1::gentoo\n\
# required by dev-python/pydot-1.2.3::gentoo\n\
# required by ros-kinetic/qt_dotgraph-0.3.11::ros-overlay\n\
# required by ros-kinetic/rqt_dep-0.4.9::ros-overlay\n\
# required by ros-kinetic/rqt_common_plugins-0.4.8::ros-overlay\n\
# required by ros-kinetic/viz-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
>=media-libs/gd-2.2.5-r1 truetype jpeg png fontconfig\n\
# required by dev-qt/qtgui-5.11.2-r1::gentoo[xcb]\n\
# required by dev-qt/qtwidgets-5.11.2::gentoo\n\
# required by ros-kinetic/turtlesim-0.7.1::ros-overlay\n\
# required by ros-kinetic/ros_tutorials-0.7.1::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
>=x11-libs/libxcb-1.13.1 xkb\n\
# required by ros-kinetic/opencv3-3.3.1-r5::ros-overlay\n\
# required by ros-kinetic/cv_bridge-1.12.8::ros-overlay\n\
# required by ros-kinetic/rqt_image_view-0.4.13::ros-overlay\n\
# required by ros-kinetic/rqt_common_plugins-0.4.8::ros-overlay\n\
# required by ros-kinetic/viz-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
>=sci-libs/vtk-8.1.0-r3 rendering qt5\n\
# required by dev-qt/qtgui-5.11.2-r1::gentoo[xcb]\n\
# required by dev-qt/qtwidgets-5.11.2::gentoo\n\
# required by ros-kinetic/turtlesim-0.7.1::ros-overlay\n\
# required by ros-kinetic/ros_tutorials-0.7.1::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
>=x11-libs/libxkbcommon-0.8.2 X\n\
# required by dev-libs/collada-dom-2.5.0::gentoo\n\
# required by ros-kinetic/collada_urdf-1.12.12::ros-overlay\n\
# required by ros-kinetic/robot_model-1.12.11::ros-overlay\n\
# required by ros-kinetic/robot-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
>=sys-libs/zlib-1.2.11-r2 minizip" >> /tmp/gentoo/etc/portage/package.use

# Until https://github.com/ros/ros-overlay/pull/719 is merged
# We need to patch and re-emerge message_filters (and anything that depends on it)
RUN mkdir -p /tmp/gentoo/etc/portage/patches/ros-kinetic/message_filters
RUN cd /tmp/gentoo/etc/portage/patches/ros-kinetic/message_filters && \
    wget https://raw.githubusercontent.com/ros/ros-overlay/2792112ea175d02473fca12497d85a55891c56c2/ros-kinetic/message_filters/files/0001-Changed-invocation-to-add-to-conform-template-syntax.patch
RUN $PREFIXED emerge ros-kinetic/message_filters


# For https://bugs.gentoo.org/649808
RUN mkdir -p /tmp/gentoo/etc/portage/profile && \
    echo "sys-fs/udev-init-scripts-32" >> /tmp/gentoo/etc/portage/profile/package.provided

# Newer version doesn't emerge cause it wants to link with libudev.so
# and I found no way of emerging it
RUN echo ">media-libs/libv4l-1.10.1" >> /tmp/gentoo/etc/portage/package.mask

# For https://bugs.gentoo.org/654804
# TODO: make a repository for these kind of patches...
# https://wiki.gentoo.org/wiki/Handbook:AMD64/Portage/CustomTree#Defining_a_custom_repository
RUN sed -i 's/local tmp_root=\${D%\/}\/tmp/local tmp_root=\${ED%\/}\/tmp/g' /tmp/gentoo/usr/portage/dev-python/PyQt5/PyQt5-5.10.1-r1.ebuild
RUN $PREFIXED ebuild /tmp/gentoo/usr/portage/dev-python/PyQt5/PyQt5-5.10.1-r1.ebuild manifest

# Patch Rviz
RUN mkdir -p /tmp/gentoo/etc/portage/patches/ros-kinetic/rviz && \
    cd /tmp/gentoo/etc/portage/patches/ros-kinetic/rviz && \
    wget https://gist.githubusercontent.com/awesomebytes/c1ba75ec3329aa63d768320b43743e70/raw/c1252160503302cf91c4bf73ef880fa9225cb7f4/0001-Adapt-Rviz-to-Ogre-1.11.patch

# If we can go faster, try
RUN sed -i -e 's/j1/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf
RUN sed -i -e 's/j2/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf

# Patch building Qt inside of a docker container https://bugs.gentoo.org/672602
RUN mkdir -p $EPREFIX/etc/portage/patches/dev-qt/qtcore &&\
    cd $EPREFIX/etc/portage/patches/dev-qt/qtcore &&\
    wget https://gist.githubusercontent.com/awesomebytes/856d1d1b0d267f90241451d933a17867/raw/0d4f5eb1095999ffa1265ee6b59ecb33833e63c7/0001-Overcome-statx-libseccomp-issue-with-running-inside-.patch

# cause of https://github.com/ros/ros-overlay/issues/725
RUN $PREFIXED emerge dev-qt/qtwidgets

RUN echo "dev-qt/qtwebkit-5.212.0_pre20180120" >> $EPREFIX/etc/portage/profile/package.provided

# patch https://github.com/ros/ros-overlay/issues/724
RUN mkdir -p $EPREFIX/etc/portage/patches/ros-kinetic/rviz_plugin_tutorials &&\
    cd $EPREFIX/etc/portage/patches/ros-kinetic/rviz_plugin_tutorials &&\
    wget https://gist.githubusercontent.com/awesomebytes/b1cc1490045a568b68dcd618629de405/raw/bbebd2f6a65c54c1a8910182b49b0db4264e909d/0001-Adapt-to-Ogre-1.10.patch

# Also for
RUN $PREFIXED emerge dev-qt/qttest dev-qt/qtconcurrent

# For ros-kinetic/opencv3 as reported in https://github.com/ros/ros-overlay/issues/726
RUN echo ">=dev-games/ogre-1.10.12 experimental" >> $EPREFIX/etc/portage/package.use
RUN $PREFIXED emerge dev-games/ogre

# As reported here https://github.com/ros/ros-overlay/issues/581
# For cv_bridge, camera_calibration_parsers (at least)
RUN echo ">=dev-libs/boost-1.65.0 python" >> $EPREFIX/etc/portage/package.use
RUN $PREFIXED emerge dev-libs/boost[python]
RUN cd $EPREFIX/usr/lib &&\
    ln -s libboost_python-2.7.so libboost_python.so

# May make opencv stuff faster
#RUN emerge sci-libs/atlas

# Setup desktop (237 packages)
RUN $PREFIXED emerge ros-kinetic/desktop || true

# Extra trick needed as qmake points to qtchooser for Rviz, but we can only do it
# when we already have QT5
# so we let ros-kinetic/desktop fail once and do this
RUN cd /tmp/gentoo/usr/bin && \
    unlink qmake && \
    ln -s /tmp/gentoo/usr/lib64/qt5/bin/qmake qmake
RUN $PREFIXED emerge ros-kinetic/desktop


# Create the bin packages
RUN $PREFIXED quickpkg \"*/*\" --include-config y --include-unmodified-config y

ENTRYPOINT ["/bin/bash"]