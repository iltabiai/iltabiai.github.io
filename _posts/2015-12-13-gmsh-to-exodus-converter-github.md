---
layout: post
title:  "Gmsh to Exodus converter"
date:   2015-12-12 14:34:00
comments: true
categories: [gmsh, exodus, peridigm, peridynamics]
tags: gmsh, exodus, peridigm, peridynamics
---

   At the [USNCCM13](http://13.usnccm.org/), we discussed the fact that (currently) [Peridigm](https://peridigm.sandia.gov/) only accepts [Exodus II](https://cubit.sandia.gov/public/13.2/help_manual/WebHelp/finite_element_model/exodus/exodus2_file_specification.htm) mesh files. The only software that generates *Exodus II* mesh files is the powerful [Cubeit](https://cubit.sandia.gov/). *Cubeit*'s license can also be expensive, especially for someone who only wants to use the conversion to *Exodus II* feature to import geometries in *Peridigm*...
   [Patrick](http://diehlpk.github.io/) discussed the possibility of writing our own conversion script that converts a widely used free meshing format to *Exodus II*. We eventually decided to use [Gmsh](http://geuz.org/gmsh/) as an input for our script, which then converts *Gmsh* meshes to *Exodus II*. 
   
   The [code is available here](https://github.com/diehlpk/gmsh-exodus-converter), everythhign is written in *python* so the only requirement is [VTK >=5.8](http://www.vtk.org/) with the python wrapper *(sudo apt-get install python-vtk)*. We currently support *triangles* and *quadrangles* as 2D elements and *tetrahedrons* only for 3D elements.
   
## Example

# 2D


<table style="width:100%">
  <tr>
    <td>Mesh generated with gmesh</td>
    <td>Exodus geometry visualized with paraview</td> 
  </tr>
  <tr>
    <td>
<div align="center">
<img src="{{ site.url }}/assets/example_mesh_1.png.png"/>
</div>
    </td>
    <td>
<div align="center">
<img src="{{ site.url }}/assets/example_exodus_1.png"/>
</div>
    </td> 

  </tr>
</table>

 | 
:------------------------:|:----------------------------------------:
![Mesh](./assets/example_mesh_1.png?raw=true "Mesh generated with gmesh")|![Exodus](./assets/example_exodus_1.png?raw=true "Mesh generated with gmesh")

# 3D

Mesh generated with gmesh | Exodus geometry visualized with paraview
:------------------------:|:----------------------------------------:
![Mesh](./assets/3D_Cube_gmsh.png?raw=true "Mesh generated with gmesh")|![Exodus](./assets/3D_Cube_exodusII.png?raw=true "Mesh generated with gmesh")
   

* [Get started with the README here](https://github.com/diehlpk/gmsh-exodus-converter)
* [Let us know here](https://github.com/diehlpk/gmsh-exodus-converter/issues) if you have any troubles using it
* Think it could be better? [Clone the repo, improve it and send a pull request](https://help.github.com/articles/using-pull-requests/)


Many thanks to [Patrick](http://diehlpk.github.io/) for working with me on this project, I learned a lot about meshes and *VTK*. #notscaredofmeshesanymore

{% include twitter_plug.html %}

