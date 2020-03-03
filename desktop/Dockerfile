# Pull ros-melodic/ros_base already emerged
FROM awesomebytes/roogp_32b_ros_melodic_ros_base

# To build qt successfully
RUN echo "# For ROS stuff that uses qt\n\
# dev-qt/qtgui udev\n\
dev-qt/qtgui -libinput -udev" >> /tmp/gentoo/etc/portage/package.use

RUN echo ">=dev-libs/libpcre2-10.32 pcre16" >> /tmp/gentoo/etc/portage/package.use 


RUN echo "# required by media-gfx/graphviz-2.40.1-r1::gentoo\n\
# required by dev-python/pydot-1.2.3::gentoo\n\
# required by ros-melodic/qt_dotgraph-0.3.11::ros-overlay\n\
# required by ros-melodic/rqt_dep-0.4.9::ros-overlay\n\
# required by ros-melodic/rqt_common_plugins-0.4.8::ros-overlay\n\
# required by ros-melodic/viz-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
>=media-libs/gd-2.2.5-r1 truetype jpeg png fontconfig\n\
# required by dev-qt/qtgui-5.11.2-r1::gentoo[xcb]\n\
# required by dev-qt/qtwidgets-5.11.2::gentoo\n\
# required by ros-melodic/turtlesim-0.7.1::ros-overlay\n\
# required by ros-melodic/ros_tutorials-0.7.1::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
>=x11-libs/libxcb-1.13.1 xkb\n\
# required by ros-melodic/opencv3-3.3.1-r5::ros-overlay\n\
# required by ros-melodic/cv_bridge-1.12.8::ros-overlay\n\
# required by ros-melodic/rqt_image_view-0.4.13::ros-overlay\n\
# required by ros-melodic/rqt_common_plugins-0.4.8::ros-overlay\n\
# required by ros-melodic/viz-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
>=sci-libs/vtk-8.1.0-r3 rendering qt5\n\
# required by dev-qt/qtgui-5.11.2-r1::gentoo[xcb]\n\
# required by dev-qt/qtwidgets-5.11.2::gentoo\n\
# required by ros-melodic/turtlesim-0.7.1::ros-overlay\n\
# required by ros-melodic/ros_tutorials-0.7.1::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
>=x11-libs/libxkbcommon-0.8.2 X\n\
# required by dev-libs/collada-dom-2.5.0::gentoo\n\
# required by ros-melodic/collada_urdf-1.12.12::ros-overlay\n\
# required by ros-melodic/robot_model-1.12.11::ros-overlay\n\
# required by ros-melodic/robot-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
>=sys-libs/zlib-1.2.11-r2 minizip" >> /tmp/gentoo/etc/portage/package.use

RUN echo "# required by dev-libs/urdfdom-9999::gentoo\n\
# required by ros-melodic/urdf-1.12.12::ros-overlay\n\
# required by ros-melodic/collada_urdf-1.12.12::ros-overlay\n\
# required by ros-melodic/robot_model-1.12.11::ros-overlay\n\
# required by ros-melodic/robot-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
=dev-libs/urdfdom_headers-9999 **\n\
# required by ros-melodic/robot_model-1.12.11::ros-overlay\n\
# required by ros-melodic/robot-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
=dev-libs/urdfdom-9999 **\n\
# required by ros-melodic/collada_urdf-1.12.12::ros-overlay\n\
# required by ros-melodic/robot_model-1.12.11::ros-overlay\n\
# required by ros-melodic/robot-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop-1.3.2::ros-overlay\n\
# required by ros-melodic/desktop (argument)\n\
=dev-libs/collada-dom-9999 **" >> /tmp/gentoo/etc/portage/package.accept_keywords


# For https://bugs.gentoo.org/649808
RUN mkdir -p /tmp/gentoo/etc/portage/profile && \
    echo "sys-fs/udev-init-scripts-32" >> /tmp/gentoo/etc/portage/profile/package.provided

# Newer version doesn't emerge cause it wants to link with libudev.so
# and I found no way of emerging it
RUN echo ">media-libs/libv4l-1.10.1" >> /tmp/gentoo/etc/portage/package.mask

# If we can go faster, try
RUN sed -i -e 's/j1/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf
RUN sed -i -e 's/j2/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf

# # Patch building Qt inside of a docker container https://bugs.gentoo.org/672602
# RUN mkdir -p $EPREFIX/etc/portage/patches/dev-qt/qtcore-5.11.3 &&\
#     cd $EPREFIX/etc/portage/patches/dev-qt/qtcore-5.11.3 &&\
#     wget https://gist.githubusercontent.com/awesomebytes/856d1d1b0d267f90241451d933a17867/raw/0d4f5eb1095999ffa1265ee6b59ecb33833e63c7/0001-Overcome-statx-libseccomp-issue-with-running-inside-.patch
# For https://github.com/ros/ros-overlay/issues/729
RUN echo ">=dev-python/PyQt5-5.10.1-r1 gui widgets webkit network printsupport opengl" >> $EPREFIX/etc/portage/package.use

RUN echo "# required by virtual/rubygems-16::gentoo[ruby_targets_ruby27]\n\
# required by dev-ruby/racc-1.4.16-r1::gentoo[-test,ruby_targets_ruby24]\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.7.0::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby25]\n\
>=dev-ruby/minitest-5.14.0 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by virtual/rubygems-16::gentoo[ruby_targets_ruby27]\n\
# required by dev-ruby/racc-1.4.16-r1::gentoo[-test,ruby_targets_ruby24]\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.5.7::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby25]\n\
>=dev-ruby/test-unit-3.3.5 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by virtual/rubygems-16::gentoo[ruby_targets_ruby27]\n\
# required by dev-ruby/racc-1.4.16-r1::gentoo[-test,ruby_targets_ruby24]\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.5.7::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby25]\n\
>=dev-ruby/xmlrpc-0.3.0 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by virtual/rubygems-16::gentoo[ruby_targets_ruby27]\n\
# required by dev-ruby/racc-1.4.16-r1::gentoo[-test,ruby_targets_ruby24]\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.5.7::gentoo[rdoc]\n\
# required by dev-ruby/xmlrpc-0.3.0::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.4.9::gentoo\n\
# required by dev-ruby/did_you_mean-1.1.2::gentoo[ruby_targets_ruby24]\n\
>=dev-ruby/bundler-2.1.4 ruby_targets_ruby27 -doc\n\
# required by dev-ruby/bundler-2.1.4::gentoo[-test,ruby_targets_ruby27]\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by dev-ruby/did_you_mean-1.4.0::gentoo[ruby_targets_ruby27]\n\
>=virtual/rubygems-16 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/power_assert-1.1.5 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/rake-13.0.1 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/did_you_mean-1.4.0 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/net-telnet-0.2.0 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/json-2.3.0 ruby_targets_ruby27\n\
# required by dev-lang/ruby-2.7.0::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/rdoc-6.2.0 ruby_targets_ruby27\n\
# required by virtual/rubygems-16::gentoo[ruby_targets_ruby27]\n\
# required by dev-ruby/racc-1.4.16-r1::gentoo[-test,ruby_targets_ruby24]\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.7.0::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/rubygems-3.1.2 ruby_targets_ruby27\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby27]\n\
# required by dev-lang/ruby-2.7.0::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/kpeg-1.1.0-r1 ruby_targets_ruby27\n\
# required by dev-ruby/rdoc-6.2.0::gentoo[ruby_targets_ruby27]\n\
# required by dev-lang/ruby-2.7.0::gentoo[rdoc]\n\
# required by dev-ruby/bundler-2.1.4::gentoo[ruby_targets_ruby27]\n\
>=dev-ruby/racc-1.4.16-r1 ruby_targets_ruby27" >> $EPREFIX/etc/portage/package.use

# dev-qt/qtcore 5.12 & 5.13 has some error that causes later on ros-melodic/qt_gui_cpp
# to fail emerging with
# [very long line] ... -lQt5Gui $[QT_INSTALL_LIBS]/libQt5Core.so -lpthread  ...
# x86_64-pc-linux-gnu-g++: error: 0/libQt5Core.so: No such file or directory
# So we emerge it and then we patch the problem (an un-substituted symbol)
RUN $PREFIXED emerge dev-qt/qtcore dev-qt/qtgui dev-qt/qttest dev-qt/qtconcurrent dev-qt/qtwidgets

# gstreamer gets pulled by Qt at some point, while emerging ros-melodic/desktop
# Meanwhile https://bugs.gentoo.org/705974 gets fixed upstream (make has a backward incompatible change)
RUN mkdir -p $EPREFIX/etc/portage/patches/media-libs/gstreamer-1.14.5 &&\
    wget https://705974.bugs.gentoo.org/attachment.cgi?id=604218 -O $EPREFIX/etc/portage/patches/media-libs/gstreamer-1.14.5/make-fix.patch
RUN mkdir -p $EPREFIX/etc/portage/patches/media-libs/gst-plugins-bad-1.14.5 &&\
    wget https://705974.bugs.gentoo.org/attachment.cgi?id=604222 -O $EPREFIX/etc/portage/patches/media-libs/gst-plugins-bad-1.14.5/make-fix.patch
RUN mkdir -p $EPREFIX/etc/portage/patches/media-libs/gst-plugins-base-1.14.5 &&\
    wget https://705974.bugs.gentoo.org/attachment.cgi?id=604220 -O $EPREFIX/etc/portage/patches/media-libs/gst-plugins-base-1.14.5/make-fix.patch


ENTRYPOINT ["/bin/bash"]
