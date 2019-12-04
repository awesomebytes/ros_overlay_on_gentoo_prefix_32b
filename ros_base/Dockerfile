# Just Ubuntu 16.04 with a user called user and some basic tools
FROM 32bit/ubuntu:16.04

RUN apt-get update
# Add sudo
RUN apt-get install apt-utils sudo -y

# Create user
RUN useradd --create-home --shell=/bin/bash user
RUN chown -R user /home/user/
# Add the user to sudoers
RUN chmod -R o-w /etc/sudoers.d/
RUN usermod -aG sudo user
# Give the user a password
RUN echo user:user | chpasswd

# Instal basic stuff
RUN apt-get install build-essential -y

# Nice tools to have
RUN apt-get install bash-completion nano net-tools less iputils-ping vim emacs -y
RUN apt-get install python-pip python-dev -y
# Download stuff
RUN apt-get install wget curl aria2 -y
# To enable ssh
RUN apt-get install openssh-server -y


WORKDIR /home/user
USER user

# Let's get some specs of the machine that is running this job
RUN cat /proc/cpuinfo; cat /proc/meminfo; df -h

# Note that we always use the same one
# This command will visit the latest release page and will look for the url of the artifact to download and download it
RUN aria2c -x 10 http://github.com`curl -s -L https://github.com/awesomebytes/gentoo_prefix_ci_32b/releases/latest | grep "download/release" | cut -d '"' -f2`

RUN tar xvf gentoo_on_tmp-*.tar.gz; rm gentoo_on_tmp-*.tar.gz

# Make sure it works
RUN bash gentoo/executeonprefix emerge --version

# All commands to run in the Gentoo Prefix environment must
# be preprended by $PREFIXED
ENV PREFIXED="linux32 /tmp/gentoo/executeonprefix"
ENV EPREFIX=/tmp/gentoo
ENV CHOST i686-pc-linux-gnu

# So we dont get complains
RUN echo "en_US.UTF-8 UTF-8" >> $EPREFIX/etc/locale.gen
RUN $PREFIXED locale-gen

# Let's make the compilations faster when possible
# Substitute the default -j2 with -j<NUM_CORES/2>
RUN sed -i -e 's/j2/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf
# Add extra jobs if we have enough CPUs
RUN echo "EMERGE_DEFAULT_OPTS=\"--jobs $((`grep -c \^processor \/proc\/cpuinfo` / 2)) --load-average `grep -c \^processor \/proc\/cpuinfo`\"" >> $EPREFIX/etc/portage/make.conf
RUN cat $EPREFIX/etc/portage/make.conf

# dev-vcs/git-2.20.1 fails with (this command was already here, but executed after emerging git)
# ERROR: cannot verify www.kernel.org's certificate
# rosdep fails with
# ERROR: cannot verify pypi.org's certificate, issued by 'CN=DigiCert SHA2 Extended Validation Server CA,OU=www.digicert.com,O=DigiCert Inc,C=US':
#  Unable to locally verify the issuer's authority
# RUN $PREFIXED emerge dev-libs/openssl app-misc/ca-certificates net-misc/wget net-misc/curl
RUN $PREFIXED update-ca-certificates

# Emerging git
RUN $PREFIXED emerge dev-vcs/git

# Setting EAPI to 6 as explained in http://wiki.ros.org/Installation/Gentoo
RUN mkdir -p $EPREFIX/etc/portage/profile/ && echo "6" > $EPREFIX/etc/portage/profile/eapi

# Setting up ros-overlay https://github.com/ros/ros-overlay
RUN mkdir -p $EPREFIX/etc/portage/repos.conf && echo "[ros-overlay]\n\
location = $EPREFIX/usr/local/portage\n\
sync-type = git\n\
sync-uri = https://github.com/ros/ros-overlay\n\
auto-sync = yes\n\
masters = gentoo" > $EPREFIX/etc/portage/repos.conf/ros-overlay.conf

# Set up Gentoo to sync via git (way faster)
RUN rm -rf $EPREFIX/usr/portage && echo "[DEFAULT]\n\
main-repo = gentoo\n\
\n\
[gentoo]\n\
location = /tmp/gentoo/usr/portage\n\
sync-type = git\n\
sync-uri = https://github.com/gentoo-mirror/gentoo \n\
auto-sync = yes" > $EPREFIX/etc/portage/repos.conf/gentoo.conf

# Sync everything
RUN $PREFIXED emaint sync -a

# # bug 674830
# RUN echo ">=sys-apps/dbus-1.12.10" >> $EPREFIX/etc/portage/package.mask
RUN $PREFIXED emerge sys-apps/dbus

# Build python with sqlite and bluetooth support
# I find very surprising sqlite is not mandatory
# Also enable PGO and LTO optimizations
RUN echo "dev-lang/python sqlite bluetooth readline" >> $EPREFIX/etc/portage/package.use
# To build succesfully the bluetooth support
RUN echo "net-wireless/bluez -udev" >> $EPREFIX/etc/portage/package.use
RUN EXTRA_ECONF="--enable-optimizations --with-lto" $PREFIXED emerge python:2.7[sqlite,bluetooth,readline] python:3.6[sqlite,bluetooth,readline]

# By default it's python3 (option 1) but ROS uses Python 2.7 (as of now)
# Choosing Python 2.7
RUN $PREFIXED eselect python set 2

# Only emerge for Python 2.7
# Because of errors like:
# Issue https://github.com/ros/ros-overlay/issues/715
RUN echo "# From the Docker image of ros-overlay\n\
# mainly force to build just Python 2.7\n\
ros-indigo/* PYTHON_TARGETS: -* python2_7\n\
ros-indigo/* PYTHON_SINGLE_TARGET: -* python2_7\n\
ros-kinetic/* PYTHON_TARGETS: -* python2_7\n\
ros-kinetic/* PYTHON_SINGLE_TARGET: -* python2_7\n\
ros-lunar/* PYTHON_TARGETS: -* python2_7\n\
ros-lunar/* PYTHON_SINGLE_TARGET: -* python2_7\n\
" >> $EPREFIX/etc/portage/package.use

# Currently there is a bug that wont allow to compile rostime and others:
# https://github.com/ros/ros-overlay/issues/711
RUN mkdir -p $EPREFIX/etc/portage/patches/ros-kinetic/catkin
COPY 0001-Workaround-error-issue-ros-overlay-711.patch $EPREFIX/etc/portage/patches/ros-kinetic/catkin/0001-Workaround-error-issue-ros-overlay-711.patch

# It's also needed to not run in problems later on (on ros-kinetic/desktop)
# This can't be done anymore, the oldest version is 3.13.5 as of writing this comment
# RUN echo ">dev-util/cmake-3.12.3" >> $EPREFIX/etc/portage/package.mask
# Make sure we emerge that version
RUN $PREFIXED emerge dev-util/cmake

# Needed to emerge dev-python/rosdep on 32b
RUN echo "# required by dev-python/rosdep-0.13.0::ros-overlay\n\
# required by dev-python/rosdep (argument)\n\
=dev-python/catkin_pkg-9999 **\n\
# required by dev-python/rosdep-0.13.0::ros-overlay\n\
# required by dev-python/rosdep (argument)\n\
=dev-python/rosdistro-9999 **\n\
# required by dev-python/rosdistro-9999::gentoo\n\
# required by dev-python/rosdep-0.13.0::ros-overlay\n\
# required by dev-python/rosdep (argument)\n\
=dev-python/rospkg-9999 **" >> $EPREFIX/etc/portage/package.accept_keywords

# Make sure rosdep & catkin emerge OK
RUN $PREFIXED emerge dev-python/rosdep ros-kinetic/catkin

RUN echo "sys-libs/libcap -pam" >> $EPREFIX/etc/portage/package.use

# Needed to emerge ros-kinetic/ros_base
RUN echo "# required by ros-kinetic/roslib-1.14.6-r1::ros-overlay\n\
# required by ros-kinetic/actionlib-1.11.13::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
>=dev-libs/boost-1.71.0 python\n\
# required by dev-python/wxpython-4.0.6::gentoo\n\
# required by ros-kinetic/actionlib-1.11.13::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
>=x11-libs/wxGTK-3.0.4-r302 opengl sdl gstreamer tiff libnotify\n\
# required by x11-libs/gtk+-3.24.11::gentoo\n\
# required by x11-misc/notification-daemon-3.20.0::gentoo\n\
# required by virtual/notification-daemon-0::gentoo[-kde,-gnome]\n\
# required by x11-libs/libnotify-0.7.8::gentoo\n\
# required by x11-libs/wxGTK-3.0.4-r302::gentoo[X,libnotify]\n\
# required by dev-python/wxpython-4.0.6::gentoo\n\
# required by ros-kinetic/actionlib-1.11.13::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
>=x11-libs/cairo-1.16.0-r3 X" >> $EPREFIX/etc/portage/package.use


# Also needed to emerge ros-kinetic/ros_base
RUN echo "# required by ros-kinetic/cpp_common-0.6.11::ros-overlay\n\
# required by ros-kinetic/topic_tools-1.12.14::ros-overlay\n\
# required by ros-kinetic/ros_comm-1.12.14::ros-overlay\n\
# required by ros-kinetic/ros_core-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
=dev-libs/console_bridge-9999 **" >> $EPREFIX/etc/portage/package.accept_keywords

# Emerge llvm already, as it takes 2h30 already, so we can
# debug the next step (where we really emerge ros_base) easier and faster
RUN $PREFIXED emerge sys-devel/llvm


ENTRYPOINT ["/bin/bash"]