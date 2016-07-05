---
layout: post
title:  "Read WiFi password in Win10"
date:   2016-07-05 16:17:00
comments: true
categories: [windows]
tags: windows
---

Click on the start button then type `CMD`.

Input the following line then press *Enter*:
```
netsh wlan show profile name="ENTER_SSID_HERE" key=clear
```

{% include twitter_plug.html %}

