# U-Boot
* mk.uboot to compile (edit `BOARD` and `CROSS_COMPILE` vars)

# OpenOCD Flash U-Boot

Via Mix of Hard And Soft[[1](http://hardsoftmix.blogspot.com/2011/07/jtag-on-iconnect.html)][[2](http://hardsoftmix.blogspot.com/2012/03/patch-u-boot-for-iconnect.html)].

* openocd -f openocd_iconnect.cfg and allow to remain running
* telnet localhost 4444 and run:
```
init
init2
init4
iconnect_reset_cpu
nand probe 0
nand list
nand erase 0 0x0 0xc0000
nand info 0 0 10
nand write 0 iconnect.kwb 0 oob_softecc_kw
```

# Make board image
* compile a Kirkwood linux/
* mk.pde to extract a pdebuild into dist/
* mk.pde.configure to perform configuration of image (set temporary root password here)
* mk.uimage to build a uImage from linux/
* mk.fdt to copy in dist/boot/fdt from linux/arch/arm/boot/dts/kirkwood
* mk.ubifs
* mk.ubi
* Copy final ubi.img to tftp server.

# Flash
* nand erase.part root
* tftpboot ${fileaddr} ubi.img
* wait for finish, reports hex byte size:
  Bytes transferred = 181403648 (ad00000 hex)
* nand write ${fileaddr} 0xa0000 [hex size, above, ad00000]

# Boot
```
ubi part root
ubifsmount ubi0
ubifsload ${fileaddr} /boot/kirkwood-iconnect.its
bootm
```

or

```
ubi part root;
ubifsmount ubi0;
ubifsload ${fileaddr} /boot/uImage;
ubifsload ${fdt_addr_r} /boot/kirkwood-iconnect.dtb;
bootm ${fileaddr} - ${fdt_addr_r} 
```

# Bootargs

```
setenv fdr_addr_r=0x1100000
setenv fdt_file=/boot/kirkwood-iconnect.dtb
setenv kernel=/boot/uImage
setenv bootargs_root 'ubi.mtd=2 root=ubi0:root rootfstype=ubifs noinitrd rw'
setenv bootargs_verbose 'systemd.log_level=debug verbose'
setenv bootargs_systemd 'init=/lib/systemd/systemd systemd.log_target=kmsg'
setenv go_bootargs 'setenv bootargs ${console} ${bootargs_root} ${bootargs_systemd} ${bootargs_verbose}'
setenv mtdparts mtdparts=orion_nand:0x80000@0x0(uboot),0x20000@0x80000(uboot_env),-@0xa0000(root)
setenv boot_root 'ubi part root; ubifsmount ubi0;'
setenv boot_load 'ubifsload ${fileaddr} ${kernel}; ubifsload ${fdr_addr_r} ${fdt_file};'
setenv boot_run 'bootm ${fileaddr} - ${fdr_addr_r};'
setenv bootcmd 'run go_bootargs; run boot_root; run boot_load; run boot_run;'
```

# Misc

```
fdt addr ${fdt_addr_r}; fdt list /; fdt list /ocp@f1000000/nand@3000000/partition@a0000
```

# Bad FITS

lol.

Starting kernel ...

| |°|À|Ð|à|ð|Ð{à{ð{ 0@P`p =>

