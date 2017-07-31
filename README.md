# `kernel-rc-aufs`: `kernel-rc` with AUFS Support

This repository contains the specfile and config files to build RC kernels that include AUFS for use with Docker.

**These kernels are not considered stable, and should not be used on production systems!**

These packages have been tested on the following distributions:
* CentOS 6
* CentOS 7
* Red Hat Enterprise Linux 6
* Red Hat Enterprise Linux 7

Other RHEL-derivatives should also work, but have not been tested.

***
## Downloading Prebuilt Packages

Install the packages from Fedora Copr:
* [.repo file for EL6](https://copr.fedorainfracloud.org/coprs/bnied/kernel-rc-aufs/repo/epel-6/bnied-kernel-rc-aufs-epel-6.repo)
* [.repo file for EL7](https://copr.fedorainfracloud.org/coprs/bnied/kernel-rc-aufs/repo/epel-7/bnied-kernel-rc-aufs-epel-7.repo)

Please keep in mind that new packages are built as time allows, and that updates to this repo will often appear before the packages are built.

**If you want these packages to be your default kernel in GRUB:** edit `/etc/sysconfig/kernel`, and change `DEFAULTKERNEL` to `DEFAULTKERNEL=kernel-rc-aufs`.

***
## Building Packages
### Prerequisites

Before building the packages, be sure to install the `fedora-packager` package, and add yourself to the `mock` group. If you're on Fedora, `fedora-packager` will be available from the base repos. EL users will need to install from EPEL.

Be aware that building the kernel can take a long time (at least half an hour, up to several hours if you're building on an older machine).

***
### Using the Build Script

Run the `build_kernel.sh` script, and either answer all three questions, or provide them as command-line parameters:

    $ ./build_rc_kernel.sh
    What kernel version do you want to build? (major version only)
    4.13-rc3
    What architecture do you want to build for? (i686, i686-NONPAE, x86_64)
    x86_64
    What version of CentOS/RHEL do you want to build for? (6 or 7)
    7

or

    $ ./build_rc_kernel.sh -v=4.13-rc3 -a=x86_64 -e=7

This will build your packages automatically. Logs for the build can be found in two places:
* `$(REPO_LOCATION)/build/logs` has the output from each command in separate log files.
* `$(REPO_LOCATION)/build/output` contains the `mock` logs so you can see where the build went wrong (if it went wrong at all).

If all goes well, your new RPMs will be moved to `~/RPMs`. The `build` directory will remain behind for analysis.

***
### Building Packages Manually

If you'd rather run through the steps manually, you can do so with the instructions below. Be sure to change `epel-6-x86_64` to `epel-7-x86_64` and the filename for the source RPM if you're building for EL7.

Linux 4.x will use the AUFS 4.x tree in its packages. Linux 3.x will use AUFS 3.x.

In the example below, we're building `kernel-rc` 4.13-rc3 with the latest commit out of the AUFS tree (`f60288dc0e0aab77ca545f42d785ec280f4700b9`) at the time of writing. When you build your kernel versions, be sure to update this step to the latest commit.

    spectool -g -C kernel-rc-aufs kernel-rc-aufs/kernel-rc-aufs-4.13-rc3.spec
    git clone git://git.code.sf.net/p/aufs/aufs3-standalone -b aufs4.x-rcN
    pushd aufs-standalone
    git archive f60288dc0e0aab77ca545f42d785ec280f4700b9 > ../kernel-rc-aufs/aufs-standalone.tar
    popd
    mock -r epel-6-x86_64 --buildsrpm --spec kernel-rc-aufs/kernel-rc-aufs-4.13-rc3.spec --sources kernel-rc-aufs --resultdir output
    mock -r epel-6-x86_64 --rebuild --resultdir output output/kernel-rc-aufs-4.13-rc3-1.el6.src.rpm

The resulting RPMs will be placed in a directory named `output`.

***
### Installing the Packages

Once your packages are built, you can install them with `yum`. `cd` to the appropriate directory, and run:

    yum localinstall --nogpgcheck kernel-rc-aufs-4.13-rc3-1.el6.x86_64.rpm

In order to use docker on EL6, you'll need to install it out of EPEL:

    yum install docker-io

For EL7, Docker packages are in the main repos:

    yum install docker

Reboot and choose the AUFS kernel from your GRUB menu (or edit GRUB to change your default kernel).

If everything is working as expected, you should see that AUFS is your storage driver when you run `docker info`:

    [bnied@buildbox ~]$ sudo docker info
    Containers: 0
    Images: 0
    Storage Driver: aufs
    Root Dir: /var/lib/docker/aufs
    Backing Filesystem: xfs
    Dirs: 0
    Dirperm1 Supported: true
    Execution Driver: native-0.2
    Kernel Version: 4.0.2-1.el7.centos.x86_64
    Operating System: CentOS Linux 7 (Core)
    CPUs: 2
    Total Memory: 1.938 GiB
    Name: buildbox.local
    ID: BZQL:SPMI:Y23R:KBPK:SHR2:UTIN:Q2SS:N6SE:3DL2:PNKK:YP5D:OANX
    WARNING: No swap limit support

If you're still seeing `devicemapper` as your storage backend, you'll need to change Docker's configuration to use AUFS. Open `/etc/sysconfig/docker`, and change line 4 from:

    OPTIONS='--selinux-enabled'
to:

    OPTIONS='--selinux-enabled --storage-driver=aufs'

Restart the Docker service, and it should switch to the correct backend.
