---
layout: post
title: Turn you iphone into a ros camera node under ubuntu
---

Durning the last four weeks i was exploring some monocular SLAM projects. At first i use my laptop's own camera as a image sensor, which is not good enough for capturing high quality pictures. Also, the camera and the screen are on the same side so it's quite inconvenient to monitoring those SLAM systems with live video input. I have a DC and an ihpone at hand so i decide to turn then into separate or wireless sensors for my laptop. In this post i'll focus on the approch of turning iphone(maybe ipad, too) into a ros node unter ubuntu 14.04. The basic idea came from [this thread](http://ubuntuforums.org/showthread.php?t=2092935)
 
1. Make your iphone as a webcam
It's not so easy under linux especially you don't have a customized app that just meed the needs. I use [Mini WebCam](http://itunes.apple.com/cn/app/mini-webcam/id379896463?mt=8) to run on my iphone as a live video server. It's free and easy to use. 
Open Mini WebCam on your iphone and press the start button. It will then stream of videos to your WLAN. Make sure your iphone and PC are in the same WLAN. You can check the video stream via web browser on ubuntu. Just type the address displayed on iphone into your Firefox's address bar. If you see the live video in your browser then you are done.

2. Create virtual video devices via v4l2loopback
In this step we will create a virtual video devices "/dev/video*" for ros from the live video we created. 

First we need a kernel module called [v4l2loopback](https://github.com/umlaeute/v4l2loopback.git) to create V4L2 loopback devices. You may try install via apt:
		sudo apt-get install v4l2loopback-dkms
or from source:
		sudo su
		git clone https://github.com/umlaeute/v4l2loopback.git
		cd v4l2loopback
		make && make install
However, the apt-get method may fail durning install and i didn't figure out why. After install complete you may load the module via:
		sudo modprobe v4l2loopback
To check the virtual device you've created, run:
		v4l2-ctl --list-devices
The output will display your new device for example(* is your device index):
		Dummy video device (0x0000) (platform:v4l2loopback-000):
			/dev/video*

3. Use Gstreamer to create a v4l2 video source from your iphone live video
Run the command below to feed the live video data to your virtual device:
		gst-launch souphttpsrc location=http://<ip>[:<port>]/ ! jpegdec ! ffmpegcolorspace ! v4l2sink device=/dev/video*
(ip/port should be replaces with your own address and * should be replaced with your own index from step 2)
[More sample usages of Gstreamer](http://wiki.oz9aec.net/index.php/Gstreamer_cheat_sheet)

4. Create a ros node from this device
I use the ros package [video_stream_opencv](wiki.ros.org/video_stream_opencv) to create a camera node from the device. Install this ros package, cd into it's /launch dirctory and make a copy of webcam.launch, (e.g. mywebcam.launch). Edit your launch file and modify the "video_stream_provider" value to your device index and save. Now you may launch the file from ros:
		roslaunch video_stream_opencv mywebcam.launch 
By default the launch file will start a image view node so you will see a small window display the video from your iphone. If you don't see the video playing try to check your rostopic and see if the image topic exist(e.g. /webcam/image_raw)


There is another impressive way which use epoccam on iphone and [epoccam_linux](https://github.com/ohwgiles/epoccam_linux.git) as driver on ubuntu. epoccam_linux use tray icons which is not enabled by default on ubuntu 14.04 so you may need a few more steps to get it working. I'm new to C so i didn't figure out how to get the video stream from epoccam on ubuntu. But it provides two video size: 1280x720(H26430fps)
and 640x480(H264 30fps). It also has a automatic connection feature in WLAN. Anyway, worth trying.
 


