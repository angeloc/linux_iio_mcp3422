# MCP3422/3/4 Linux Kernel driver

This is an out of tree kernel driver for Microchip's  MCP3422, MCP3423, MCP3424 high resolution ADC.

This driver is supports only kernel less than 3.10, for 3.11 onward this driver is in kernel tree.

This driver can be used on Raspberry Pi with ADC Pi v2 or DeltaSigma Pi extensions boards from [Abelectronics](http://www.abelectronics.co.uk).

## HOWTO cross compile for the Raspberry Pi

1. Download this repository

	~/$ git clone https://github.com/angeloc/linux_iio_mcp3422.git

2. Download latest 3.10 kernel for the Raspberry Pi from here [3.10.y](https://github.com/raspberrypi/linux/archive/rpi-3.10.y.zip).

3. Make a new default config for the Raspberry Pi:

	~/linux-rpi-3.10.y$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcmrpi_defconfig

4. Enable IIO subsystem:
	
	~/linux-rpi-3.10.y$ sed -i "s/CONFIG_IIO=n/CONFIG_IIO=y/" .config

5. Recompile your kernel:

	~/linux-rpi-3.10.y$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- make

6. Install uImage and module in the correct folders

7. Compile mcp3422 driver:

	~/linux_iio_mcp3422$ make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf-

8. Copy *mcp3422.ko* file into /lib/modules/3.10.y/drivers/iio/adc

9. Boot your Raspberry Pi and enable your MCP3422 device (replace 0x68 with the address of your chip on the bus):

	~/# insmod /lib/modules/3.10.y/drivers/iio/adc/mcp3422.c

	~/# echo 0x68 > /sys/bus/i2c/devices/i2c-0/new_device

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
~/iio:device0# cat in_voltage0_scale
2.047000000
~/iio:device0# echo 3 > in_voltage_sampling_frequency
~/iio:device0# cat in_voltage0_scale
2.047984375	
```

For any comment/suggestion, please mail me at <angelo.compagnucci@gmail.com>.