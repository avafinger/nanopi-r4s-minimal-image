# nanopi-r4s-minimal-image
NanoPi R4S Ubuntu 20.04 LTS

![NanoPi R4S](https://github.com/avafinger/nanopi-r4s-minimal-image/raw/main/nanopi-4rs-cam.jpg)

* [Introduction](#introduction)
* [Kernel History](#kernel-history)
* [Mini Router](#mini-router)
* [H264 Camera](#h264-camera)
* [Build your Kernel](#build-kernel)
* [Releases](#releases)
* [Instructions](#instructions)
* [Issues](#issues)
* [Acknowledgement](#acknowledgement)

## Introduction

This is the Ubuntu 20.04 LTS Image for the **NanoPi R4S 4GB** dram version with latest mainline (Kernel / U-Boot).
Image is minimal with some tools already installed. You can switch Kernel version to get most of the board working.

![Htop screenshot](https://github.com/avafinger/nanopi-r4s-minimal-image/raw/main/htop-nanopi-r4s.png)

## Kernel history

Kernel will be added as it is a WiP, ideal for testing, developing and checking functionality.

* **v1 is Kernel 5.10.43**

  The latest patched kernel from FriendlyElec where you get most peripherals working.
  
  https://github.com/avafinger/nanopi-r4s-minimal-image/releases/tag/v1

* **v2 is Kernel 5.13.1**

  Mainline Kernel (stable kernel).
  
  ![CPU Freq / VCore](https://github.com/avafinger/nanopi-r4s-minimal-image/raw/main/nanopi-r4s-htop.png)
  
  https://github.com/avafinger/nanopi-r4s-minimal-image/releases/tag/v2
  
* Upgrade to Kernel 5.13.2

  https://github.com/avafinger/nanopi-r4s-minimal-image/releases/tag/v2-update
  
  
* Upgrade to Kernel 5.13.3
  
  https://github.com/avafinger/nanopi-r4s-minimal-image/releases/tag/v2-update-2

* **v3 is Kernel 4.19.161**

  WiP.


## Mini Router

Building your mini router with the preferred kernel and configuration.
Instructions and annotations to build our mini router and navigate safer on the Internet.

WiP


## H264 camera

Experiments and annotations with USB H.264 Camera (Trial and Error).

Testing USB2.0 with a USB H264 camera (2MP - 30 FPS), the sensor is 3MP but delivers 1920x1080P 30 FPS. The Camera has good images for indoor usage.
For the test, we build **videostreamer** with HTML5 MP4 support for streaming video. In our test **NanoPi R4S** IP is 192.168.254.119.
The remuxing takes ~20% CPU. The camera delivers ~30 FPS.

Build:

    sudo apt install golang
    sudo apt-get install libmp3lame-dev libx264-dev libpulse-dev libpulse-dev libv4l-dev libvdpau-dev
    sudo apt-get install ffmpeg libavcodec-dev libavformat-dev libavdevice-dev
    git clone https://github.com/horgh/videostreamer
    cd videostreamer
    go build

Run:

    sudo ./videostreamer -host 192.168.254.119 -format v4l2  -input "/dev/video3"  -verbose=0 -fcgi=false


From a browser (your remote PC), type: 

    http://192.168.254.119:8080/stream
    

![Streaming video H264](https://github.com/avafinger/nanopi-r4s-minimal-image/raw/main/nanopi-r4s-h264.png)


## Building Kernel

Instructions to build and test your own Kernel (ON-BOARD).

![Building Kernel](https://github.com/avafinger/nanopi-r4s-minimal-image/raw/main/nanopi-r4s-building-kernel.png)

WiP



## Instructions

The current setup for the OS image is:

* eth0 as DHCP (WAN)
* eth1 (R8169) renamed to enp1s0 and static IP: 192.168.254.199 (enabled in /etc/network/interfces)
* No iptables created for the router
* Kernel is conservative, 1.8GHz / 1.4GHz and can be set to 2.0GHz / 1.5GHz
* 8GB SD size minimal.
* all the build tools are installed
* ssh installed, use ssh ubuntu@192.168.254.199 or ssh ubuntu@IP_FROM_DHCP_WAN

**Credentials**

user / pass: ubuntu / ubuntu

**Network**

Netplan is not active, and you have to edit the file /etc/network/interfaces (with linux) to change the settings:

    ubuntu@192.168.254.199's password: 
    Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.13.1 aarch64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
    Last login: Mon Jul 12 23:26:53 2021 from 192.168.254.253
    ubuntu@nanopi-r4s:~$ ifconfig
    enp1s0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
            inet 192.168.254.199  netmask 255.255.0.0  broadcast 192.168.255.255
            ether de:68:6e:ce:06:bf  txqueuelen 1000  (Ethernet)
            RX packets 96  bytes 13677 (13.6 KB)
            RX errors 0  dropped 1  overruns 0  frame 0
            TX packets 35  bytes 5587 (5.5 KB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    eth0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
            ether 62:0f:e5:4f:d8:f8  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
            device interrupt 31  

    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            inet6 ::1  prefixlen 128  scopeid 0x10<host>
            loop  txqueuelen 1000  (Local Loopback)
            RX packets 320  bytes 22960 (22.9 KB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 320  bytes 22960 (22.9 KB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    ubuntu@nanopi-r4s:~$ route
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    default         _gateway        0.0.0.0         UG    0      0        0 enp1s0
    192.168.0.0     0.0.0.0         255.255.0.0     U     0      0        0 enp1s0
    ubuntu@nanopi-r4s:~$ cat /etc/network/interfaces
    # interfaces(5) file used by ifup(8) and ifdown(8)
    auto lo
    iface lo inet loopback

    #auto eth0
    allow-hotplug eth0
    iface eth0 inet dhcp

    ## eth1 renamed enp1s0
    #allow-hotplug enp1s0
    auto enp1s0
    #iface enp1s0 inet dhcp
    iface enp1s0 inet static
    address 192.168.254.199
    netmask 255.255.0.0
    broadcast 192.168.255.255
    gateway 192.168.254.254
    dns-nameservers 8.8.8.8 8.8.4.4

    ###auto wlan0
    #allow-hotplug wlan0
    #iface wlan0 inet dhcp
      wpa-ssid "FIB-5G"
      wpa-psk "Pw"
    ###address 192.168.254.55
    ###netmask 255.255.0.0
    ###gateway 192.168.254.254
    ###dns-nameservers 8.8.8.8 8.8.4.4
    ######wpa-conf /etc/wpa_supplicant/wpa_supplicant.conf
    ### Disable power saving on compatible chipsets (prevents SSH/connection dropouts over WiFi)
    ###wireless-mode Managed
    #wireless-power off

    ubuntu@nanopi-r4s:~$ 

**Flashing the Image**

You need a linux box to burn your SD CARD.
Download the files and find the device name for your SD CARD after is inserted:

    dmesg|tail
    [270176.904124] sd 6:0:0:0: [sdd] 31116288 512-byte logical blocks: (15.9 GB/14.8 GiB)
    [270176.938132]  sdd: sdd1 sdd2

Burn the Image file like this (taken from the above info):

    ./flash_mainline_sdcard.sh /dev/sdd

If you have an embedded sd card reader, look for /dev/mmcblkN where N is your sd card number

## Releases

Releases will be available here: https://github.com/avafinger/nanopi-r4s-minimal-image/releases


## Issues

* Kernel 5.10.43

  Reboot not always works due to "can't mount rootfs" (slow cards).
  
 
 ## Acknowledgement 
 
 I would like to thanks FE for the sample. 
