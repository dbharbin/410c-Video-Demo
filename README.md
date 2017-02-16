#Qualcomm Dragonboard 410c video hardware acceleration demo
This repo contains the scripts along with the instructions for running a comparison of h/w vs s/w video accelaration on the DB410c.  

## Configuration
Two configurations were used; one with software video decode and one with hardware video acceleration enabled.  

**Note:** the scripts herein point to a demo video that is not part of this repo `DroneVideoLoop2017.mp4`.  Put your own test file in place of this if have a different test file. 

### Software video decode configuration
This was done by installing the Debian build 144 prebuilt on the DB410c.

**Hardware Config**
The DB410c only needs a USB keyboard/mouse connected to USB port, an HDMI cable connected betweent the HDMI Port and a monitor, and the power cable for the board.

**Software Install**

* The build used is located [here](http://builds.96boards.org/releases/dragonboard410c/linaro/debian/16.09/).  This is Debian build 144.
* The installations instructions are [here](http://www.96boards.org/db410c-getting-started/Installation/README.md/).   You can use the SD Card method.
* to run the automation script, `apt-get install wmctrl` needs executed after base OS is installed.
	* wmctrl allows the script to place the Top terminal on top of the video during the video.  It could be done manually in the menu of the Terminal window as well, but wanted to make it one command to start the whole demo


#### Start the Demo
Navigate ot the directory that the DEB_RunDemo script from this repo has been downloaded to and run it.

`./DEB_RunDemo`

**Note:** If a video test file of a different length is used, the user must edit `DEB_RunDemo` and change the `videolength` variable in the script to match the length of the test file in seconds.

### Hardware video decode configuration
The hardware decode is still being upstreamed an is available in the Open Embedded build for the DB410c at the time of this writing.


**Hardware Config**

Plug a USB keyboard/mouse to the 410c USB port and an HDMI cable between the 410c HDMI Port and a monitor.

Plug in the [UART Adaptor Board](http://www.96boards.org/product/uarts/).  Connect a micro-USB to USB cable from the UART Adaptor board to your linux development host. 

From your linux host development machine, open a terminal and connect to the target with a terminal emulation program. For example:

* `sudo screen /dev/ttyUSB0 115200`

**Software Config**

The demo used build 27 located [here](http://builds.96boards.org/snapshots/reference-platform/openembedded/morty/dragonboard-410c/rpb/27/)

*	use the fastboot method shown [here](http://www.96boards.org/db410c-getting-started/Installation/README.md/)  to install the following files:
	* Rootfs [here](http://builds.96boards.org/snapshots/reference-platform/openembedded/morty/dragonboard-410c/rpb/27/rpb-desktop-image-dragonboard-410c-20170203080749-27.rootfs.img.gz)
	* Boot image [here](http://builds.96boards.org/snapshots/reference-platform/openembedded/morty/dragonboard-410c/rpb/27/boot--4.4-r0-dragonboard-410c-20170203080749-27-27.img)
* Copy the `OE_RunDemo` script from this repo to the 410c


#### Start the demo

Power up the target with the software configured per above.  Since this is an OE build, the typical desktop will not appear on the HDMI monitor.  Instead a black screen with just the navigation arrow will be on the screen.

* left click the mouse connected to the DB410c and open an XTerm
* Execute the `OE_RunDemo` script from the command line.

**Using Top**
You can either start Top to monitor CPU performance from the Terminal emulator or prior to starting the Demo script you could open a second terminal, run Top, and tell that terminal to stay on top through `right click-layer-Always on top` sequence.

##Other notes and commands
Investigating the startup scripts, notice the use of `cpufreq-set` command and its modes.  This should be set to the desired operational mode in order to get consistant/desirabed results.  

To change cpu frequency

* `cpufreq-set -g powersave`   // lowers to 200mHz
* `cpufreq-set -g performance` // up to 1.2gHz
* `cpufree-set -g ondemand`  // on demand
* Should play w/ H/W Acceleration in powersave
* `cpufreq-info`   // shows current settings

Use of `xset` was required in the OE build to keep the monitor / demo from going to sleep.  See the `OE_RunDemo` script for details.

* `DISPLAY=:0 xset q` to see the sleep settings.

In the OE (hardware video acceleration) demo, the following command can be used to verify that things are working well and interrupts are occuring

* `cat /proc/interrupts|grep venus`

\<end\>
