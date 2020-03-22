# Build Octeon SDK 5.1.0

Build Octeon SDk on Debian Buster.

## Install host packages 

```shell
apt-get install alien dpkg-dev debhelper build-essential libtool automake
```

## Install SDK

```shell
sudo alien --scripts OCTEON-SDK-5.1.0-609.x86_64.rpm
sudo dpkg -i octeon-sdk_5.1.0-610_amd64.deb
sudo alien --scripts OCTEON-LINUX-5.1.0-609.x86_64.rpm
sudo dpkg -i octeon-linux_5.1.0-610_amd64.deb
```

## Patch SDK (sdk_5.1.0_update_p2)

Follow the procedures in README.txt.

## Patch GCC (GCC7.3.0_Toolchain_sdk510)

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK 
patch -p1 < 5.1.0-sdk_build_with_7.3_toohchain_for_GA.diff
ln -s octeon-tools-249.0 tools
```

Note. 5.1.0-sdk_build_with_7.3_toohchain_for_p2.diff: Extra patch for SDK510 patch2 ← patch error !!!

## Additional Patch for new GNU tools

* download m4-1.4.18

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux/embedded_rootfs/storage
wget https://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.gz
```

* patch pkg_makefiles

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux/embedded_rootfs
patch -p0 < ../../gnu-patches/xxxx-pkt-makefiles.patch
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

Build Linux:

```shell
cd /usr/local/Cavium_Networks/OCTEON-SDK/linux  
make kernel
```

If the make report aclocal-1.15 or automake-1.15 not found, you might try this:

```shell
cd /usr/bin && ln -s aclocal aclocal-1.15
cd /usr/bin && ln -s automake automake-1.15
```


