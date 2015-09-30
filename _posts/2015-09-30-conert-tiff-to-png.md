---
layout: post
title:  "Convert batch of 8 bit .tiff images to .png"
date:   2015-09-30 16:30:00
categories: [ubuntu, image, tiff, png]
tags: ubuntu, image, tiff, png
comments: true
---

When capturing images from a lab camera, you might find yourself with a bunch of *.tiff* images.

The first thing you will need is the *ImageMagick* package for Ubuntu. You can get it using:

    sudo apt-get install imagemagick
   
This package contains a very convenient **convert** function, it can be used to convert a *.tiff* image into a *.png* while keeping transparency using:

    convert inputfile.tiff -transparent white outputfile.png
    
You can then use this command to recursively treat any file in the folder with a simple **grep** and some batch scripting:

    #!/bin/sh
    for file in `ls | grep tif`
    do
        convert "$file" -fuzz 5% -transparent white "${file}.png"
        echo "writing ${file}.png"
    done
    
To use it, you simply need to put a bunch of *.tiff* files in the same folder, and execute the script.
If your files are recorded as *.tif* and not *.tiff*, you will need to change it in the second line of the script.

Final tip: If any other file in the folder (it can be the script itself) contains the letters *T*-*I*-*F*-*F* in this order, it will be transformed into a *.png* even if it is a text file.
        
{% include twitter_plug.html %}

