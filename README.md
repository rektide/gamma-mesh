# U-Boot
* mk.uboot to compile (edit `BOARD` and `CROSS_COMPILE` vars)

# OpenOCD Flash U-Boot
* openocd -f openocd_iconnect.cfg and allow to remain running
* telnet localhost 4444 and run:
init
init2
init4
iconnect_reset_cpu
nand probe 0
nand list
nand erase 0 0x0 0xc0000
nand info 0 0 10
nand write 0 iconnect.kwb 0 oob_softecc_kw

# Make board image
* compile a Kirkwood linux/
* mk.pde to extract a pdebuild into dist/
* mk.uimage to build a uImage from linux/
* mk.fdt to copy in dist/boot/fdt from linux/arch/arm/boot/dts/kirkwood
* mk.ubifs
* mk.ubi
* Copy final ubi.img to tftp server.

# Flash

* nand erase.part rootfs
* tftpboot 0x800000 ubi.img
* wait for finish, reports hex byte size:
  Bytes transferred = 181403648 (ad00000 hex)
* nand write 0x800000 0xa0000 [hex size, above, ad00000]

# Boot

ubi part rootfs
ubifsmount ubi0
ubifsload 0x800000 /boot/kirkwood-iconnect.its
bootm

or

ubifsload 0x800000 /boot/uImage
ubifsload 0x1100000 /boot/kirkwood-iconnect.dtb
bootm


# Bad FITS

lol.

Starting kernel ...

| |°|À|Ð|à|ð|Ð{à{ð{ 0@P`p =>

