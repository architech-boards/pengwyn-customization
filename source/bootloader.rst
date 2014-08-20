U-boot
======

This chapter explains how to compile the u-boot.

Get the sources
---------------

The bootloader used by @board@ board is **U-Boot**. If you need to modify the bootloader or
to recompile it you have two ways to get the sources:

* use the sources you find in Yocto build directory after having compiled at least once a yocto image, or
* download the official u-boot release, than patch it with the BSP patches for @board@ board.

Anyway, we will assume in this guide that u-boot sources will be copied to:

.. host::

 | /home/@user@/Documents/u-boot

and such directory does not yet exists on your PC.
Of course, you are free to choose the path you like the most for u-boot sources, just remember
to replace the path used in this guide with your custom path.
So, where can we get the sources?

1. From Yocto sources

The first way is based on the sources set up by the Yocto build system. However, it is never
advisable to work with the sources in the Yocto build directory, if you really want to modify
the source code inside the Yocto environment we strongly suggest to refer to the official Yocto
documentation. To avoid messing up Yocto recipes and installation, it is desirable to copy the
patched u-boot sources you find in the build directory elsewhere. The directory we are talking
about is this one:

.. host::

 | /home/@user@/architech_sdk/architech/@board-alias@/yocto/build/tmp/work/@machine-name@-poky-linux-@eabi@/u-boot-ti-staging/2013.10-r8/git/

Replace:

.. host::

 | /home/@user@/architech_sdk/architech/@board-alias@/yocto/build/

all over this chapter with your custom build directory path if you are not working with the default SDK 
build directory.

2. From the official u-boot release

The second way is to get the official U-Boot sources and patch them with @board@ BSP patches.
@board@ board uses U-Boot version 2013.04, which can be downloaded from:

.. host::

 | cd /home/@user@/Documents
 | git clone -b ti-u-boot-2013.10 git://git.ti.com/ti-u-boot/ti-u-boot.git
 | mv ti-u-boot u-boot
 | cd u-boot
 | git checkout 78d8ebd4a0214b72a125f5b98c5ed2f9a3e5e783

.. _crosstoolchain:

The cross-toolchain
-------------------

| In order to build the u-boot you need install the toolchain in */opt/poky*. 
| You can build the toolchain or download it from our server.

The most comfortable way to build the toolchain is to ask *Bitbake* for it:

.. host::

 | cd /path/to/yocto/directory
 | source poky/oe-init-build-env
 | bitbake meta-toolchain

When *Bitbake* finishes, you find an installer script under directory:

.. host::

 | /path/to/yocto/directory/build/tmp/deploy/sdk/

If you prefer download the same file already compiled from bitbake, you can download here `poky-eglibc-i686-meta-toolchain-armv7a-vfp-neon-toolchain-1.5.3.sh <http://downloads.architechboards.com/pengwyn/toolchain/dora/1/bin/poky-eglibc-i686-meta-toolchain-armv7a-vfp-neon-toolchain-1.5.3.sh>`_ .

.. note::

  Install the toolchain in default directory (*/opt/poky/1.5.3*)

Run the script installer and you get, under the installation directory */opt/poky/1.5.3/*, a script to *source* to get your environment
almost in place for compiling. The name of the script is:

.. host::

 | environment-setup-armv7a-vfp-neon-poky-linux-@eabi@

Anyway, the environment is not quite right for compiling the bootloader and the Linux kernel, you need to unset
a few variables first to get it ready:

.. host::

 | unset CFLAGS CPPFLAGS CXXFLAGS LDFLAGS

Here you go, you now have the proper working environment to compile *u-boot* (or the Linux kernel).

Build U-boot
------------

Patches are in the Yocto meta-layer **@meta-layer@**. You can use them right away if you are working with the SDK:

.. host::

 | patch -p1 -d /home/@user@/Documents/u-boot < /home/@user@/architech_sdk/architech/@board-alias@/yocto/@meta-layer@/recipes-bsp/u-boot/u-boot-ti-staging-2013.10/0001-pengwyn.patch

However, if you are not working with the official SDK the most general solution to check them out and patch the sources is:

.. host::

 | cd /home/@user@/Documents
 | git clone -b dora https://github.com/architech-boards/@meta-layer@.git 
 | patch -p1 -d /home/@user@/Documents/u-boot < /home/@user@/Documents/@meta-layer@/recipes-bsp/u-boot/u-boot-ti-staging-2013.10/0001-pengwyn.patch

Configuration and board files for @board@ board are in:

.. host::

 | /home/@user@/Documents/u-boot/board/ti/am335x/*
 | /home/@user@/Documents/u-boot/include/configs/pengwyn.h

Suppose you modified something and you wanted to recompile the sources to test your patches, well, you
need a cross-toolchain (see :ref:`crosstoolchain` Section). To use it to compile the bootloader 
or the operating system kernel run:

.. host::

 | source /opt/poky/1.5.3/environment-setup-armv7a-vfp-neon-poky-linux-@eabi@
 | unset CFLAGS CPPFLAGS CXXFLAGS LDFLAGS

then you can run these commands to compile it:

.. host::

 | cd /home/@user@/Documents/u-boot/
 | make @machine-name@_config
 | make -j <2 * number of processor's cores> @machine-name@



Once the build process completes, you can find *u-boot.img* and *MLO* file inside directory */home/@user@/Documents/u-boot*.

