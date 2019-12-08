# Pull ros-kinetic/ros_base already emerged
FROM awesomebytes/roogp_32b_ros_kinetic_ros_base

# To build qt successfully
RUN echo "# For ROS stuff that uses qt\n\
# dev-qt/qtgui udev\n\
dev-qt/qtgui -libinput -udev" >> /tmp/gentoo/etc/portage/package.use

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

RUN echo "# required by dev-libs/urdfdom-9999::gentoo\n\
# required by ros-kinetic/urdf-1.12.12::ros-overlay\n\
# required by ros-kinetic/collada_urdf-1.12.12::ros-overlay\n\
# required by ros-kinetic/robot_model-1.12.11::ros-overlay\n\
# required by ros-kinetic/robot-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
=dev-libs/urdfdom_headers-9999 **\n\
# required by ros-kinetic/robot_model-1.12.11::ros-overlay\n\
# required by ros-kinetic/robot-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
=dev-libs/urdfdom-9999 **\n\
# required by ros-kinetic/collada_urdf-1.12.12::ros-overlay\n\
# required by ros-kinetic/robot_model-1.12.11::ros-overlay\n\
# required by ros-kinetic/robot-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop-1.3.2::ros-overlay\n\
# required by ros-kinetic/desktop (argument)\n\
=dev-libs/collada-dom-9999 **" >> /tmp/gentoo/etc/portage/package.accept_keywords


# For https://bugs.gentoo.org/649808
RUN mkdir -p /tmp/gentoo/etc/portage/profile && \
    echo "sys-fs/udev-init-scripts-32" >> /tmp/gentoo/etc/portage/profile/package.provided

# Newer version doesn't emerge cause it wants to link with libudev.so
# and I found no way of emerging it
RUN echo ">media-libs/libv4l-1.10.1" >> /tmp/gentoo/etc/portage/package.mask

# # For https://bugs.gentoo.org/654804
# # TODO: make a repository for these kind of patches...
# # https://wiki.gentoo.org/wiki/Handbook:AMD64/Portage/CustomTree#Defining_a_custom_repository
# RUN sed -i 's/local tmp_root=\${D%\/}\/tmp/local tmp_root=\${ED%\/}\/tmp/g' /tmp/gentoo/usr/portage/dev-python/PyQt5/PyQt5-5.10.1-r1.ebuild
# RUN $PREFIXED ebuild /tmp/gentoo/usr/portage/dev-python/PyQt5/PyQt5-5.10.1-r1.ebuild manifest

# If we can go faster, try
RUN sed -i -e 's/j1/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf
RUN sed -i -e 's/j2/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf

# # Patch building Qt inside of a docker container https://bugs.gentoo.org/672602
# RUN mkdir -p $EPREFIX/etc/portage/patches/dev-qt/qtcore-5.11.3 &&\
#     cd $EPREFIX/etc/portage/patches/dev-qt/qtcore-5.11.3 &&\
#     wget https://gist.githubusercontent.com/awesomebytes/856d1d1b0d267f90241451d933a17867/raw/0d4f5eb1095999ffa1265ee6b59ecb33833e63c7/0001-Overcome-statx-libseccomp-issue-with-running-inside-.patch
# For https://github.com/ros/ros-overlay/issues/729
RUN echo ">=dev-python/PyQt5-5.10.1-r1 gui widgets" >> $EPREFIX/etc/portage/package.use

# dev-qt/qtcore 5.12 & 5.13 has some error that causes later on ros-kinetic/qt_gui_cpp
# to fail emerging with
# [very long line] ... -lQt5Gui $[QT_INSTALL_LIBS]/libQt5Core.so -lpthread  ...
# x86_64-pc-linux-gnu-g++: error: 0/libQt5Core.so: No such file or directory
# So we emerge it and then we patch the problem (an un-substituted symbol)
RUN $PREFIXED emerge dev-qt/qtcore dev-qt/qtgui

# cause of https://github.com/ros/ros-overlay/issues/725
RUN $PREFIXED emerge dev-qt/qttest dev-qt/qtconcurrent dev-qt/qtwidgets dev-python/PyQt5[gui,widgets]
RUN echo "dev-qt/qtwebkit-5.212.0_pre20180120" >> $EPREFIX/etc/portage/profile/package.provided
RUN cd /tmp/gentoo/usr/bin && \
    unlink qmake && \
    ln -s /tmp/gentoo/usr/lib64/qt5/bin/qmake qmake
RUN qt_install_libs_path=`qmake -query QT_INSTALL_LIBS`; sed -i 's@$$\[QT_INSTALL_LIBS\]@'$qt_install_libs_path'@' $EPREFIX/usr/lib/libQt5Gui.prl

# For ros-kinetic/opencv3 as reported in https://github.com/ros/ros-overlay/issues/726
# RUN echo ">=dev-games/ogre-1.10.12 experimental" >> $EPREFIX/etc/portage/package.use
# RUN sed -i 's/-DOGRE_NODE_STORAGE_LEGACY=no/-DOGRE_NODE_STORAGE_LEGACY=yes/g' $EPREFIX/usr/portage/dev-games/ogre/ogre-1.10.12.ebuild &&\
#     $PREFIXED ebuild $EPREFIX/usr/portage/dev-games/ogre/ogre-1.10.12.ebuild manifest
RUN echo ">dev-games/ogre-1.9.0-r1" >> $EPREFIX/etc/portage/package.mask
RUN $PREFIXED emerge dev-games/ogre

# As reported here https://github.com/ros/ros-overlay/issues/581
# For cv_bridge, camera_calibration_parsers (at least)
RUN echo ">=dev-libs/boost-1.65.0 python" >> $EPREFIX/etc/portage/package.use
RUN $PREFIXED emerge dev-libs/boost[python]
RUN cd $EPREFIX/usr/lib &&\
    ln -s libboost_python-2.7.so libboost_python.so



ENTRYPOINT ["/bin/bash"]
