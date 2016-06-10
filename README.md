# Readme: piii64
64bit raspberry pi 3 binaries and configs (backup of my progress)

## Create U-Boot image
```.sh
mkimage -A arm -O linux -T script -C none -n boot.scr -d boot.scr boot.scr.uimg
```

## Compile 64-bit kernel (Gentoo)
see "`kernel/.config`" for kernel configuration
```.sh
ARCH="arm64" CROSS_COMPILE="aarch64-unknown-linux-gnu-" make -j2
```

- gcc version: 5.3.0

**Kernel sources:**
[bcm2837-64-next](https://github.com/anholt/linux/tree/bcm2837-64-next)

