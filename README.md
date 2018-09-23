# Introduction

THIS IS A WORK IN PROGRESS, NOT FINISHED YET.

# Assumptions

The instructions below were created on a Raspberry Pi Model 3B with 16GB SD Card. If you use different hardware, you may experience difficulties due to differences in hardware, especially regarding Serial Interface configuration.

These instructions were created on Raspbian Stretch Lite, specifically image version **2018-06-27-raspbian-stretch-lite.img**.


# Get prepared

You will require the following equipment:

* A working [Clipsal C-Bus system with](https://www.clipsal.com/Trade/Products/Integrated-Systems/C-Bus-Control-and-Management-System) with a [RS-232 PC Interface model 5500PC](https://www.clipsal.com/Trade/Products/ProductDetail?catno=5500PC).
* 1x Raspberry Pi kit ([Raspberry Pi Model 3B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/), 16GB micro SD card, power supply and case).
* A USB mouse and USB keyboard. These are only required for initial setup, and are not be needed long term.

Additionally, some wiring will be required to connect the Raspberry Pi to the Clipsal Serial Connector. For this, you need:

* 1x Cat5 cable
* 1x [soldering iron](https://en.wikipedia.org/wiki/Soldering_iron) and knowledge of how to use one, or a friend who can help you. 
* 1x RS232 to TTL converter that utilises a MAX3232 chip. Easily found online for very small cost (approximately $1.00). Ideally find one that is just a board, with no connectors on either end.
* 4x [Dupont wire](https://en.wikipedia.org/wiki/Jump_wire) with female on one end. What is on the other end does not matter, it will be cut off.

The intention is to connect into the rear (i.e. permanent) serial connection on the C-Bus PC Interface, achieved through an RJ-45 connector. The 9-pin connector on the front of the C-Bus PC Interface will not be used, because this is intended for temporary connection during initial installation.

<http://www.cleverhome.com.au/manuals/Clipsal-C-Bus-5500PC-PC-Interface-Installation.pdf>

The wiring needs to be like this:

TODO

# Installation and Configuration

## Hardware

### Raspberry Pi

You will require the following equipment:

* 1x Raspberry Pi kit (Raspberry Pi Model 3B, 16GB micro SD card, power supply and case.
* A USB mouse and USB keyboard. These are only required for initial setup, and are not be needed long term.


### Serial Interface

* Some wiring. A length of Cat5 cable works fine, similer cables are also OK.
* A RS232 to UART adapter. Look for one containing a MAX3232 integrated circuit.

## Operating System

Download Raspbian Stretch Lite image from <https://www.raspberrypi.org/downloads/raspbian/> . Download the zip version, and then extract the image file (with .img extension).

Write the image to your SD Card. A suitable tool for this purpose is Etcher, download from <https://etcher.io/>.

## Temporary Installation

Initially, connect the Raspberry Pi to a USB keyboard, USB mouse and any HDMI capable monitor or TV. If you are using a wired network connection, also connect to the network now.

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

You are ready to connect the Raspberry Pi to C-Bus.

TODO: Add more detail here.

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

The file will need to be called `MY-HOME.xml` regardless of its original filename. 

Create a directory to store the C-Gate project file:
```
mkdir -p /var/rpi-config/cgate
chmod 777 -R /var/rpi-config
```

Copy the project file onto your Raspberry Pi and place it in the `/var/rpi-config/cgate` directory. The filename must be set to `MY-HOME.xml`

Example command for copying from a Windows 10 computer to your Raspberry Pi with IP Address 10.64.104.10:

```
scp "C:\Clipsal\C-Gate2\tag\mike-ess.xml" pi@10.64.104.10:/var/rpi-config/cgate/MY-HOME.xml
```

### C-Gate Monitor

Type `docker pull mikeess/rpi-cgate-monitor` to download the Docker image.

Create a directory to store the C-Gate Monitor configuration:
```
mkdir -p /var/rpi-config/cgate-monitor
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
    "cgate_project":"MY-HOME",
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

Create your config file ```/root/.homebridge/config.json```. For further information about what this file should contain, see the **Configuration** section of the actual [Homebridge-cbus documentation](https://github.com/anthonywebb/homebridge-cbus/blob/master/README.md).

To run the Docker Container interactive:
```
docker run -i -t --network=host -p 10.64.104.12:5353:5353 -p 10.64.104.12:51826:51826 --volume=/root:/root:rw  mikeess/rpi-homebridge-cbus
```

To run the Docker Container as daemon (recommended):
```
docker run --d --network=host -p 10.64.104.12:5353:5353 -p 10.64.104.12:51826:51826 --volume=/root:/root:rw  mikeess/rpi-homebridge-cbus
```

## Start Docker Containers

### rpi-ser2sock

```
docker run --name=rpi-ser2sock \
  -d \
  -i -t \
  --env TZ=`cat /etc/timezone` \
  --expose 10001 \
  --device=/dev/serial0 \
  --network=host \
  mikeess/rpi-ser2sock
```

### rpi-cgate

```
docker run --name=rpi-cgate \
  -d \
  -i -t \
  --env TZ=`cat /etc/timezone` \
  -p 127.0.0.1:20023:20023 \
  -p `hostname -I | awk '{print $1;}'`:20023:20023 \
  --expose 20023 \
  --network=host \
  --mount type=bind,source=/var/rpi-config/cgate/MY-HOME.xml,destination=/clipsal/original-project/MY-HOME.xml \
  mikeess/rpi-cgate
```

### rpi-cgate-monitor

```
docker run --name=rpi-cgate-monitor \
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

This is still a work in progress....

```
#docker run -i -t --network=host -p 10.64.104.12:5353:5353 -p 10.64.104.12:51826:51826 --volume=/root:/root:rw  mikeess/rpi-homebridge-cbus

docker run -i -t --network=host -p 5353:5353 -p 51826:51826 --volume=/root:/root:rw  mikeess/rpi-homebridge-cbus

service dbus start && service avahi-daemon start
homebridge
```

## Authors

* **Mike Ess** - *Created instructions, photos and code.*

## Acknowledgments

A huge thanks to all contributors of the [**ser2sock**](https://github.com/nutechsoftware/ser2sock), [**Homebridge**](https://github.com/nfarina/homebridge) and [**C-Bus for Homebridge**](https://github.com/anthonywebb/homebridge-cbus/blob/master/README.md) softwares. This gives Clipsal C-Bus a new lease of life in this age of "Internet of Things".
