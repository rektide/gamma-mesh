# U-Boot
* mk.uboot to compile (edit `BOARD` and `CROSS_COMPILE` vars)

# Image
* compile a Kirkwood linux/
* mk.pde to extract a pdebuild into dist/
* mk.uimage to build a uImage from linux/
* mk.fdt to copy in dist/boot/fdt from linux/arch/arm/boot/dts/kirkwood
* mk.ubifs
* mk.ubi
* Copy final ubi.img to tftp server.

# Serve

* nand erase.part rootfs
* tftpboot 0x800000 ubi.img
* wait for finish, reports hex byte size:
  Bytes transferred = 181403648 (ad00000 hex)
* nand write 0x800000 0xa000 [hex size, above, ad00000]
