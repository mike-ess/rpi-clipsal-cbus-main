# Introduction

THIS IS A WORK IN PROGRESS, NOT FINISHED YET.

# Assumptions

The instructions below were created on a Raspberry Pi Model 3B with 16GB SD Card. If you use different hardware, you may experience difficulties due to differences in hardware, especially regarding Serial Interface configuration.

These instructions were created on Raspbian Stretch Lite, specifically image version **2018-06-27-raspbian-stretch-lite.img**.

# Get prepared

## Necessary Clipsal C-Bus Equipment

* A working [Clipsal C-Bus system with](https://www.clipsal.com/Trade/Products/Integrated-Systems/C-Bus-Control-and-Management-System)
* A working [Clipsal RS-232 PC Interface model 5500PC](https://www.clipsal.com/Trade/Products/ProductDetail?catno=5500PC) on the C-Bus Network..

## Necessary Raspberry Pi Equipment

* 1x Raspberry Pi Kit including:
* [Raspberry Pi Model 3B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/).
* 16GB micro SD card.
* Power supply.
* Raspberry Pi case.
* A USB mouse and USB keyboard. These are only required for initial setup, and are not be needed long term.

## Necessary Wiring Equipment

Additionally, some wiring will be required to connect the Raspberry Pi to the Clipsal Serial Connector. For this, you need:

* 1x [Cat5 cable](https://en.wikipedia.org/wiki/Category_5_cable), cut to a suitable length for your installation.
* 1x RS232 to TTL converter that utilises a [MAX3232](https://en.wikipedia.org/wiki/MAX232) chip. Easily found online for very small cost (approximately $1.00). Ideally find one that is just a board, with no connectors on either end. About the size of a US penny.
* 4x [Dupont wire](https://en.wikipedia.org/wiki/Jump_wire) with female on one end. What is on the other end does not matter, it will be cut off.
* 1x [RJ-45](https://en.wikipedia.org/wiki/Modular_connector#8P8C) Plug.
* Some short lengths of [Heat shrink](https://en.wikipedia.org/wiki/Heat-shrink_tubing), or [Electrical Tape](https://en.wikipedia.org/wiki/Electrical_tape).

![Photo of MAX3232](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/max3232-back.png "Photo of MAX3232")

![Photo of MAX3232](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/max3232-front.png "Photo of MAX3232")

## Necessary Tools

* A [Soldering Iron](https://en.wikipedia.org/wiki/Soldering_iron) and knowledge of how to use one, or a friend who can help you. 
* A sharp hobby knife.
* A [RJ-45](https://en.wikipedia.org/wiki/Modular_connector#8P8C) crimping tool.
* Some [Side cutters](https://en.wikipedia.org/wiki/Diagonal_pliers).

![Photo of Tools](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/tools.png "Photo of Tools")

# Serial Interface Wiring

## Clipsal Serial Connection Specification

The intention is to connect into the rear (i.e. permanent) serial connection on the C-Bus PC Interface, achieved through an RJ-45 connector. 

Specifications from the Clipsal documentation:

![Clipsal Documentation](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/clipsal-5500pc-rj45-pin-specifications.png "Clipsal Documentation")

![Clipsal Documentation](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/clipsal-5500pc-rj45-wiring-diagram.png "Clipsal Documentation")

The 9-pin connector on the front of the C-Bus PC Interface will not be used, because this is intended for temporary connection during initial installation.

## Raspberry Pi Serial Connection Specification

![Photo of GPIO Port on Raspberry Pi](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspberry-pi-pinout.png "Photo of GPIO Port on Raspberry Pi")

## Wiring Explanation

This is a simple serial implementation, requiring use of:

* Positive Voltage
* Ground (GND)
* Transmit (TX)
* Receive (RX)

The Positive Voltage differs on each device

* Raspberry Pi uses TTL, +5v
* Clipsal 5500OC uses RS232, +3.3V

The purpose of the MAX3232 is to convert between these two.

The wiring required for the TTL end (Raspberry Pi) is:

* GPIO Pin #2 to MAX3232 TTL Positive
* GPIO Pin #6 to MAX3232 TTL Ground
* GPIO Pin #8 to MAX3232 TTL Transmit (inbound arrow)
* GPIO Pin #10 to MAX3232 TTL Receieve (outbound arrow)

The wiring required for the TTL end (Raspberry Pi) is:

* RS232 RJ45 Pin #4 to MAX3232 TTL Negative
* RS232 RJ45 Pin #5 to MAX3232 TTL Receive (outbound arrow)
* RS232 RJ45 Pin #6 to MAX3232 TTL Transmit (inbound arrow)
* There is no Positive required on the RS232 end.

## Wiring Photos / Example

These pictures show it clearly, just follow the colours. Note the colours are **independant and unrelated** on each end of the MAX3232 circuit board.

RJ45 for Clipsal 5500PC:
![Photo of RJ45](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/cat5-complete.png "Photo of RJ45")

MAX3232 in the middle:
![Photo of MAX3232](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/max3232-back-wired.png "Photo of MAX3232")

Raspberry Pi:
![Photo of Raspberry Pi](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/r-pi-gpio.png "Photo of Raspberry Pi")

Wire everthing up as described above, using a [soldering iron](https://en.wikipedia.org/wiki/Soldering_iron) on the MAX3232 circuit.

For the RJ45 connector, use a crimping tool. Pay special attention to the RJ45 connector, to ensure the correct colour wires are placed into the correct slots, after crimping there is no way to change it without cutting the RJ45 plug and adding another. Also ensure the three wires are stripped to a suitable length, and the five unused wires are trimmed back.

![Photo before Crimping](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/cat5-length.png "Photo before Crimping")

![Photo before Crimping](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/cat5-aligned.png "Photo before Crimping")

![Photo of Crimping](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/cat5-crimp.png "Photo of Crimping")

![Photo of RJ45](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/cat5-complete.png "Photo of RJ45")

It is also recommended to cover the MAX3232 with heat shrink, or insultation tape, to avoid any problems with the circuits touching other electronics including the Raspberry Pi motherboard. The heat shrink is best put on the wires before the end plugs are attached.

You can also drill a 6mm hole in the case somewhere, near the edge, and cut away any residual plastic shavings with a hobby knife, to allow the Cat5 cable to cleanly exit the Raspberry Pi case.

The final product looks like this:

![Photo of Raspberry Pi](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/rpi-complete1.png "Photo of Raspberry Pi")

![Photo of Raspberry Pi](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/rpi-complete2.png "Photo of Raspberry Pi")

# Operating System Installation

Download Raspbian Stretch Lite image from <https://www.raspberrypi.org/downloads/raspbian/> . Download the zip version, and then extract the image file (with .img extension).

Write the image to your SD Card. A suitable tool for this purpose is Etcher, download from <https://etcher.io/>.

## Temporary Installation

Initially, connect the Raspberry Pi to a USB keyboard, USB mouse and any HDMI capable monitor or TV. If you are using a wired network connection, also connect to the network now.

**At this stage, do not connect the serial cable to the Clipsal C-Bus system. This is because the serial interface on the Rasberry Pi is not correctly configured yet.**

Power on the Raspberry Pi. When it loads, login with username=**pi** and password=**raspberry**.

## Initial setup

Type ```sudo raspi-config``` and then use the following instructions. Each instruction below expects you to begin navigation from the home menu.

### Password

Select option 1 to change the password for the **pi** user.  

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-main-menu.png "Menu Screenshot")

### Hostname

Select Option 2 > Option N1 to set the hostname. This should be a name that no other device on your network has (e.g. **rpi-cbus**)

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-2.png "Menu Screenshot")

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-n1.png "Menu Screenshot")

### Wi-Fi Connection

If your Raspberry Pi is intended to be connected to Wi-Fi, when select Option 2 > Option N2 and then enter the SSID of your network, and the password of your network. Then, Select Option 4 > Option I4 to select the right WiFi country.

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-2.png "Menu Screenshot")

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-n2.png "Menu Screenshot")

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-4.png "Menu Screenshot")

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-i4.png "Menu Screenshot")

### Localisation Settings

Select Option 4 > Option I2 to select your time zone.

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-4.png "Menu Screenshot")

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-i2.png "Menu Screenshot")

Select Option 4 > Option I3 to select your keyboard layout. This is important because the default is UK keyboard layout, which will not be right for many users. A US keyboard may be preferred.

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-4.png "Menu Screenshot")

![Menu Screenshot](https://github.com/mike-ess/rpi-clipsal-cbus-main/blob/master/images/raspi-config-option-i3.png "Menu Screenshot")

Exit **raspi-config**. If prompted to reboot, then do so, and then login again using your new credentials for the pi user.

### Fixed IP Address

Type ```sudo nano /etc/network/interfaces.d/eth0``` and edit the file contents as shown below. This example sets the IP address to 10.64.104.10 with a default gateway of 10.64.104.1 and a subnet mask of 255.255.255.0:
```
# Configure loopback
auto lo
iface lo inet loopback

# Configure network
auto eth0
iface eth0 inet static
address 10.64.104.10
gateway 10.64.104.1
netmask 255.255.255.0
network 10.64.104.0
broadcast 10.64.104.255
```

The new IP address will not take affect until the Raspberry Pi is next restarted.

### Serial Interface Settings

Type ```sudo systemctl disable hciuart``` to disable unwanted usage of the UART serial interface.

Type ```sudo nano /boot/config.txt``` to edit the config.txt file.

Add these lines into the file, to disable Bluetooth and enable the UART serial interface:
```
# Enable Serial Interface
enable_uart=1
# Disable Bluetooth
dtoverlay=pi3-disable-bt
```
Save and close the file.

Type ```sudo nano /boot/cmdline.txt``` to edit the cmdline.txt file.

Remove the section of text that says ```console=serial0,115200```, to disable console output to the serial interface.

Save and close the file.

### SSH client

Type `sudo apt-get -y install ssh` to install the SSH server. This may be used to connect to the Raspberry Pi using an SSH client such as Putty.

Type `sudo systemctl enable ssh.service` to enable the SSH server. 

### Firmware Update

Type these commands to update the firmware.

```
sudo rpi-update
```

### Restart

Reboot the Raspberry Pi by typing:

```
sudo shutdown -r now
``` 
Wait for the reboot, then login as the **pi**user again.

### Verify Configuration

At this point, the Raspberry Pi should be ready to become "headless", meaning you do not need a keyboard, mouse and monitor or TV.

If you wish, take this opportunity to login to the Raspberry Pi using an SSH client (such as [Putty](https://www.putty.org/) on a Windows machine). You will need to know the IP Address, username and password.

If you cannot login via SSH, check all previous steps (especially the IP Address and the SSH client) and make corrections, until you are ready.

If you can login OK via SSH, you are ready to go. Shut down the Raspberry Pi by typing ```sudo shutdown -h now``` and it will usually only take a few seconds to fully shut down.

## Permanent Installation

Now you can move the Raspberry Pi to its desired location, and you only need to plugin power, plus a network cable if you are not using Wi-Fi. 

**You are now ready to connect the Raspberry Pi to C-Bus.**

Power the Raspberry Pi up again, and login via SSH as the **pi** user.

## Further Setup

### Elevate to root

Type ```sudo bash``` to become root. All subsequent commands below will require root.

### Telnet Client

Type `sudo apt-get -y install telnet` to install the Telnet client. This is optional, however can be useful for troubleshooting as Telnet can be used to check what ports are open.

### Update Packages

Type `sudo apt-get update && sudo apt-get -y upgrade` to get Raspbian up to date with packages.

### Install Docker

Type `sudo bash -c "curl -sSL https://get.docker.com | sh"` to install Docker.

## Prepare Docker Containers

Each of the software components are created as Docker containers to make installation easier. The instructions will work through installation and configuration of all Docker containers, then startup of them all towards the end.

### ser2sock

Type `docker pull mikeess/rpi-ser2sock` to download the Docker image.

No configuration is required. Look in the [Dockerfile of ser2sock](https://github.com/mike-ess/rpi-ser2sock/blob/master/Dockerfile) for details of (a) what port number is used and (b) what device is used to connect to the serial interface on the Raspberry Pi.

### Clipsal C-Gate Software

Type `docker pull mikeess/rpi-cgate` to download the Docker image.

This image is built on the assumption you already have a C-Gate project file. This project file will have been created by your installer or electrician when C-Bus was installed and configured using the [C-Bus Toolkit](http://www2.clipsal.com/cis/technical/downloads/c-bus_toolkit) software. As part of installation on your Raspberry Pi, you will provide a *copy* of that file on your Raspberry Pi to C-Gate for read-only purposes. Keep your original project file safe, and treat it as your master copy.

The file will need to be called `MYHOME.xml` regardless of its original filename. 

Create a directory to store the C-Gate project file:
```
mkdir -p /var/rpi-config/cgate
chmod 777 -R /var/rpi-config
```

Copy the project file onto your Raspberry Pi and place it in the `/var/rpi-config/cgate` directory. The filename must be set to `MYHOME.xml`

Example command for copying from a Windows 10 computer to your Raspberry Pi with IP Address 10.64.104.10:

```
scp "C:\Clipsal\C-Gate2\tag\mike-ess.xml" pi@10.64.104.10:/var/rpi-config/cgate/MYHOME.xml
```

### C-Gate Monitor

Type `docker pull mikeess/rpi-cgate-monitor` to download the Docker image.

Create a directory to store the C-Gate Monitor configuration:
```
mkdir -p /var/rpi-config/cgate-monitor
chmod 777 /var/rpi-config/cgate-monitor
```

Create the configuration file `/var/rpi-config/cgate-monitor/twitter-config.py` and ensure it contains your Twitter API credentials. You can register with Twitter to use the API, and gain your own set of credentials, at https://developer.twitter.com/en/docs/basics/getting-started .

```
twitter_auth = {
    "access_token":"Your_Twitter_API_Access_Token",
    "access_token_secret":"Your_Twitter_API_Access_Token_Secret",
    "consumer_key":"Your_Twitter_API_Consumer_Key",
    "consumer_secret":"Your_Twitter_API_Consumer_Key"
}

twitter_recipients="twitter_user_1,twitter_user_2,etc..."
```

Create the configuration file `/var/rpi-config/cgate-monitor/cbus-config.py` and ensure it contains the following:

```
cgate_config = {
    "ip_address":"127.0.0.1",
    "command_port":20023,
    "event_port":20025,
    "cgate_project":"MYHOME",
    "cbus_network":"254",
    "cbus_application":"56"   
}

startup_config = {
    "max_connection_attempts":10,
    "connection_retry_interval":1 # Seconds
}
```
The above is the necessary connection details to ser2sock, running in a separate Docker container.

Edit the configuration file `/var/rpi-config/cgate-monitor/cbus-config.py` again, the this time define two sets of data:

- **cbus_groups**: This should contain the same CBus group information as is found in your C-Gate Toolkit project. 
- **cbus_groups**: This should contain the same CBus unit information as is found in your C-Gate Toolkit project. 

Note the groups and units *do not* need to be named the same in this configuration, as they are in your C-Gate Toolkit project, but they *should have* the same intent. The installer may have given the groups names that are not intended to be human readable, whereas with C-Gate Monitor you will want the groups to be named appropriately. Example: In C-Gate Toolkit, group 5 may be called "5 Kitchen Downstairs" where for CGate Monitor you can just call it "Kitchen".

Here is a sample of what the data should look like. Substitute with your own data, based on your CGate project, and the names you wish to use.

```
cbus_groups = {
    "1":"Kitchen",
    "2":"Living Room",
    "3":"Bedroom",
    "4":"Bathroom",
    "5":"Garage",
    "6":"Bus Coupler 1",
    "7":"Bus Coupler 2",
    "8":"Bus Coupler 3",
    "9":"Bus Coupler 4",
    "10":"Trigger All Lights On"
}

cbus_units = {
    "1":"Kitchen",
    "2":"Living Room East",
    "3":"Living Room West",
    "4":"Bedroom Main",
    "5":"Bedroom Bedside Left",
    "6":"Bedroom Bedside Right",
    "7":"Bathroom",
    "8":"Garage",
    "9":"Bus Coupler for Alarm"
}
```

Edit the configuration file ```/var/rpi-config/cgate-monitor/cbus-config.py``` again, the this time define one set of data called **event_actions**. This defines when a message should be sent to you, and what the messages are. The format of each line of data is:

```("group_number","value, usually 0 or 255"):"Message to send")```

0 means a light has been switched completely off. 255 indicates a light has been switched completely on. Groups connected to relay circuits, bus couplers, trigger groups etc only use these two values. Dimmer circuits can use the full range of 0-255, however it is recommended you only use 0 and 255 because other values will be difficult to achieve unless you have specific programming for those values.

Here is a sample of what the data should look like. Substitute with your own data. What you put here is up to you! The only things that matters is that you use the correct group numbers corresponding with **cbus_groups** data above.

```
event_actions = {
    ("1","255"):"Kitchen Lights Turned On",
    ("3","0"):"Bedroom Lights Turned Off",
    ("6","255"):"ALARM SIREN SOUNDING. POSSIBLE INTRUDER?",
    ("6","0"):"ALARM SIREN HAS STOPPED",
    ("7","255"):"Alarm Armed",
    ("7","0"):"Alarm Disarmed",
    ("10","255"):"All Lights Off"
}
```

### Homebridge & Homebridge-CBus

This is still a work in progress....

Type `docker pull mikeess/rpi-homebridge-cbus` to pull the Docker image from Docker Hub.

Create a directory to store the C-Gate Monitor configuration:
```
mkdir -p /var/rpi-config/homebridge-cbus
chmod 777 /var/rpi-config/homebridge-cbus
```

Create your config file ```/root/.homebridge/config.json```. For further information about what this file should contain, see the **Configuration** section of the actual [Homebridge-cbus documentation](https://github.com/anthonywebb/homebridge-cbus/blob/master/README.md).

Note the groups and units *do not* need to be named the same in this configuration, as they are in your C-Gate Toolkit project, or as named for the C-Gate Monitor, but they *should have* the same intent. You will want the names to make sense when displayed on your Apple device(s), and more importantly when spoken to Siri Example: In C-Gate Toolkit, group 5 may be called "5 Kitchen Downstairs" where for CGate Monitor you can just call it "Kitchen Lights".

Here is a sample of what the data should look like, with the Raspberry Pi having IP address 10.64.104.10. Substitute with your own data, based on your CGate project, and the names you wish to use.

```
{
  "bridge": {
    "name": "My Home",
    "username": "CC:22:3D:E3:CE:28",
    "port": 51826,
    "pin": "031-45-152"
  },
  
  "description": "My home HomeKit API configuration file",
 
  "platforms": [
    {
      "platform": "homebridge-cbus.CBus",
      "name": "CBus",
      "client_ip_address": "10.64.104.10",
      "client_controlport": 20023,
      "client_cbusname": "MYHOME",
      "client_network": 254,
      "client_application": 56,
      "client_debug": true,
      
      "accessories": [
	    { "type": "dimmer", "id": 1, "name": "Kitchen Lights", "enabled": true },
        { "type": "dimmer", "id": 2, "name": "Living Room Lights", "enabled": true },
        { "type": "dimmer", "id": 3, "name": "Bedroom Lights", "enabled": true },
		{ "type": "dimmer", "id": 4, "name": "Bathroom Lights", "enabled": true },
        { "type": "dimmer", "id": 5, "name": "Garage Lights", "enabled": true },
        { "type": "switch", "id": 10, "name": "All Lights Off Trigger", "enabled": true, "activeDuration": "1s"}
	  ]
    }
  ]
}

```

## Start Docker Containers

### rpi-ser2sock

```
docker run --name=rpi-ser2sock \
  --rm \
  -d \
  -i -t \
  --env TZ=`cat /etc/timezone` \
  --device=/dev/serial0 \
  --network=host \
  mikeess/rpi-ser2sock
```

### rpi-cgate

```
docker run --name=rpi-cgate \
  --rm \
  -d \
  -i -t \
  --env TZ=`cat /etc/timezone` \
  --network=host \
  --mount type=bind,source=/var/rpi-config/cgate/MYHOME.xml,destination=/clipsal/original-project/MYHOME.xml \
  mikeess/rpi-cgate
```

### rpi-cgate-monitor

```
docker run --name=rpi-cgate-monitor \
  --rm \
  -d \
  --env TZ=`cat /etc/timezone` \
  -i -t \
  --network=host \
  --mount type=bind,source=/var/rpi-config/cgate-monitor/cbus_config.py,destination=/python/cgate-monitor/cbus_config.py \
  --mount type=bind,source=/var/rpi-config/cgate-monitor/twitter_config.py,destination=/python/cgate-monitor/twitter_config.py \
  --mount type=bind,source=/var/rpi-config/cgate-monitor/email_config.py,destination=/python/cgate-monitor/email_config.py \
  mikeess/rpi-cgate-monitor
```

### rpi-homebridge-cbus

```
docker run --name rpi-homebridge-cbus \
  --rm \
  -d \
  --env TZ=`cat /etc/timezone` \
  -i -t \
  --network=host \
  --mount type=bind,source=/var/rpi-config/homebridge-cbus/.homebridge,destination=/root/.homebridge \
   mikeess/rpi-homebridge-cbus
```

## Authors

* **Mike Ess** - *Created instructions, photos and code.*

## Acknowledgments

A huge thanks to all contributors of the [**ser2sock**](https://github.com/nutechsoftware/ser2sock), [**Homebridge**](https://github.com/nfarina/homebridge) and [**C-Bus for Homebridge**](https://github.com/anthonywebb/homebridge-cbus/blob/master/README.md) softwares. This gives Clipsal C-Bus a new lease of life in this age of "Internet of Things".
