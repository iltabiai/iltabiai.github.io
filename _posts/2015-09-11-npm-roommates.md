---
layout: post
title:  "Using Nmap to find your roommates"
date:   2015-09-11 20:52:00
categories: [nmap, python, wifi, scan]
tags: nmap, python, wifi, scan
---

You can easily check if someone is connected to a Wifi network using a simple function provided in the [Network MAP](https://nmap.org/) package. Nmap is a security package designed to discover hosts, services and machines on a network.

Setting up
----------------

First, you need to install *Nmap* and its python wrapper:

	sudo apt-get install nmap
	sudo pip install python-nmap


Use it
----------------
You can either use the *python* console to perform unique scans or script your scans to perform them on a regular basis.
You will need to be **root** in order to use nmap. 

	#First, you need a Postscanner object that will be used to do the scan
	nm = nmap.PortScanner()
	#You can then do a scan of all the IPV4 addresses provided by the network you are connected to
	nm.scan(hosts = '192.168.1.0/24', arguments = '-sn')

The 3 first numbers of the IP address (`192.168.1`) used as `hosts` might need to be changed depending of the network. Check the IP address of a machine connected to that same network to know which `hosts` you should scan.
You can then check the `all_hosts()` function attached to the `PortScanner` object which provides a list containing information about each IP address available in the network. For each element, you can check several values such as the MAC address. 

	for host in nm.all_hosts():
		#If the status of an IP address is not down, print it
		if nm[host]['status']['state'] != "down":
			print "STATUS:", nm[host]['status']['state']
			#Print the MAC address
			try:
				print "MAC ADDRESS:", nm[host]['addresses']['mac']
			except:
				mac = 'unknown'

For some devices, the MAC address is not openly displayed. 

Implementation
----------------

The best way to use this is to set up a Raspberry Pi running a script with a Cron job regularly scanning your network and notifying you through an intranet webpage, mail or SMS if any interesting event happens. To do so, you need to know the MAC address of your roommates' smartphones or laptops. You can also use it for network discovery. You will quickly motice that you can attach MAC addresses to people by performing
Nmap is a much more powerful tool than what is presented here. This is just a fun trick that can be used with roommates [to play an intro music when they get home for example](https://github.com/usag1e/Homza).

Now that I know that anyone can get my MAC addresses by performing simple scans when I am connected to a WiFi network and when I am not, what I would like to know is how can one be protected from such attempts? Is it possible to voluntarily hide your MAC address from the netowrk?

PS: Check NMap's presence in [blockbuster movies](https://nmap.org/movies/)


