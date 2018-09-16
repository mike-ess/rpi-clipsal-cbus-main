# Introduction

THIS IS A WORK IN PROGRESS, NOT FINISHED YET.

# Assumptions

The instructions below were created on a Raspberry Pi Model 3B with 16GB SD Card. If you use different hardware, you may experience difficulties due to differences in hardware, especially regarding Serial Port configuration.

These instructions were created on Raspbian Stretch Lite, specifically image version **2017-11-29-raspbian-stretch-lite.img**.


# Get prepared

You will require the following equipment:

* A working [Clipsal C-Bus system with](https://www.clipsal.com/Trade/Products/Integrated-Systems/C-Bus-Control-and-Management-System) with a [RS-232 PC Interface model 5500PC](http://www2.clipsal.com/cis/technical/product_groups/cbus/system_units_and_accessories/pc_interface).
* 1x Raspberry Pi kit (Raspberry Pi Model 3B, 16GB micro SD card, power supply and case.
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

Initially, connect the Raspberry Pi to a keyboard, mouse and any monitor or TV with HDMI connections. If you are using a wired network connection, also connect to the network now.

Power on the Raspberry Pi. When it loads, login with username=**pi** and password=**raspberry**.

## Initial setup

Type ```sudo raspi-config``` and then use the following instructions. Each instructon expects you to begin navigation from the home menu.

### Network Settings

Select option 1 to change the password for the **pi** user.  

Select Option 2 > Option N1 to set the hostname. This should be a name that no other device on your network has (e.g. **cpi-cbus**)

If your Raspberry Pi is intended to be connected to Wi-Fi, when select Option 2 > Option N2 and then enter the SSID of your network, and the password of your network. Then, Select Option 4 > Option I2 to select the right WiFi country.

TODO: Fixed IP Address

### Localisation Settings

Select Option 4 > Option I2 to select your time zone.

Select Option 4 > Option I2 to select your keyboard layout. This is important because the default is UK keyboard layout, which will not be right for many users.

### SSH client

Type `sudo apt-get -y install ssh` to install the SSH server. This may be used to connect to the Raspberry Pi using an SSH client such as Putty.

### Restart

Exit **raspi-config**.

Reboot the Raspberry Pi by typing ```sudo shutdown -r now``` and wait for the reboot. When ready, login using login with username = **pi** and password = your own password.

### Verify Configuration

At this point, the Raspberry Pi should be ready to become "headless", meaning you do not need a keyboard, mouse and monitor or TV.

Take this opportunity to login to the Raspberry Pi using an SSH client. You will know the IP Address, username and password.

If you cannot login via SSH, check the steps above and make corrections, until you are ready.

If you can login OK via SSH, you are ready to go. Shut down the Raspberry Pi by typing ```sudo shutdown -h now``` and it will usually only take a few seconds to fully shut down.

## Permanent Installation

Now you can move the Raspberry Pi to its desired location, and you only need to plugin power and network cable (if you are not using Wi-Fi). 

Do not plug in the serial connections to C-Bus yet.

Power the Raspberry Pi up again, and login via SSH.

## Further Setup

### Firmware Update

Type these commands to update the firmware.
```
sudo rpi-update
sudo shutdown -r now   # TODO: is this necessary?
```

login again via SSH.

### Elevate to root

Type ```sudo bash``` to become root. All subsequent commands below will require root.

### Update Packages

Type `sudo apt-get update && sudo apt-get -y upgrade` to get Raspbian up to date with packages.

## Software Components

Each of the software components are created as Docker containers to make installation easier.

### Docker

Type `sudo bash -c "curl -sSL https://get.docker.com | sh"` to install Docker.

TODO: Move this further up the order of installation.

### Telnet client

Type `sudo apt-get -y install telnet` to install the Telnet client. This is optional, however can be useful for troubleshooting as it helps check what ports are open.

## Prepare Docker Containers

### ser2sock

Type `docker pull mikeess/rpi-ser2sock` to download the Docker image.

No configuration is required. Look in the [Dockerfile of ser2sock](https://github.com/mike-ess/rpi-ser2sock/blob/master/Dockerfile) for details of (a) what port number is used and (b) what device is used to connect to the serial interface on the Raspberry Pi.

TODO: Rephrase the above.

### Clipsal C-Gate Software

Type `docker pull mikeess/rpi-cgate` to download the Docker image.

This image is built on the assumption you already have a C-Gate project file. This project file will have been created by your installer or electrician when C-Bus was installed and configured using the [C-Bus Toolkit](http://www2.clipsal.com/cis/technical/downloads/c-bus_toolkit) software. As part of installation on your Raspberry Pi, you will provide a *copy* of that file on your Raspberry Pi to C-Gate for read-only purposes. Keep your original project file safe, and treat it as your master copy.

Copy the project file onto your Raspberry Pi and place it in the /var/rpi-config/cgate directory.

If you have already put the file on your Raspberry Pi:
```
mkdir -p /var/rpi-config/cgate
cp [path/to/your/project/file]/[projectname].xml /var/rpi-config/cgate/MY-HOME.xml
```

If you need to put the file on your Raspberry Pi, then from a Windows machine you can use the [**pscp**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) SCP client to transfer the file (full instructions [here](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter5.html).

``
pscp [C:\path\to\your\project\file]\projectname].xml pi@[IP address of R-Pi]:/var/rpi-config/cgate/MYHOME.xml
```

Example:
```
pscp C:\Clipsal\C-Gate2\tag\john-doe.xml pi@192.168.0.10:/var/rpi-config/cgate/MYHOME.xml
```

### C-Gate Monitor

Type `docker pull mikeess/rpi-cgate-monitor` to download the Docker image.

Create the configuration file ```/var/rpi-config/cgate-monitor/twitter-config.py``` and ensure it contains your Twitter API credentials. You can register with Twitter to use the API, and gain your own set of credentials, at https://developer.twitter.com/en/docs/basics/getting-started .

```
twitter_auth = {
    "access_token":"Your_Twitter_API_Access_Token",
    "access_token_secret":"Your_Twitter_API_Access_Token_Secret",
    "consumer_key":"Your_Twitter_API_Consumer_Key",
    "consumer_secret":"Your_Twitter_API_Consumer_Key"
}

twitter_recipients="twitter_user_1,twitter_user_2,etc..."
```

Create the configuration file ```/var/rpi-config/cgate-monitor/cgate-config.py``` and ensure it contains the following:

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

Edit the configuration file ```/var/rpi-config/cgate-monitor/cgate-config.py``` again, the this time define two sets of data:

- cbus_groups: This should contain the same CBus group information as is found in your CGate project. 
- cbus_groups: This should contain the same CBus unit information as is found in your CGate project. 

Note the groups and units do not need to be named the same in this configuration, as they are in you CGate project, but they should have the same intent. The installer may have given the groups names that are not intended to be human readable, whereas for messages you want the gruops to be names appropriately. Example: In CGate, group 5 may be called "5 Kitchen Downstairs" where for rpi-cgate-monitor you can just call it "Kitchen".

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

Edit the configuration file ```/var/rpi-config/cgate-monitor/cgate-config.py``` again, the this time define one set of data called event_actions. This defines when a message should be sent to you. The format of each line of data is:

```("group_number","value, usually 0 or 255"):"Message to send")```

Here is a sample of what the data should look like. Substitute with your own data. What you put here is up to you! The only things that matters is that you use the correct group numbers corressponding with cbus_groups above.

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

Linked to Host:
```
docker run --name=ser2sock \
  -d \
  -i -t \
  --expose 10001 \
  --device=/dev/serial0 \
  --network=host \
  mikeess/rpi-ser2sock
```

Linked to Bridge:
```
docker run --name=ser2sock \
  -d \
  -i -t \
  --expose 10001 \
  --device=/dev/serial0 \
  --network=bridge\
  mikeess/rpi-ser2sock
```

### rpi-cgate

Linked to Host:
```
docker run --name=cgate \
  -d \
  -i -t \
  -p 127.0.0.1:20023:20023 \
  -p `hostname -I | awk '{print $1;}'`:20023:20023 \
  --expose 20023 \
  --network=host \
  --mount type=bind,source=/var/rpi-config/cgate,destination=/var/rpi-config/cgate \
  mikeess/rpi-cgate
```

Linked to Bridge:
```
docker run --name=cgate \
  -d \
  -i -t \
  -p 127.0.0.1:20023:20023 \
  --link ser2sock \
  --expose 20023 \
  --network=host \
  --mount type=bind,source=/var/rpi-config/cgate,destination=/var/rpi-config/cgate \
  mikeess/rpi-cgate
```

### rpi-cgate-monitor

Linked to Host:
```
docker run --name=cbus-monitor \
  -d \
  -i -t \
  --network=host
  --mount type=bind,source=/var/rpi-config/cgate,destination=/var/rpi-config/cgate \
  mikeess/rpi-cbus-monitor
```

Linked to Bridge:
```
docker run --name=cbus-monitor \
  -d \
  -i -t \
  --network=bridge
  --link cgate \
  --mount type=bind,source=/var/rpi-config/cgate,destination=/var/rpi-config/cgate \
  mikeess/rpi-cbus-monitor
```

### rpi-homebridge-cbus

#docker run -i -t --network=host -p 10.64.104.12:5353:5353 -p 10.64.104.12:51826:51826 --volume=/root:/root:rw  mikeess/rpi-homebridge-cbus

docker run -i -t --network=host -p 5353:5353 -p 51826:51826 --volume=/root:/root:rw  mikeess/rpi-homebridge-cbus

service dbus start && service avahi-daemon start
homebridge
```

## Authors

* **Mike Ess** - *Created instructions, photos and code.*

## Acknowledgments

A huge thanks to all contributors of the [**ser2sock**](https://github.com/nutechsoftware/ser2sock), [**Homebridge**](https://github.com/nfarina/homebridge) and [**C-Bus for Homebridge**](https://github.com/anthonywebb/homebridge-cbus/blob/master/README.md) softwares. This gives Clipsal C-Bus a new lease of life in this age of "Internet of Things".
