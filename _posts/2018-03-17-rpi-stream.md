---
layout: post
title:  "Stream Raspberry Pi microphone through Wifi"
categories: []
tags:
date: 2018-03-17 16:08:00
comments: true
---

The goal of this article is to show how you can easily stream the sound captured by a microphone connected to the Raspberry Pi to another distant machine. In order to do this, you will need:

* Raspberry pi 3
* Microphone ([I used this one](https://www.amazon.ca/gp/product/B0058MJX4O/ref=oh_aui_detailpage_o03_s00?ie=UTF8&psc=1) )
* USB soundcard ([I used](https://www.amazon.ca/gp/product/B00NMXY2MO/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1) )

We'll also need the **vlc** package, you can download/install that on your Raspberry Pi using:

`sudo apt-get install vlc`

Next, you should plug your USB sound card to the RAspberry Pi and plug the microphone to the USB soundcard.
We can then check if the Raspberry Pi sees the USB soundcard using:

```bash
pi@raspberrypi:~ $ cat /proc/asound/modules
```

This command should show that the Raspberry pi detected two soundcards:

```
0 snd_bcm2835
1 snd_usb_audio
```
Now let's check that the microphone part of the USB soundcard is properly detected:

```bash
pi@raspberrypi:~ $ arecord –l

**** List of CAPTURE Hardware Devices ****
card 1: Device [USB PnP Sound Device], device 0: USB Audio [USB Audio]
Subdevices: 1/1
Subdevice #0: subdevice #0
```
The Raspberry Pi is here showing that the microphone plugged to the soundcard is detected as device 0, plugged to soundcard 1.

We can then test this microphone, first by simply recording a few seconds and checking that it works:
```
arecord -D hw:1,0 -d 10 -f cd test.wav -c 1
```
Ẁhere:

* `-D`: We want to use a recording device
* `Hw:1,0`: We'll use device 0 plugged to soundcard 1
* `-d 10`: We'll record 10 seconds of sound
* `-f cd test.wav`: The file will be saved in the current folder, and will be names `test.wav`
* `-c 1`: Use 1 if you are using a mono microphone, 2 for stereo

After 10 seconds, you should have control over the prompt again, and you can listen to the recodring using:
```
aplay test.wav
```
_(If your Raspberry Pi is plugged to a TV using HDMI the defaul sound output will be the HDMI)_
If you are able to listen to the sound recorded by the mic and it seems fine, we can now try to send it over the network.

The first method we can try is to simply send the uncompressed audio feed from the microphone using SSH tunneling:

```
arecord -D plughw:1,0 -f dat | ssh -C user@remoteip aplay -f dat
```
Where `user@remoteip` are the SSH credentials of the distant machine on the network that you'd like to stream the feed to. You will not need to do anything on the distant machine (except turning ON the audio), the feed will automatically start. The distant machine needs to have SSH activated.
This solution induces a latency, on my Raspberry Pi, of about 1-5 seconds and appears to consume quite a lot of CPU reesources.

Another solution, which appears to be more efficient, consists in compressing the feed in MP3 before sending it over the network. This is possible using VLS and the RPT protocole, you can simply input:
```
cvlc -vvv alsa://hw:1,0 --sout \
 '#transcode{acodec=mp3,ab=128}:rtp{mux=ts,dst=239.255.1.1,sdp=sap}'
 ```

 In this case, the audio feed will be sent as an 128kbps MP3 feed to the 239.255.1.1 destination. To access it, input `rpt://239.255.1.1` in the network feeds of VLC on any machine connected to the same network.
 The audio feed should then start on VLC.

 __I have sources for this and will add it later.___

{% include twitter_plug.html %}