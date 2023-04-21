# ch347_openocd-0.12.0-rc3

For build the openocd application is need to use libch347.so library present in

CH347Par_Linux_V1.02_HID.zip downloaded from:

https://github.com/wuxx/USB-HS-Bridge/tree/master/doc

Also the linux driver (if needed) is present there, I have patched the all warning
and I have released today in:

https://github.com/darioia/mych347



******* Not tested on target jet *******

# Changes steps:

1) Download openOcd 0.12.0 source design base openocd (..../openocd-0.12.0-rc3) from official repository

     openocd-0.12.0-rc3

2) Downloaded from https://github.com/WCHSoftGroup/ch347/tree/main/OpenOCD_CH347 the following files: 

     ch347_jtag.c and ch347.cfg
     
   and from https://github.com/wuxx/USB-HS-Bridge.git the file
   
     CH347DLL_EN.H
     
   Copyed that following files:
   
     ..../OpenOCD_CH347/ch347_jtag.c  -> .../openocd-0.12.0-rc3/src/jtag/drivers/ch347_jtag.c
     
     ..../OpenOCD_CH347/bin/ch347.cfg -> .../openocd-0.12.0-rc3/tcl/interface
     
     ..../USB-HS-Bridge/doc/CH347EVT/EVT/TOOLS/CH347Demo/ExternalLib/CH347DLL_EN.H -> .../openocd-0.12.0-rc3/src/jtag/drivers/ch347dll.h
     

3) Fixed all bugs and warning cleanup present in ch347_jtag.c and ch347dll.h

4) Changed .../openocd-0.12.0-rc3/src/jtag/drivers/Makefile.am adding at the end of old drivers the following lines:

     DRIVERFILES += %D%/ch347_jtag.c
     
     %C%_libocdjtagdrivers_la_LIBADD += -lch347

5) Changed .../openocd-0.12.0-rc3/src/jtag/interfaces.c adding at the end of old drivers the following lines:

     extern struct adapter_driver ch347_adapter_driver;
     
     &ch347_adapter_driver,

6) Added in .../openocd-0.12.0-rc3/contrib/60-openocd.rules the following lines:

     \# Espressif USB JTAG/serial debug units
     
     ATTRS{idVendor}=="303a", ATTRS{idProduct}=="1001", MODE="660", GROUP="plugdev", TAG+="uaccess"
     
     ATTRS{idVendor}=="303a", ATTRS{idProduct}=="1002", MODE="660", GROUP="plugdev", TAG+="uaccess"
     
  copy this file in /etc/udev/rules.d/
  
6.a) Added in /etc/udev/rules.d//99-ch341a-prog.rules

     KERNEL=="hidraw*", SUBSYSTEM=="hidraw", ATTRS{idVendor}=="1a86", ATTRS{idProduct}=="55dd", MODE="0664", GROUP="plugdev"
     
     (optional, to be verify)
 
     
# Build 

Build openocd for all interface handled:

     ./configure  --enable-malloc-logging --enable-dummy --enable-rshim --enable-ftdi --enable-stlink --enable-ti-icdi \
     --enable-ulink --enable-usb-blaster-2 --enable-ft232r --enable-vsllink --enable-xds110 --enable-cmsis-dap-v2 --enable-osbdm \
     --enable-opendous --enable-armjtagew --enable-rlink --enable-usbprog --enable-esp-usb-jtag --enable-cmsis-dap --enable-nulink \
     --enable-kitprog --enable-usb-blaster --enable-presto --enable-openjtag --enable-buspirate --enable-jlink --enable-aice \
     --enable-parport --enable-parport-ppdev --enable-parport-giveio --enable-jtag_vpi --enable-vdebug --enable-jtag_dpi \
     --enable-amtjtagaccel --enable-bcm2835gpio --enable-imx_gpio --enable-am335xgpio --enable-ep93xx --enable-at91rm9200 \
     --enable-gw16012 --enable-sysfsgpio --enable-xlnx-pcie-xvc --enable-internal-jimtcl --enable-jimtcl-maintainer \
     --enable-internal-libjaylink --enable-remote-bitbang
     
     make -j 9 all
     
     sudo make install

