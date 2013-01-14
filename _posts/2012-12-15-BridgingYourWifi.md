---
date: 2012-12-15 09:24:31
title: WIFI Coverage Extension Report
layout: post
tags:
    - WIFI
categories:
    - Misc
---

Everyone wants a huge house as long as the WIFI coverage is great. In 2006 intel introduced its 802.11n capable MIMO wireless adapters, and they boasted the peak transfer speed can go up to 300Mbps (450Mbps)......only when signal is good of course.

Keeping a good signal around  house is not very easy, especially when the house is separated into rooms with thick walls. The result is that the 300Mbps speed is only limited to several square meters around the router.

I have tried to use Bridge mode to extend the coverage of the Wifi - a TP-LINK WR700N mini router acts as a bridge router, and a TP-LINK WR941N as the master router.

Basically I have 2 options here, the WR700N can operate in either Bridge or Repeater mode, the only difference being whether it is operating using the same protocol with its upstream router.

The following is my test result:

	ROUTER CONFIGS	1

	Router	LanIP			DHCP		DHCPRange	Protocol	Mode	SSID			Channel

	WR941N	192.168.1.1		Enabled		100-199		802.11N		Router	TP-LINK_EC4818	11

	WR700N	192.168.1.253	Enabled		002-050		802.11N		Bridge	TP_LINK_EC4818	6

	TEST RESULTS 	1
	
	DeviceName							Roaming		NetwortUsability	Description

	ThinkPad Tablet (Android 4.0)		Failed		Usable				Cannot roam, connects to only one AP (the WR941N) regardless of signal strength.

	Kindle Fire							Failed		Not Usable			The Kindle Fire network module crashes, as the WIFI settings become unavailable.

	ThinkPad X220 (Windows 8)			Failed		Usable				Can connect, but failed when roaming - network is limited when roaming, but configurations are all right.

	Surface (Windows RT)				Failed		Usable				The same as Windows 8.

	iPad 2 (IOS 5)						Failed		Usable				The same as Windows 8

	HTC 7 Mozart(Windows Phone)			Failed 		Usable 				The same as Windows 8

	Sony X10 Mini(Android 2.2)			- 			-					Not tested, because this setting cannot meet my needs.

	Nokia C6-00(Symbian S60 V5)			-			-					Not tested, because this setting cannot meet my needs.



----------------------------

	ROUTER CONFIGS	2

	Router	LanIP			DHCP		DHCPRange	Protocol	Mode	SSID			Channel

	WR941N	192.168.1.1		Enabled		100-199		802.11N		Router	TP-LINK_EC4818	11

	WR700N	192.168.1.253	Disabled	-			802.11N		RepeaterTP_LINK_EC4818	11
	
	TEST RESULTS 	2
	
	DeviceName							Roaming		NetwortUsability	Description

	ThinkPad Tablet (Android 4.0)		Success		Not Usbale			Can connect to different APs, but cannot assign properly the network addresses.

	Kindle Fire							Failed		Not Usable			Same as ThinkPad Tablet.

	ThinkPad X220 (Windows 8)			Success		Usable				Can connect, and can roam properly. The Windows 8 will auotmatically get IP config information from 192.168.1.1

	Surface (Windows RT)				Failed		Usable				The same as Windows 8.

	iPad 2 (IOS 5)						Success		Not Usbale			Same as ThinkPad Tablet.

	HTC 7 Mozart(Windows Phone)			Success		Not Usbale			Same as ThinkPad Tablet.

	Sony X10 Mini(Android 2.2)			- 			-					Not tested, because this setting cannot meet my needs.

	Nokia C6-00(Symbian S60 V5)			-			-					Not tested, because this setting cannot meet my needs.
	


----------------------------

	ROUTER CONFIGS	3

	Router	LanIP			DHCP		DHCPRange	Protocol	Mode	SSID			Channel

	WR941N	192.168.1.1		Enabled		100-199		802.11N		Router	TP-LINK_EC4818	11

	WR700N	192.168.1.253	Enabled		002-050		802.11N		RepeaterTP_LINK_EC4818	11
	
	TEST RESULTS 	3 
	
	DeviceName							Roaming		NetwortUsability	

	ThinkPad Tablet (Android 4.0)		Success		Usable			

	Kindle Fire							Success		Usable			

	ThinkPad X220 (Windows 8)			Success		Usable				

	Surface (Windows RT)				Success		Usable				

	iPad 2 (IOS 5)						Success		Usable			

	HTC 7 Mozart(Windows Phone)			Success		Usable			

	Sony X10 Mini(Android 2.2)			Success		Usable				

	Nokia C6-00(Symbian S60 V5)			Success		Usable			


