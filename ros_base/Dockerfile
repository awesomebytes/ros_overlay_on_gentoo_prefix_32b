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

RUN tar xvf gentoo_on_tmp-*.tar.gz

# Make sure it works
RUN bash gentoo/executeonprefix emerge --version

# All commands to run in the Gentoo Prefix environment must
# be preprended by $PREFIXED
ENV PREFIXED=linux32 /tmp/gentoo/executeonprefix
ENV EPREFIX=/tmp/gentoo
ENV CHOST i686-pc-linux-gnu

# By default it's python3 (option 1)
# Choosing Python 2.7
RUN $PREFIXED eselect python set 2

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

# Make sure rosdep emerges OK
RUN $PREFIXED emerge dev-python/rosdep

# Make sure catkin emerges OK
RUN $PREFIXED emerge ros-kinetic/catkin

# Setup the basic ROS
RUN $PREFIXED  emerge ros-kinetic/ros_base

# Create the bin packages
RUN $PREFIXED quickpkg "*/*" --include-config y --include-unmodified-config y

ENTRYPOINT ["/bin/bash"]