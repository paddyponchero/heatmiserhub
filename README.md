# heatmiserhub
Fixing Heatmiser Hub with Failed Micro SD Card

The older white heatmiser hub uses a micro sd card instead of flash memory.
After several years the SD card failed in my hub.
On investigation I discovered that the board is based on the imx233 chip smsc9500 usb ethernet.
It is largely similar to the Olimex olinuxino
https://www.olimex.com/Products/OLinuXino/iMX233/iMX233-OLinuXino-MAXI/open-source-hardware
lock Mode.
Steps to repair:

1) Connect to serial console.
Need a 3.3V serial adapter I used a modified ch341a I had already for programming eeproms.
This needs a tweak to get it to operate serial at 3.3V futher info here:
https://www.eevblog.com/forum/repair/ch341a-serial-memory-programmer-power-supply-fix/

Connect Pin 2 of the console connector to RXD on your serial 
Connect Pin 4 to TXD

The console runs at 115200bps 

I used putty to monitor the serial console.

After establishing a console connection

I inserted a new sd card into the hub and got the following code: 0x8020a007

Checking the Freescale Error Codes - this means ERROR_DDI_SD_CONFIG_BLOCK_NOT_FOUND

https://community.nxp.com/pwmxy87654/attachments/pwmxy87654/imx-processors/54683/1/IMX23_ROM_Error_Codes.pdf

Reading the reference manual I deduced that the chip is programmed to operate in Boot Control Block Mode

A special code should be written to the final 512kb of the disk to tell the controller where the boot code started. 

https://www.nxp.com/docs/en/reference-manual/IMX23RM.pdf

The boot partition should also be of a special type: type 53 you can do this using fdisk

Type “p” to view existing partitions on the SD-card
Type “o” to delete all partitions
Type “n” to create new one
Set “1” as partition number
Type “p” to select primary partition
Press Enter to select default beginning sector
Type “+32M” to create 32MB partition
Type “t” to change partition type
Type “53”
Type “w” to write partitions to the SD-card


