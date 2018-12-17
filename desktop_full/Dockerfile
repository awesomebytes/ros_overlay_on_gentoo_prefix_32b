FROM awesomebytes/roogp_ros_kinetic_desktop_2

# If we can go faster, try
RUN sed -i -e 's/j1/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf
RUN sed -i -e 's/j2/j'"$((`grep -c \^processor \/proc\/cpuinfo` / 2))"'/g' $EPREFIX/etc/portage/make.conf

# Patch issue https://github.com/ros/ros-overlay/issues/607
ADD 0001-Force-find-gtk2.0.patch 0001-Force-find-gtk2.0.patch
RUN mkdir -p $EPREFIX/etc/portage/patches/ros-kinetic/image_view &&\
    mv 0001-Force-find-gtk2.0.patch $EPREFIX/etc/portage/patches/ros-kinetic/image_view

# desktop_full includes desktop, perception & simulators
RUN $PREFIXED emerge ros-kinetic/perception

RUN echo "# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=virtual/rubygems-14 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/json-2.1.0 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/test-unit-3.2.9 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/minitest-5.11.3 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/net-telnet-0.2.0 ruby_targets_ruby25\n\
# required by virtual/rubygems-14::gentoo[ruby_targets_ruby25]\n\
# required by dev-ruby/mustache-1.1.0::gentoo\n\
# required by app-text/ronn-0.7.3-r3::gentoo[ruby_targets_ruby23,-test]\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/rubygems-2.7.8 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo[rdoc]\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/rdoc-6.0.4 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/power_assert-1.1.3 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/rake-12.3.2 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/did_you_mean-1.2.1 ruby_targets_ruby25\n\
# required by dev-lang/ruby-2.5.3::gentoo\n\
# required by dev-libs/sdformat-6.1.0::gentoo\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/xmlrpc-0.3.0 ruby_targets_ruby25\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=media-libs/freeimage-3.15.4-r1 png\n\
\n\
# required by dev-ruby/rdoc-6.0.4::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.4.5-r1::gentoo[rdoc]\n\
# required by dev-ruby/mustache-1.1.0::gentoo[ruby_targets_ruby24]\n\
# required by app-text/ronn-0.7.3-r3::gentoo[ruby_targets_ruby23,-test]\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/racc-1.4.14 ruby_targets_ruby25\n\
# required by dev-ruby/rdoc-6.0.4::gentoo[ruby_targets_ruby25]\n\
# required by dev-lang/ruby-2.4.5-r1::gentoo[rdoc]\n\
# required by dev-ruby/mustache-1.1.0::gentoo[ruby_targets_ruby24]\n\
# required by app-text/ronn-0.7.3-r3::gentoo[ruby_targets_ruby23,-test]\n\
# required by sci-electronics/gazebo-9.4.1::ros-overlay\n\
# required by =sci-electronics/gazebo-9.4.1 (argument)\n\
>=dev-ruby/kpeg-1.1.0-r1 ruby_targets_ruby25" >> /tmp/gentoo/etc/portage/package.use


# Workaround (bug https://bugs.gentoo.org/672598)
RUN sed -i 's/${D}/${ED}/g' /tmp/gentoo/usr/portage/dev-libs/libspnav/libspnav-0.2.2.ebuild &&\
    cd $EPREFIX/usr/portage/dev-libs/libspnav &&\
    $PREFIXED ebuild libspnav-0.2.2.ebuild manifest

# Still plenty of problems to get there...
# currently stuck on ignition-math
RUN $PREFIXED emerge =sci-electronics/gazebo-9.4.1

RUN $PREFIXED emerge ros-kinetic/desktop_full

