gentoo on pcDuino

== Setting up a cross-compiler

	emerge -av crossdev

	crossdev -S -t armv7a-hardfloat-linux-gnueabi

== Download kernel and tools

	git clone https://github.com/pcduino/kernel.git

	cd kernel

	git clone --depth 1 git://github.com/linux-sunxi/linux-sunxi.git -b sunxi-3.4

	git clone --depth 1 https://github.com/linux-sunxi/u-boot-sunxi.git -b sunxi

	git clone --depth 1 git://git.denx.de/u-boot.git -b U-Boot-1_1_6

== Build u-boot mkimage

	cd u-boot
	touch include/config.mk
	make tools BIN_FILES=mkimage 	

== Building u-boot and the SPL

	cd u-boot-sunxi
	cat boards.cfg | grep pcDuino

	CROSS_COMPILE=armv7a-hardfloat-linux-gnueabi- make distclean
	CROSS_COMPILE=armv7a-hardfloat-linux-gnueabi- make pcDuino_config
	CROSS_COMPILE=armv7a-hardfloat-linux-gnueabi- make

	u-boot-sunxi-with-spl.bin
	spl/sunxi-spl.bin
	u-boot.img

== Building the Linux kernel

	cd linux-sunxi
	CROSS_COMPILE=armv7a-hardfloat-linux-gnueabi- ARCH=arm make sun4i_defconfig
	CROSS_COMPILE=armv7a-hardfloat-linux-gnueabi- ARCH=arm make -j3 uImage modules

== Installing the kernel

	mount ${bootfs} /mnt/gentoo/boot
	cp arch/arm/boot/uImage /mnt/gentoo/boot/
	CROSS_COMPILE=armv7a-hardfloat-linux-gnueabi- ARCH=arm make INSTALL_MOD_PATH=/mnt/gentoo modules_install
