Meta Layer
==========

A Yocto/OpenEmbedded meta-layer is a directory that contains recipes, configuration files, patches, etc., all needed by
*Bitbake* to properly "see" and build a BSP, a distribution, a (set of) package(s), whatever.
**@meta-layer@** is a meta-layer which defines the customizations to make to TI's AM335x BSP and Yocto/OpenEmbedded
in order to get a working system, tailor made of @board@.

You can get it with *git*:

.. host::

 | git clone -b @yocto-version@ https://github.com/architech-boards/@meta-layer@.git

The machine name for @board@ is **@machine-name@**.

The strictly BSP related recipes are located under:

.. host::

 | @meta-layer@/recipes-bsp/u-boot/
 | @meta-layer@/recipes-bsp/flash/
 | @meta-layer@/recipes-kernel/linux/

The other recipes are there just to customize other aspects of the system or to offer some facility to help you easily
manage some task, for example, working with flash memory or partitions.

@board@ is powered by a NAND memory, big enough to place a full featured root file system inside of it.
However, you might not be interested in how to place the file system inside of it from the beginning and how to mount and
unmount it inside your file system.
There is a recipe inside @meta-layer@, **pengwyn-flash-utils**, that will install three scripts inside the target file system
to make the aforementioned tasks easy:

* *pengwyn_to_flash*

* *pengwyn_mount_flash*

* *pengwyn_umount_flash*

*pengwyn_to_flash* takes as input files, cleans and formats the NAND flash memory, and finally takes the files you gave
him to setup the file system. For more information just run:

.. host::

 | pengwyn_to_flash -h

from @board@ shell.

*pengwyn_mount_flash* lets you mount the flash memory partition inside your filesystem (under */mnt/flash*) without any effort
and, likewise, *pengwyn_umount_flash* helps you unmounting the partition.

Remember that to install those scripts inside the target, you need to add **meta-openmbedded/meta-oe** meta layer to your *bblayers.conf* file. If you are working with Architech virtual machine, you don't have to worry about that, everything is already in place.

*pengwyn-flash-utils* won't be placed by default inside your file system, if you want it you need to add a line like this one
to your *local.conf* file

.. host::

 | IMAGE_INSTALL_append = " pengwyn-flash-utils"

Probably the most comfortable way, at least at the beginning, to build a valid SD card is to use file *.sdcard* that
*Bitbake* emits when builds an image. However, *Bitbake* prepares a final iso image to write to the medium without any knowledge of its size. If you write the image on an SD card, for example, the first thing you notice is that the file system does not fit the card.

