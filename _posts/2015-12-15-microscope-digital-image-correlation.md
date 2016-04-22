---
layout: post
title:  "Optical microscope Digital Image Correlation"
date:   2015-12-15 22:40:00
comments: true
categories: [digital image correlation]
tags: dic
---

   A small dogbone specimen containing one unique fiber is prepared in such a way that its surface provides a good enough level of grey in order to do Digital Image Correlation without the need of an additional speckle pattern.
   The purpose of the experiment is to evaluate the deformation field and observed damage mechanisms around the fiber (\\(100 \mu m diameter\\)) while the specimen is experiencing quasi-static tensile load.

<table style="width:100%">
  <tr>
    <td>
    <div align="center">
    <img src="{{ site.url }}/assets/minispeckle.png"/>
    </td>
    <td>
    <iframe width='600' height='635' src='https://pictures.lytro.com/ilyasst/pictures/1089008/embed' frameborder='0' allowfullscreen scrolling='no'></iframe>
    </td> 
  </tr>
</table>


   The specimen is loaded in a microtraction machine adapted to perform the test under the microscope's lens.

<div align="center">
<iframe width='600' height='635' src='https://pictures.lytro.com/ilyasst/pictures/1089009/embed' frameborder='0' allowfullscreen scrolling='no'></iframe>
</div>

   The test is stopped 8 times in order to take pictures using the amazing **Olympus OLS4100 laser confocal microscope**, it provides us with height information for each pixel and sweeps through a range of the z-height to provide a crisp clear definition at every corner of the picture.

<table style="width:100%">
  <tr>
    <td>First image</td>
    <td>Final image</td> 
  </tr>
  <tr>
    <td>
<div align="center">
<img src="{{ site.url }}/assets/Speck001_x10_02_scale.jpg"/>
</div>
    </td>
    <td>
<div align="center">
<img src="{{ site.url }}/assets/Speck001_x10_08_1.jpg"/>
</div>
    </td> 
  </tr>
</table>

   On the first image, it is possible to distinguish the fiber but quite hard. We used the laser instead of white light to maximize pixels' intensity.
   The white areas are actually reflecting the laser and will make the image correlation analysis impossible around those defects (glue? contamination? the specimen was cleaned though. **EDIT: those are actually bubbles, voids, in the epoxy that are partially revealed by the polishing.**).
   
   On the final image, it is clearly possible to see that the fiber completely detached from the matrix. The material also seems to have shifted along the \\(45^o\\) line passing through the fiber. The white areas' shapes and colors also changed erathically, which is why they cannot be considered in the region of interest during the analysis.
   
**These experiments were possible with the help of [Damien Texier](https://www.researchgate.net/profile/Damien_Texier) and were done at École de Technologie Supérieur, Montréal.**

{% include twitter_plug.html %}

