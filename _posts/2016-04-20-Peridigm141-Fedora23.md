---
layout: post
title:  "Compiling Peridigm 1.4.1 on Fedora 23"
date:   2016-04-20 16:32:00
comments: true
categories: [peridynamics, fedora, tips]
tags: peridynamics
---

Thanks to [Patrick Diehl](https://twitter.com/diehlpk), I have been working at the Bonn University, more precisely at the [Institut für Numerische Simulation](http://www.ins.uni-bonn.de/institut/) in order to develop some new tools to _better explore some of my experimental results through the Peridynamics framework_ (writing it in italics because I am still not sure that it is what we are actually doing, welcome to Research). While I am there, Patrick guided me installing [Peridigm](https://peridigm.sandia.gov/) on my machine. In this blogpost, I will re-write the steps to do so in such a way that it is clear even for someone who is not familiar with the Linux environment. If you are pretty familiar with the Linux environment, you should head (toward Patrick's blogpost)[http://diehlpk.github.io/2016/04/22/builing-peridigm-f-23.html], which is more for experienced users.

First, you need a [Virtual machine sfotware](https://www.virtualbox.org/) if you are on Windows, then grab the image of [Fedora 23](https://getfedora.org/de/workstation/). [Create a new Virtual Machine](http://linuxpitstop.com/install-virtualbox-on-ubuntu/) and [install Fedora](http://linuxpitstop.com/install-fedora-22-workstation-on-virtualbox/) on the VM.

 Once your machine is installed and working, you need to install a few packages. To do so, open a terminal and type:

```bash
sudo dnf install mpich-devel mpich netcdf-mpich-devel netcdf-mpich hdf5-mpich-devel hdf5-mpich netcdf-fortran-mpich-devel boost-mpich-devel boost-mpich blas-devel blas lapack-devel lapack gcc-c++
```

[DNF](https://en.wikipedia.org/wiki/DNF_%28software%29) is the equivalent of `apt-get` from Ubuntu in Fedora. 

You will also need to get a *group* of softwares. This is a great feature of *DNF* which will get and install a bunch of packages necessary for a certain field or usage, in this case, you will need to:
```
sudo dnf  groupinstall "X Software Development"
```

Once you have the necessary packages, you will need to get an compile *Trilinos* on your machine. This guide considers that you are using *Trilinos 12.6.2*, you can [get it here](https://trilinos.org/download/). Unpack Trilinos on your machine, and use the terminal to enter the unpacked folder. We are going to create a *build* folder inside it in order to compile it properly.

```bash
mkdir build && cd build
```

We will then need to load the previously installed [mpich](https://www.mpich.org/) package, which is necessary for multiprocessor computations. We need it to compile Trilinos:
```
module load mpi/mpich-x86_64
```
Now, let's create an empty text file in the previously created `build` folder and paste a list of instruction for the C compiler, explaining how it should actually compile Trilinos. This file should be called `build.sh`:

```bash
cmake \
-D CMAKE_INSTALL_PREFIX:PATH=/home/ilyass/trilinos-12.6.2-source \
-D CMAKE_CXX_FLAGS:STRING="-O2 -ansi -pedantic -ftrapv -Wall -Wno-long-long" \
-D CMAKE_BUILD_TYPE:STRING=RELEASE \
-D Trilinos_WARNINGS_AS_ERRORS_FLAGS:STRING="" \
-D Trilinos_ENABLE_ALL_PACKAGES:BOOL=OFF \
-D Trilinos_ENABLE_Teuchos:BOOL=ON \
-D Trilinos_ENABLE_Shards:BOOL=ON \
-D Trilinos_ENABLE_Sacado:BOOL=ON \
-D Trilinos_ENABLE_Epetra:BOOL=ON \
-D Trilinos_ENABLE_EpetraExt:BOOL=ON \
-D Trilinos_ENABLE_Ifpack:BOOL=ON \
-D Trilinos_ENABLE_AztecOO:BOOL=ON \
-D Trilinos_ENABLE_Amesos:BOOL=ON \
-D Trilinos_ENABLE_Anasazi:BOOL=ON \
-D Trilinos_ENABLE_Belos:BOOL=ON \
-D Trilinos_ENABLE_ML:BOOL=ON \
-D Trilinos_ENABLE_Phalanx:BOOL=ON \
-D Trilinos_ENABLE_Intrepid:BOOL=ON \
-D Trilinos_ENABLE_NOX:BOOL=ON \
-D Trilinos_ENABLE_Stratimikos:BOOL=ON \
-D Trilinos_ENABLE_Thyra:BOOL=ON \
-D Trilinos_ENABLE_Rythmos:BOOL=ON \
-D Trilinos_ENABLE_MOOCHO:BOOL=ON \
-D Trilinos_ENABLE_TriKota:BOOL=OFF \
-D Trilinos_ENABLE_Stokhos:BOOL=ON \
-D Trilinos_ENABLE_Zoltan:BOOL=ON \
-D Trilinos_ENABLE_Piro:BOOL=ON \
-D Trilinos_ENABLE_Teko:BOOL=ON \
-D Trilinos_ENABLE_SEACASIoss:BOOL=ON \
-D Trilinos_ENABLE_SEACAS:BOOL=ON \
-D Trilinos_ENABLE_SEACASBlot:BOOL=ON \
-D Trilinos_ENABLE_Pamgen:BOOL=ON \
-D Trilinos_ENABLE_EXAMPLES:BOOL=OFF \
-D Trilinos_ENABLE_TESTS:BOOL=ON \
-D TPL_ENABLE_Matio=OFF \
-D TPL_ENABLE_HDF5:BOOL=ON \
-D TPL_ENABLE_Netcdf:BOOL=ON \
-D TPL_ENABLE_MPI:BOOL=ON \
-D TPL_ENABLE_BLAS:BOOL=ON \
-D TPL_ENABLE_LAPACK:BOOL=ON \
-D TPL_ENABLE_Boost:BOOL=ON \
-D CMAKE_VERBOSE_MAKEFILE:BOOL=OFF \
-D Trilinos_VERBOSE_CONFIGURE:BOOL=OFF \
-D Netcdf_INCLUDE_DIRS=/usr/include/mpich-x86_64/ \
-D HDF5_INCLUDE_DIRS=/usr/include/mpich-x86_64/ \
..
```

*It is important that you modify the second line of this file by changing the absolute path to the location where you unpacked Trilinos on your machine.*
We will then change the authorization of the `build.sh` file to make it executable by your Linux user by typing `chmod a+x build.sh` and can configure the compilation of Trilinos by executing this file, just type: `./build.sh`
The Cmake compiler will now configure Trilinos for compilation, it will take some time....
After that, we can finally install Tirlinos, by typing:

```bash
make -j
make install
```

This is also going to take some time, but after it, Trilinos will be installed on your machine. Congrats!
Next, we are going to compile *Peridigm*. We are directly going to pull the latest release of Peridigm from the [Github repository of Peridigm](https://github.com/peridigm/peridigm). Go to your home folder by simply typing `cd` in the terminal, then input line after line:

```bash
git clone https://github.com/peridigm/peridigm.git
git fetch
git checkout release-1.4.1
```

You should now have a folder called `peridigm` in you home folder. We are now going to create a `build` folder in it (similar to what we did for Trilinos) to compile Peridigm:

```bash
cd peridigm 
mkdir build
cd build
```

The next step is also similar to what we previously did with Trilinos, you will need to create a `build.sh` text file in the `build` folder and paste the following content in it:

```bash
cmake \
-D CMAKE_BUILD_TYPE:STRING=Release \
-D Trilinos_DIR:PATH=/home/ilyass/trilinos-12.6.2-source/lib/cmake/Trilinos/ \
-D CMAKE_C_COMPILER:STRING=/usr/lib64/mpich/bin/mpicc \
-D CMAKE_CXX_COMPILER:STRING=/usr/lib64/mpich/bin/mpicxx \
-D CMAKE_CXX_FLAGS:STRING="-O2 -Wall -ansi -pedantic -Wno-long-long -ftrapv -Wno-deprecated -std=gnu++11" \
..
```

Again, *do not forget to change the absolute path on the third line of the previous script to the location where you installed Trilinos on your machine*.
We can now configure the Cmake compiler by executing the `build.sh` file:

```bash
chmod a+x build.sh
./build.sh
```

Wait for it....
And you can then install *Peridigm*:

```bash
make  -j

```

You should be done ! To execute Peridigm, you will need to type in your Terminal `./src/Peridigm` when you are in the `peridigm` folder. And that's it !

Now if you want to be able to run `Peridigm` from any folder in your machine, you need to modify your `.bashrc` to tell it where the `Peridigm` executable is:

```bash
cd
nano .bashrc
```

And add the following line at the end of the file:

```bash
export PATH=$PATH:/home/ilyass/peridigm/src/
```

Restart your terminal, and you're done ! You can now type the `Peridigm` command anywhere in your temrinal and it will call out the software.

Many thanks to [Patrick Diehl](https://twitter.com/diehlpk) :)

{% include twitter_plug.html %}

