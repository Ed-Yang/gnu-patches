# Build Octeon SDK 5.1.0

Build Octeon SDk on Debian Buster.

$ uname -a  

```shell
Linux buster 4.19.0-8-amd64 #1 SMP Debian 4.19.98-1 (2020-01-26) x86_64 GNU/Linux
```

$ gcc -v

```shell
gcc version 8.3.0 (Debian 8.3.0-6)
```

Assuming the patch files are extracted on /tmp.  

## Install host packages

```shell
apt-get install alien dpkg-dev debhelper build-essential libtool automake
apt-get install-y  libncurses5-dev libncursesw5-dev  git wget bc flex  byacc bison pkg-config 
```

## Install SDK

```shell
sudo alien --scripts OCTEON-SDK-5.1.0-609.x86_64.rpm
sudo dpkg -i octeon-sdk_5.1.0-610_amd64.deb
sudo alien --scripts OCTEON-LINUX-5.1.0-609.x86_64.rpm
sudo dpkg -i octeon-linux_5.1.0-610_amd64.deb
```

## Patch SDK p2 - part 1 (sdk_5.1.0_update_p2)

Follow the procedures in README.txt.  

cd /usr/local/Cavium_Networks/OCTEON-SDK  

```shell
patch -s -p1 < /tmp/sdk-5.1.0-patch_release2/sdk-5.1.0.patch
patch -s -p1 < /tmp/sdk-5.1.0-patch_release2/linux-5.1.0.patch
```

```shell
patch -s -p1 < /tmp/sdk-5.1.0-patch_release2/sdk-ejtag-5.1.0.patch
/tmp/sdk-5.1.0-patch_release2/octeon_copy_5_1_0_p2_files.sh /tmp/sdk-5.1.0-patch_release2
```

## Patch GCC (GCC7.3.0_Toolchain_sdk510)

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK
patch -p1 < /tmp/GCC7.3.0_Toolchain_sdk510/5.1.0-sdk_build_with_7.3_toohchain_for_GA.diff
tar xvf /tmp/GCC7.3.0_Toolchain_sdk510/octeon-tools-249.0.tar.bz2
ln -s octeon-tools-249.0 tools
```

Note. 5.1.0-sdk_build_with_7.3_toohchain_for_p2.diff: Extra patch for SDK510 patch2 ← patch error !!!

## Patch SDK p2 - part 2 (sdk_5.1.0_update_p2)

```shell
source env-setup OCTEON_CN73XX
cd host/remote-lib
make
```

```shell
cd ../remote-utils
make
```

## Additional Patch for new GNU tools

cd /usr/local/Cavium_Networks/OCTEON-SDK  
git clone https://github.com/Ed-Yang/gnu-patches.git  

* download m4-1.4.18

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux/embedded_rootfs/storage
wget https://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.gz
```

* patch pkg_makefiles

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux/embedded_rootfs
patch -p0 < ../../gnu-patches/xxxx-pkg-makefiles.patch
```

* patch octeon linux warning

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux/kernel
patch -p0 < ../../gnu-patches/xxx-octeon-linux-warning.patch
```

Modified files:

```shell
kernel/linux/arch/mips/cavium-octeon/executive/cvmx-pcie.c
kernel/linux/drivers/net/ethernet/cavium/octeon/octeon3-ethernet.c
```

## Build

Setup environment variables:

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/  
source env-setup OCTEON_CN73XX
```

### Linux + embedded filesystem

Build kernel with filesystem image:

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux  
make kernel
```

### Linux for Debian filesystem

Build kernel only image

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux  
make kernel-deb
```

Write Debian filesystem to compact flash  

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux/debian
PATH=$PATH:/sbin
make DISK=/dev/sdx compact-flash
```
substitute /dev/sdx to the correct device. e.g /dev/sda or /dev/sdb
 
## MISC

If the make report aclocal-1.15 or automake-1.15 not found, you might try this:

```shell
cd /usr/bin && ln -s aclocal aclocal-1.15
cd /usr/bin && ln -s automake automake-1.15
```
