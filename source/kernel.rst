Linux Kernel
============

Like we saw for the :ref:`bootloader <bsp_bootloader_label>`, the first thing you need is: sources.
Get them from *Bitbake* build directory (if you built the kernel with it) or get them from the Internet.

*Bitbake* will place the sources under directory:

.. host::

 | /path/to/build/tmp/work/pengwyn-poky-linux-gnueabi/linux-ti-staging/3.12.17-r22a/git


If you are working with the virtual machine, you will find them under directory:

.. host::

 | /home/@user@/architech_sdk/architech/@board-alias@/yocto/build/tmp/work/pengwyn-poky-linux-gnueabi/linux-ti-staging/3.12.17-r22a+XXX/git

| XXX is a random code assigned by bitbake.
| We suggest you to **don't work under Bitbake build directory**, you will pay a speed penalty and you could have troubles syncronizing the all thing. Just copy them some place else and do what you have to do.

If you didn't build them already with *Bitbake* or you just want to do make every step by hand, you can
always get them from the Internet by cloning the proper repository and checking out the proper hash commit:

.. host::

 | cd ~/Documents
 | git clone git://git.ti.com/ti-linux-kernel/ti-linux-kernel.git
 | cd ti-linux-kernel
 | git checkout f0d4672333685697320f4907d5b4d3919121c299

and by properly patching the sources:

.. host::

 | cd ~/Documents
 | patch -p1 -d ti-linux-kernel/ < /home/@user@/architech_sdk/architech/@board-alias@/yocto/meta-pengwyn/recipes-kernel/linux/linux-ti-staging-3.12.17/0002-pengwyn.patch
 | cp /home/@user@/architech_sdk/architech/@board-alias@/yocto/meta-pengwyn/recipes-kernel/linux/linux-ti-staging-3.12.17/defconfig ti-linux-kernel/arch/arm/configs/pengwyn_defconfig

However, if you are not working with the official SDK the most general solution to check them out and patch the sources is:

.. host::

 | cd ~/Documents
 | git clone -b dora https://github.com/architech-boards/meta-pengwyn.git
 | git clone -b dora git://git.yoctoproject.org/meta-ti.git
 | patch -p1 -d ti-linux-kernel/ < meta-pengwyn/recipes-kernel/linux/linux-ti-staging-3.12.17/0002-pengwyn.patch
 | cp meta-pengwyn/recipes-kernel/linux/linux-ti-staging-3.12.17/defconfig ti-linux-kernel/arch/arm/configs/pengwyn_defconfig


Now that you have the sources, you can start browsing the code from the following files:

.. host::

 | ~/Documents/ti-linux-kernel/arch/arm/boot/dts/pengwyn-common.dtsi
 | ~/Documents/ti-linux-kernel/arch/arm/boot/dts/pengwyn-dvi.dts
 | ~/Documents/ti-linux-kernel/arch/arm/boot/dts/pengwyn-touch.dts

For build the kernel the cross-toolchain must be installed in */opt/poky/1.5.3* directory, see in :ref:`the cross-toolchain<crosstoolchain>` paragraph.
Source the script to load the proper environment for the cross-toolchain:

.. host::

 | source /opt/poky/1.5.3/environment-setup-armv7a-vfp-neon-poky-linux-@eabi@
 | unset CFLAGS CPPFLAGS CXXFLAGS LDFLAGS

and you are ready to customize the kernel:

.. host::

 | cd ~/Documents/ti-linux-kernel
 | make pengwyn_defconfig
 | make menuconfig

and to compile it:

.. host::

 | make -j <2 * number of processor's cores> uImage

If you omit *-j* parameter, *make* will run one task after the other, if you specify it *make* will parallelize
the tasks execution while respecting the dependencies between them.
Generally, you will place a value for *-j* parameter corresponding to the double of your processor's cores number,
for example, on a quad core machine you will place *-j 8*.

Once the kernel is compiled, the last build to do is the dtb file. This file permits at the boot time to configure the kernel with a specific hardware configuration. So if you are using a touchscreen you will build the *pengwyn-touch.dts* file else if you are using a display with dvi connector will be *pengwyn-dvi.dts* file. In the same directory where you have compiled the kernel launch the command:

.. host::

 | make pengwn-touch.dtb

or

.. host::

 | make pengwyn-dvi.dtb

By the end of the build process you will get *uImage* under *arch/arm/boot* and *pengwyn-touch.dtb* or *pengwyn-dvi.dtb* under *arch/arm/boot/dts* directories.

