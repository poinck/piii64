# Readme: piii64
64-bit Raspberry Pi 3 binaries and configs (backup of my progress)

## Cross Compiler for arm64/aarch64
- gcc 4.9.3 won't work, switch to 5.3.0
- get a cross compiler toolchain, on Gentoo:
```.sh
crossdev -S --g 5.3.0 -t aarch64-unknown-linux-gnu
```

## U-Boot
**Compile 64-bit U-Boot**
- use these U-Boot developer sources: [branch rpi_dev](https://github.com/swarren/u-boot.git), maybe after the next regular release in fall 2016 we can switch back to mainline
```.sh
ARCH="arm64" CROSS_COMPILE="aarch64-unknown-linux-gnu-" make rpi_3_defconfig
ARCH="arm64" CROSS_COMPILE="aarch64-unknown-linux-gnu-" make
```
- we will need file 'u-boot.bin' in the next step

**Get U-Boot working**
- create a msdos partition table on a sd card
- create a small boot partition formatted in vfat
- copy official raspberry firmware files 'bootcode.bin' and 'start.elf' from [raspberrypi/firmware](https://github.com/raspberrypi/firmware/boot) into the boot partition
- create a config.txt file that will be read by the firmware and add the following entries (for more fine tuning have a look into the [official documentation](https://github.com/raspberrypi/documentation/blob/master/configuration/config-txt.md)):
```.sh
arm_control=0x200
kernel=u-boot.bin
```
- copy the 'u-boot.bin' file we have created into the boot partition

## Kernel
**Compile 64-bit kernel (Gentoo)**
- Kernel sources: [bcm2837-64-next](https://github.com/anholt/linux/tree/bcm2837-64-next)
- see "`kernel/.config`" for kernel configuration
```.sh
ARCH="arm64" CROSS_COMPILE="aarch64-unknown-linux-gnu-" make -j2
```
- copy kernel image file 'Image' and device tree blob file 'bcm2837-rpi-3-b.dtb' into the boot partition

**Configure U-Boot to boot the kernel**
- create U-Boot script file 'boot.scr':
```.sh
mmc dev 0
setenv fdtfile bcm2837-rpi-3-b.dtb
setenv bootargs earlyprintk console=tty0 console=ttyAMA0 root=/dev/mmcblk0p2 rootwait
fatload mmc 0:1 ${loadaddr} image
fatload mmc 0:1 ${fdt_addr_r} ${fdtfile}
booti ${loadaddr} - ${fdt_addr_r}
```
- make it readable for U-Boot:
```.sh
mkimage -A arm -O linux -T script -C none -n boot.scr -d boot.scr boot.scr.uimg
```
- add file 'boot.scr.uimg' to the boot partition

## aarch64 Userland
```.sh
TO DO, maybe we can use the experimental arm64/aarch64 stage 3 archive from Gentoo
```
