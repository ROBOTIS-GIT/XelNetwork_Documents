# OpenSoftware

## Git Repositories
- [commXel](https://github.com/ROBOTIS-GIT/XelNetwork_CommXel)
- [sensorXel](https://github.com/ROBOTIS-GIT/XelNetwork_SensorXel)
- [powerXel](https://github.com/ROBOTIS-GIT/XelNetwork_PowerXel)

<br>

## Getting Start
#### Install microRTPSAgent
Please refer to [this link](https://micro-rtps.readthedocs.io/en/latest/installation.html#installing-the-agent-stand-alone) for detail install information.

- Install microRTPSAgent stand alone on your SBC or PC. (Linux)
```bash
$ sudo apt update
$ sudo apt install build-essential cmake git

# If you have ROS2 installed on your PC, you do not need to install FastRTPS.
$ git clone https://github.com/eProsima/Fast-RTPS
$ mkdir Fast-RTPS/build && cd Fast-RTPS/build
$ cmake -DTHIRDPARTY=ON ..
$ sudo make install
$ cd ../..

$ git clone https://github.com/eProsima/micro-RTPS-agent.git
$ mkdir micro-RTPS-agent/build && cd micro-RTPS-agent/build
$ cmake -DTHIRDPARTY=ON -DCONFIG_UDP_TRANSPORT_MTU=4096 ..
$ sudo make install
$ cp ./DEFAULT_FASTRTPS_PROFILES.xml ~/
$ sudo ldconfig /usr/local/lib/
```

#### Run XelNetwork
- Run microRTPSAgent
```bash
$ cd
$ MicroRTPSAgent udp 2018
```
- Connect power to powerXel
- Connect commXel to powerXel
- Power on powerXel
- Connect sensorXels

#### Check topics using ROS2 or DDS application
```bash
#If you use ROS2
$ ros2 topic list -t
```

#### Change Xel's information using XelManager(GUI)
With XelManager, you can change the Xel ID, change the name, type, and data type of the topic.
(comming soon)

<br>

## Development
#### Install & Setting IDE (EclipseCDT)
- **Dependencies**
	- JAVA JDK (openjdk or OracleJAVA)
	
- [**GNU ARM ToolChain**](http://gnuarmeclipse.github.io/toolchain/install/#gnulinux)
```bash
$ mkdir ~/tools && cd ~/tools
$ wget https://launchpad.net/gcc-arm-embedded/5.0/5-2016-q2-update/+download/gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
$ tar -xvjf gcc-arm-none-eabi-5_4-2016q2-20160622-linux.tar.bz2
$ ./gcc-arm-none-eabi-5_4-2016q2/bin/arm-none-eabi-gcc --version
```

- [**OpenOCD**](http://gnuarmeclipse.github.io/openocd/install/)
	- Install OpenOCD
```bash
$ cd ~/tools
$ wget https://github.com/gnuarmeclipse/openocd/releases/download/gae-0.10.0-20170124/gnuarmeclipse-openocd-debian64-0.10.0-201701241841.tgz
$ tar xvf gnuarmeclipse-openocd-debian64-0.10.0-201701241841.tgz
$ ./openocd/0.10.0-201701241841/bin/openocd --version
$ sudo cp ./openocd/0.10.0-201701241841/contrib/60-openocd.rules /etc/udev/rules.d
$ sudo udevadm control --reload-rules
$ sudo udevadm trigger
```

- **Eclipse CDT**
	- [Download Eclipse CDT](http://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-cc-developers)
	- Extract download file to the location you want.

- [**GNU ARM Eclipse Plugin**]((gnuarmeclipse.github.io/plugins/install/) )
	- Click [Help] - [Install New Software] on the top menu of Eclipse.
	- In the pop-up window, click [Add ... ] Button.
	- Enter the following in the [Add Repository] popup window.
		- Name : GNU ARM Eclipse Plug-ins
		- Location : http://gnuarmeclipse.sourceforge.net/updates
	- Check all and install GNU ARM C/C++ Cross Develoment Tools
	- When you install it, the warning "unsigned content" appears in the middle of the installation. If so, press the [OK] button to proceed with the installation.


- **Eclipse Setting**
	- Set Global Toolchain path
		- Click [Windows] - [Preferences] on the top menu
		- Click [C/C++] - [Build] - [Workspace Tools] on the left menu of the popup window
		- Enter Path as shown below.
			- Toolchain folder: ~/tools/gcc-arm-none-eabi-5_4-2016q2/bin		
	- Set OpenOCD path
		- Click [Windows] - [Preferences] on the top menu
		- Click [Run / Debug] - [OpenOCD] on the left menu of the popup window
		- Enter Path as shown below.
			- Executable: openocd
			- Folder: ~/tools/openocd/0.10.0-201701241841/bin
		- Click [Apply]
	- Set String Substitution for ExternalTool argument
		- Click [Windows] - [Preferences] on the top menu
		- Click [Run / Debug] - [String Substitution] on the left menu of the popup window
		- Click [New..] button and enter variable, value as shown below.
			- Name: COM_PORT
			- Value: /dev/ttyACM0
		
#### Get Xel's source from git
```bash
$ cd && mkdir -p ~/workspace/source && cd ~/workspace/source
$ git clone --recursive https://github.com/ROBOTIS-GIT/XelNetwork_CommXel.git -b develop
$ git clone --recursive https://github.com/ROBOTIS-GIT/XelNetwork_SensorXel.git -b develop
$ git clone --recursive https://github.com/ROBOTIS-GIT/XelNetwork_PowerXel.git -b develop
```

#### Import Xel's project to EclipseCDT
- Click [File] - [Import..] on the top menu
- Click [Existing Projects into Workspace] in [General], click [Next >] button.
- Enter path as shown below.
	- Select root directory: ~/workspace/source
- Select projects list below in [Projects:]
	- commXel_app
	- commXel_boot
	- sensorXel_app
	- sensorXel_boot
	- powerXel_app
	- powerXel_boot
- Click [Finish]

#### Introduce source tree
(comming soon)

	
	