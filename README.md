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
  - [SDL2 demo](#sdl2 demo)
  - [SDL3 demo](#sdl3 demo)
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

USB H264 Camera test
![H264 Display 1920x1080](https://raw.githubusercontent.com/avafinger/nanopi-r6s-debian11-xfce/main/nanopi-r6s-camera-h264.png)

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
