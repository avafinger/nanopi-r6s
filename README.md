## nanopi-r6s-debian11-xcfe-minimal experimental image

Debian 11 (11.6) with xfce (X11) for the NanoPi R6S base image for testing hardware functionality and debugging.

Table of Contents:

- [Introduction](#introduction)
- [Board Storage Layout](#board-storage-layout)
- [Build a Kernel](#build-kernel)
- [Install Kernel](#install-kernel)
- [Networking](#ethernet)
- [USB Camera Test](#usb-camera-test)
  - [H264](#h264)
    - [Gstreamer pipeline H264](#gstreamer-pipeline-h264)
    - [Python Gstreamer code](#python-gstreamer-code)
  - [YUY2](#yuy2)
    - [Gstreamer pipeline YUY2](#gstreamer-pipeline-yuy2)
  - [MJPEG](#mjpeg)
    - [Gstreamer pipeline MJPEG](#gstreamer-pipeline-mjpeg)
- [Hardware decoder](#hardware-decoding)
  - [Gstreamer](#gstreamer)
  - [FFmpeg](#ffmpeg)
- [Hardware encoder](#hardware-encoding)
- [Rockchip NPU](#npu)
  - [SDL2 demo](#sdl2-demo)
  - [SDL3 demo](#sdl3-demo)
- [Mini Router](#mini-router)
- [Releases](#releases)
- [Issues / Limitations](#issues)
- [Acknowledgments](#acknowledgments)

## Introduction

The OS Image is "lean and mean", X11 (Debian 11.6) with Hardware Acceleration and the latest rockchip multimedia and kernel.
You can load and run different kernels for testing.
This is a WiP and is used to test RK3588/RK3588s hardware features, use as is.

## Board Storage Layout

|  storage |   type    |
|----------|-----------|
|  mmcblk0 |  sd card  |
|  mmcblk2 |  eMMC 32G |


## Build Kernel

**build from kernel tree (CROSS-COMPILE recommended)**


    # change accordingly
    # your kernel config
    export DTB=rk3588s-nanopi-r6s.dtb
    export CONF="defconfig"
    
    # kernel directory
    export KVD=$PWD
    export SRC="output"
    export DST="output"
    export ARCH=arm64
    export CROSS_COMPILE=aarch64-linux-gnu-
    make ${CONF}
    make -j8 INSTALL_MOD_PATH=output Image 
    make -j8 INSTALL_MOD_PATH=output dtbs 
    make -j8 INSTALL_MOD_PATH=output modules 
    make -j8 INSTALL_MOD_PATH=output modules_install
    KV=$(make -s kernelrelease)
    echo $KV
    make -j4 INSTALL_MOD_PATH=output INSTALL_HDR_PATH=output/usr/src/linux-headers-${KV} headers_install
    echo $DST
    echo $SRC
    mkdir -p ${DST}/boot/dtbs/${KV}/rockchip/
    cp -vf arch/arm64/boot/dts/rockchip/${DTB} ${DST}/boot/dtbs/${KV}/rockchip/${DTB}
    cp -vf arch/arm64/boot/Image ${DST}/boot/Image_${KV}
    rm ${DST}/lib/modules/${KV}/source
    rm ${DST}/lib/modules/${KV}/build
    mv ${DST}/lib ${DST}/usr/
    tar -cvpzf kernel_$KV.tar.gz --directory=./output/ .


## Install Kernel

**install resulting tar.gz from cross-compiler**

    sudo tar -xvpzf kernel_$KV.tar.gz -C /

## Boot your new kernel

The boot menu is in the file:

    /boot/extlinux/extlinux.conf

Add the new entry and make it default


    timeout 10
    menu title select kernel
    DEFAULT mainline_kernel

        label kernel-5.10.110-4G
            kernel /Image_5.10.110-debugx
            initrd /initrd.img
            devicetreedir /dtbs/5.10.110-debugx
            append earlyprintk console=ttyFIQ0,1500000n8 rw init=/sbin/init rootfstype=ext4 rootwait root=/dev/mmcblk0p2 net.ifnames=0 usbcore.autosuspend=-1 mem=4G

        label kernel-5.10.110-debugx
            kernel /Image_5.10.110-debugx
            initrd /initrd.img
            devicetreedir /dtbs/5.10.110-debugx
            append earlyprintk console=ttyFIQ0,1500000n8 rw init=/sbin/init rootfstype=ext4 rootwait root=/dev/mmcblk0p2 net.ifnames=0 usbcore.autosuspend=-1

        label mainline_kernel
            kernel /Image_6.2.0-rc1
            initrd /initrd.img
            devicetreedir /dtbs/6.2.0-rc1
            append earlyprintk console=ttyFIQ0,1500000n8 rw init=/sbin/init rootfstype=ext4 rootwait root=/dev/mmcblk0p2 net.ifnames=0 usbcore.autosuspend=-1


## Ethernet

|  port  |   speed   | label |
|--------|-----------|-------|
|  eth0  |  1 Gbps   | WLAN  |
|  eth1  |  2.5 Gbps | LAN0  |
|  eth2  |  2.5 Gbps | LAN1  |



    eth0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
            ether 5a:1a:db:02:99:23  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
            device interrupt 74  

    eth1: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
            ether fe:a1:f3:98:92:b9  txqueuelen 1000  (Ethernet)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
            device interrupt 107  

    eth2: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 192.168.254.10  netmask 255.255.0.0  broadcast 192.168.255.255
            inet6 2804:7f4:3595:3a50:1a07:dcba:d14c:d298  prefixlen 64  scopeid 0x0<global>
            inet6 fe80::a6fc:8d31:62bd:b390  prefixlen 64  scopeid 0x20<link>
            ether 22:97:5b:c2:1a:67  txqueuelen 1000  (Ethernet)
            RX packets 364  bytes 36157 (35.3 KiB)
            RX errors 0  dropped 8  overruns 0  frame 0
            TX packets 425  bytes 38398 (37.4 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
            device interrupt 117  

    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            inet6 ::1  prefixlen 128  scopeid 0x10<host>
            loop  txqueuelen 1000  (Local Loopback)
            RX packets 24  bytes 2564 (2.5 KiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 24  bytes 2564 (2.5 KiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0


## USB Camera Test

USB Camera test with H264, YUY2, MJPEG pixel types

## H264

H264 USB Camera test
![H264 Display 1920x1080](https://raw.githubusercontent.com/avafinger/nanopi-r6s-debian11-xfce/main/nanopi-r6s-camera-h264.png)

The H264 camera used for the test is the HVBCAM-1710, 1920x1080 H264 30 fps.
The H264 frame is decoded and displayed with gstreamer.

gstreamer pipeline:

    GST_GL_API=gles2 GST_GL_PLATFORM=egl gst-launch-1.0 v4l2src device=/dev/video2 ! video/x-h264,width=1920,height=1080,framerate=30/1 ! h264parse ! mppvideodec ! glimagesink

H264 USB Camera + MJPEG camera test on 4K display
![H264 + MJPEG Camera + 4K Display](https://raw.githubusercontent.com/avafinger/nanopi-r6s-debian11-xfce/main/4k_cam2.png)

![Stressing USB2.0 with H264 + MJPEG Camera + 4K Display](https://raw.githubusercontent.com/avafinger/nanopi-r6s-debian11-xfce/main/4k_cam1.png)

gstreamer pipeline for the MJPEG (720 HD):

    GST_GL_API=gles2 GST_GL_PLATFORM=egl gst-launch-1.0 v4l2src device=/dev/video6 ! image/jpeg,width=1280,height=720,framerate=30/1 ! mppjpegdec ! glimagesink


to be completed.

## Hardware decoder

to be completed.

## Hardware encoder

to be completed.

## Rockchip NPU

to be completed.

## Mini Router

to be completed.

## Releases

Releases will be available here:

    https://github.com/avafinger/nanopi-r6s-debian11-xfce/releases
    
* **v1**
  
  **v1** is the raw OS image we will use to boot up linux and install OS image with tar.gz files and flash the **v2** OS Image to the eMMC device.
  
  https://github.com/avafinger/nanopi-r6s-debian11-xfce/releases/tag/v1
  

## Issues

RGA breaks for some pixel formats and force FFmpeg to use software rendering.

## Acknowledgments

I would like to thanks the folks at FrienlyElec for providing the sample used here.
