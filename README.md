# `kernel-rc-aufs`: `kernel-rc` with AUFS Support

This repository contains the specfile and config files to build release candidate (RC) kernels that include [the AUFS filesystem](http://aufs.sourceforge.net).

**_WARNING:_ These kernels are not considered stable, and should not be used on production systems!**

The Docker spec files that were part of the [original repo](https://github.com/sciurus/docker-rhel-rpm.git) are no longer included.

Additionally, the build script I had written to build these RPMs has been deprecated. RPM building is now done via the [`kernel-rc-aufs-docker` image.](https://github.com/bnied/kernel-rc-aufs-docker)

This has been tested on the following distributions:
* CentOS 7
* CentOS 8
* Red Hat Enterprise Linux 7
* Red Hat Enterprise Linux 8

Other RHEL-derivative Linux distributions (AlmaLinux, Rocky Linux, etc.) should all work as well, but haven't been tested.

***
## Downloading Packages

Packages are available from [the Spaceduck.org Yum repo](https://yum.spaceduck.org/). Install the [.repo](https://yum.spaceduck.org/kernel-rc-aufs/kernel-rc-aufs.repo) file into `/etc/yum.repos.d` to get updates automatically.

**If you want these packages to be your default kernel in GRUB:** edit `/etc/sysconfig/kernel`, and change `DEFAULTKERNEL` to:
* `DEFAULTKERNEL=kernel-rc-aufs` for EL7
* `DEFAULTKERNEL=kernel-rc-aufs-core` for EL8
