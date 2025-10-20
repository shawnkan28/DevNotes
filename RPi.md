# Raspberry Pi Notes
some general guide to setting up RPi Servers.

## Installing RPI
burn RPI image into mircro SD card.<br>
https://www.raspberrypi.com/news/raspberry-pi-imager-imaging-utility/ 


## Setup SSH
https://www.youtube.com/watch?v=63yw7b0NuWc
<br>
<b><u>Default user doesnt exist anymore. see this article.</u></b>
<br>
https://www.raspberrypi.com/news/raspberry-pi-bullseye-update-april-2022/
<br>
<b><u>only works with 2.4Ghz.</u></b>
Once you import the image to the MicroSD card you want to do this:
1. open the microSD Card in the folder manager
2. Create a empty file called "ssh" without any extension.
3. create a new file called "wpa_supplicant.conf" file and input credentails for the WIFI.
4. update the conf file the the following:
```conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US
network={
    ssid="WIFI_NAME"
    psk="WIFI_PASSWORD"
}
```
5. Remove and connect MicroSD to RPI
6. Find out what the RPI IP Address by using the rpi user.
```ps
ping raspberrypi.local
```
7. now in your command line type in 
```pi
ssh pi@ipaddress
```
8. type in "yes" for the prompt and the default password for RPI is "raspberry"

## Update System
run the following commands to update all existing plugins
```vim
sudo apt update
sudo apt upgrade
```

## Commonly used Commands
```
# Change to root user
sudo su - 

# Change Password
passwd <username>
```