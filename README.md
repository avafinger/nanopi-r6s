## nanopi-r6s-debian11-xcfe-minimal experimental image

Debian 11 (11.6) with xfce (X11) for the NanoPi R6S base image for testing hardware functionality and debugging.

Table of Contents:

- [Introduction](#introduction)
- [Build Your Kernel](#build-your-kernel)
- [USB Camera Test](#usb-camera-test)
  - [H264](#h264)
    - [Gstreamer pipeline H264](#gstreamer-pipeline-h264)
    - [Python Gstreamer code](#python-gstreamer-code)
  - [YUY2](#yuy2)
    - [Gstreamer pipeline YUY2](#gstreamer-pipeline-yuy2)
- [Hardware decoder](#hardware-decoding)
  - [Gstreamer](#gstreamer)
  - [FFmpeg](#ffmpeg)
- [Hardware encoder](#hardware-encoding)
- [Rockchip NPU](#npu)
  - [SDL2 demo](#sdl2-demo)
  - [SDL3 demo](#sdl3-demo)
- [Mini Router](#mini-router)
- [Issues](#issues)
- [Acknowledgments](#acknowledgments)

## Introduction

The OS Image is "lean and mean", X11 (Debian 11.6) with Hardware Acceleration and the latest rockchip multimedia and kernel.
You can load and run different kernels for testing.
This is a WiP and is used to test RK3588/RK3588s hardware features, use as is.

## Build Kernel

to be completed.

## USB Camera Test

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

## Issues

RGA breaks for some pixel formats and force FFmpeg to use software rendering.

## Acknowledgments

I would like to thanks the folks at FrienlyElec for providing the sample used here.
