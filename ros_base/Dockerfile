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
RUN apt-get install python-pip python-dev  -y
RUN apt-get install wget -y
# To enable ssh
RUN apt-get install openssh-server -y
# To enable adding to the clipboard from the shell
RUN apt-get install xclip -y
# To download stuff faster
RUN apt-get install aria2 -y

WORKDIR /home/user
USER user

# Let's get some specs of the machine that is running this job
RUN cat /proc/cpuinfo
RUN cat /proc/meminfo
RUN df -h

# Note that we always use the same one
RUN aria2c -x 10 https://github.com/awesomebytes/gentoo_prefix_ci_32b/releases/download/release%2F2018-12-05T11at43plus00at00/gentoo_on_tmp-x86_2018-12-05T11at43plus00at00.tar.gz

RUN tar xvf gentoo_on_tmp-*.tar.gz; rm gentoo_on_tmp-*.tar.gz

# Make sure it works
RUN bash gentoo/executeonprefix emerge --version

# All commands to run in the Gentoo Prefix environment must
# be preprended by $PREFIXED
ENV PREFIXED="linux32 /tmp/gentoo/executeonprefix"
ENV EPREFIX=/tmp/gentoo
ENV CHOST i686-pc-linux-gnu

# By default it's python3 (option 1)
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

# So we dont get complains
RUN echo "en_US.UTF-8 UTF-8" >> $EPREFIX/etc/locale.gen
RUN $PREFIXED locale-gen

# # Needed for dev-perl/TimeDate-2.300.0
# # as it errors with dodoc failing with
# # ERROR:root:Failed to copy file: _parsed_options=Namespace(group=-1, mode=420, owner=-1, preserve_timestamps=False), source='ChangeLog', dest_dir='/home/user/gentoo/var/tmp/portage/dev-perl/TimeDate-2.300.0/image/tmp/gentoo/usr/share/doc/TimeDate-2.300.0'
# # Traceback (most recent call last):
# #   File "/tmp/gentoo/usr/lib/portage/python2.7/doins.py", line 209, in run
# #     copyfile(source, dest)
# #   File "/home/user/gentoo/usr/lib/python2.7/site-packages/portage/util/file_copy/__init__.py", line 30, in _optimized_copyfile
# #     _file_copy(src_file.fileno(), dst_file.fileno())
# # OSError: [Errno 1] Operation not permitted
# RUN echo "dev-perl/TimeDate-2.300.0" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/MailTools-2.190.0" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/Digest-HMAC-1.30.0-r1" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/Authen-SASL-2.160.0-r1" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/Net-SSLeay-1.820.0" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/IO-Socket-SSL-2.52.0" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/Net-SMTP-SSL-1.40.0" >> $EPREFIX/etc/portage/profile/package.provided
# RUN echo "dev-perl/Module-Build-0.422.400" >> $EPREFIX/etc/portage/profile/package.provided

# Let's make the compilations faster when possible
# Substitute the default -j2 with -j<NUM_CORES/2>
RUN sed -i -e 's/j2/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf
# Add extra jobs if we have enough CPUs
RUN echo "EMERGE_DEFAULT_OPTS=\"--jobs $((`grep -c \^processor \/proc\/cpuinfo` / 2)) --load-average `grep -c \^processor \/proc\/cpuinfo`\"" >> $EPREFIX/etc/portage/make.conf
RUN cat $EPREFIX/etc/portage/make.conf

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

# Sync ros-overlay
RUN $PREFIXED emaint sync -r ros-overlay

# rosdep fails with
# ERROR: cannot verify pypi.org's certificate, issued by 'CN=DigiCert SHA2 Extended Validation Server CA,OU=www.digicert.com,O=DigiCert Inc,C=US':
#  Unable to locally verify the issuer's authority
RUN $PREFIXED emerge dev-libs/openssl
RUN $PREFIXED emerge app-misc/ca-certificates
RUN $PREFIXED emerge net-misc/wget
RUN $PREFIXED emerge net-misc/curl
RUN $PREFIXED update-ca-certificates

# Currently there is a bug that wont allow to compile rostime and others:
# https://github.com/ros/ros-overlay/issues/711
# # Downgrading cmake seems to work it out
# # We do it here as cmake is emerged as a dependency before we actually
# # emerge ros-kinetic/ros_base
# RUN echo ">=dev-util/cmake-3.13.0" >> $EPREFIX/etc/portage/package.mask
# Probably a better workaround is:
RUN mkdir -p $EPREFIX/etc/portage/patches/ros-kinetic/catkin
COPY 0001-Workaround-error-issue-ros-overlay-711.patch $EPREFIX/etc/portage/patches/ros-kinetic/catkin/0001-Workaround-error-issue-ros-overlay-711.patch

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

# Make sure rosdep emerges OK
RUN $PREFIXED emerge dev-python/rosdep

# Make sure catkin emerges OK
RUN $PREFIXED emerge ros-kinetic/catkin

# Needed to emerge ros-kinetic/ros_base
RUN echo "# required by dev-python/wxpython-3.0.2.0::gentoo\n\
# required by ros-kinetic/actionlib-1.11.13::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
>=x11-libs/pango-1.42.4 X\n\
# required by x11-libs/gtk+-3.24.1::gentoo\n\
# required by x11-themes/adwaita-icon-theme-3.26.1::gentoo\n\
# required by x11-libs/gtk+-2.24.32-r1::gentoo\n\
# required by x11-themes/gtk-engines-adwaita-3.22.3::gentoo\n\
>=x11-libs/cairo-1.16.0-r2 X\n\
# required by dev-python/wxpython-3.0.2.0::gentoo\n\
# required by ros-kinetic/actionlib-1.11.13::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
>=x11-libs/wxGTK-3.0.4-r1:3.0 tiff" >> $EPREFIX/etc/portage/package.use

# Also needed to emerge ros-kinetic/ros_base
RUN echo "# required by ros-kinetic/cpp_common-0.6.11::ros-overlay\n\
# required by ros-kinetic/topic_tools-1.12.14::ros-overlay\n\
# required by ros-kinetic/ros_comm-1.12.14::ros-overlay\n\
# required by ros-kinetic/ros_core-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base-1.3.2::ros-overlay\n\
# required by ros-kinetic/ros_base (argument)\n\
=dev-libs/console_bridge-9999 **" >> $EPREFIX/etc/portage/package.accept_keywords

# Needed to emerge dev-python/pillow
# both 5.3.0 & 5.2.0 don't emerge cause of
# /usr/include/string.h:627:12: fatal error: bits/string.h: No such file or directory
# #  include <bits/string.h>
RUN echo ">=dev-python/pillow-5.2.0" >> $EPREFIX/etc/portage/package.mask


# Setup the basic ROS
RUN $PREFIXED emerge ros-kinetic/ros_base || true
# Apparently dev-lisp/sbcl-1.4.13 sometimes may fail in some of the tests
# given the machine is too stressed
# SB-CONCURRENCY-TEST::MAILBOX.INTERRUPTS-SAFETY.1.
# So just re-running may fix it
# (if nothing failed, this will just take a couple of seconds)
RUN $PREFIXED emerge ros-kinetic/ros_base

# Create the bin packages
RUN $PREFIXED quickpkg \"*/*\" --include-config y --include-unmodified-config y

ENTRYPOINT ["/bin/bash"]