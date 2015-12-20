---
layout: post
title:  "Setup static IP wifi connection on Raspberry Pi"
date:   2015-12-19 22:30:00
comments: true
categories: [raspberry pi, ubuntu]
tags: ubuntu rpi
---

   Last September, the [jessie update](https://www.raspberrypi.org/blog/raspbian-jessie-is-here/) for the *Raspbian* OS.
   It included a few updates that changes the way you have to set up your network. A clear method to setup a Raspberry Pi with a static IP on wifi is given here. The goal is to have a headless Raspberry Pi that automatically connects to one or several known WiFi networks. 
   
   In the previous version, most of the changes to the network configuration had to be done in the `interfaces` file, you can check its content:
   
```bash
pi@raspberrypi:~ $ cd /etc/network
pi@raspberrypi:~ $ cat interfaces
```

```
# interfaces(5) file used by ifup(8) and ifdown(8)

# Please note that this file is written to be used with dhcpcd
# For static IP, consult /etc/dhcpcd.conf and 'man dhcpcd.conf'

# Include files from /etc/network/interfaces.d:
source-directory /etc/network/interfaces.d

auto lo
iface lo inet loopback

iface eth0 inet manual

allow-hotplug wlan0
iface wlan0 inet manual
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf

allow-hotplug wlan1
iface wlan1 inet manual
    wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
```

   You do not need to modify this file anymore. In order to get Wifi on your Raspberry Pi, you need a wireless USB adapter. Once you have one, just plug it and reboot your RPi. (I use this [one](http://www.amazon.com/TP-LINK-TL-WN725N-Wireless-Adapter-150Mbps/dp/B008IFXQFU)) 
   
   You still need to provide the system with the SSID of your network and the password. You can do that here:

```bash
pi@raspberrypi:~ $ cd /etc/wpa_supplicant
pi@raspberrypi:~ $ sudo nano wpa_supplicant.conf
```
You need to add the following lines:

```
network={
    ssid="ESSID"
    psk="Your_wifi_password"
    key_mgmt=WPA-PSK
}
```

The real novelty is the use of [dhcpcd](http://www.linuxfromscratch.org/blfs/view/svn/basicnet/dhcpcd.html) to manage static connection. To set up a static IP for you Raspberry Pi, you need to modify the following file:

```bash
pi@raspberrypi:~ $ sudo nano /etc/dhcpcd.conf
```

Add the following lines, after adapting them to your network:

```
interface wlan0
     static ip_address=192.168.0.XXX
     static routers=192.168.0.1
     static domain_name_servers=192.168.0.1
```

If you do not have the information about your network, you can always take an already connected device's settings (smartphone or laptop).
You can now reboot your Raspberry Pi. It should automatically connect to the configured network, and reconnect (as soons as it is available again) if the connection is lost for any reason.

   The next thing you should do is enable the SSH server in the Advanced settings of `sudo raspi-config` and you're good to go !
   

{% include twitter_plug.html %}

