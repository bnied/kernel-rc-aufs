# `kernel-rc-aufs`: `kernel-rc` with AUFS Support

This repository contains the specfile and config files to build release candidate (RC) kernels that include [the AUFS filesystem](http://aufs.sourceforge.net).

**_WARNING:_ These kernels are not considered stable, and should not be used on production systems!**

The Docker spec files that were part of the [original repo](https://github.com/sciurus/docker-rhel-rpm.git) are no longer included.

These kernels should work on Red Hat Enterprise Linux, and any RHEL-derivative distrubition, such Almalinux, CentOS, Oracle Enterprise Linux, or Rocky Linux.

***
## Downloading Packages

Packages are available from [Copr](https://copr.fedorainfracloud.org/coprs/bnied/kernel-rc-aufs). Follow the instructions there to get updates automatically.

**If you want these packages to be your default kernel in GRUB:** edit `/etc/sysconfig/kernel`, and change `DEFAULTKERNEL` to:
* `DEFAULTKERNEL=kernel-rc-aufs` for EL7
* `DEFAULTKERNEL=kernel-rc-aufs-core` for EL8/EL9
