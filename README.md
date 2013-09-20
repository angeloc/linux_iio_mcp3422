# MCP3422/3/4 Linux Kernel driver

This is an out of tree kernel driver for Microchip's  MCP3422, MCP3423, MCP3424 high resolution ADC.

This driver supports only kernel release less than 3.12, for 3.13 onward this driver is in kernel tree.

To use the driver on Linux <= 3.10 stay on master branch, if you have a kernel >= 3.11 switch to 3.11 branch.

This driver can be used on Raspberry Pi with ADC Pi v2 or DeltaSigma Pi extensions boards from [Abelectronics](http://www.abelectronics.co.uk).

## HOWTO cross compile for the Raspberry Pi

1. Download this repository

	```bash
	~/$ git clone https://github.com/angeloc/linux_iio_mcp3422.git
	```

2. Download latest 3.10 kernel for the Raspberry Pi from here [3.10.y](https://github.com/raspberrypi/linux/archive/rpi-3.10.y.zip) and unzip it somewhere.

3. Make a new default config for the Raspberry Pi:

	```bash
	~/linux-rpi-3.10.y$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-  make bcmrpi_defconfig
	```

4. Enable IIO subsystem:

	```bash	
	~/linux-rpi-3.10.y$ ./scripts/config --enable I2C; \
	./scripts/config --enable IIO; \
	./scripts/config --enable I2C_BCM2708; \
	./scripts/config --set-val I2C_BCM2708_BAUDRATE 400000; \
	 ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-  make prepare scripts
	```

Press Enter for each unsetted option until the kernel begins to compile.

5. Recompile your kernel:

	```bash
	~/linux-rpi-3.10.y$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-  make
	```

6. Replace the raspberry pi kernel /media/sdcardp1/kernel.img with your new kernel file arch/arm/boot/Image.

7. Install kernel modules you compiled with:

	```bash
	~/linux_iio_mcp3422$ ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make modules_install INSTALL_MOD_PATH=/media/sdcardp2/
	```

8. Compile mcp3422 driver:

	```bash
	~/linux_iio_mcp3422$ KERNELDIR=~/linux-rpi-3.10.y ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make
	```

9. Copy *mcp3422.ko* file into /media/sdcardp2/lib/modules/3.10.y/drivers/iio/adc

10. Boot your Raspberry Pi and enable your MCP3422 device (replace 0x68 with the address of your chip on the bus):

	```bash
	~/# insmod /lib/modules/3.10.y/drivers/iio/adc/mcp3422.ko
	~/# echo 0x68 > /sys/bus/i2c/devices/i2c-0/new_device
	```

10. Use your new device reading files in:

	```bash
	~/# cd /sys/bus/i2c/devices/0-0068/iio\:device0/
	~/iio:device0# ls
	in_voltage0_raw
	in_voltage0_scale
	in_voltage1_raw
	in_voltage1_scale
	in_voltage2_raw
	in_voltage2_scale
	in_voltage3_raw
	in_voltage3_scale
	in_voltage_sampling_frequency
	in_voltage_scale_available
	~/iio:device0# cat in_voltage0_raw
	2047
	~/iio:device0# cat in_voltage0_scale
	0.001000000
	```
