diving-meta-ros
===============

This repository shows how to create a bitbake recipe for a ROS package and cross-compile it using the [meta-ros] project.

(For this tutorial the Angstrom Distribution was used. Refer to this [instructions](http://www.angstrom-distribution.org/building-angstrom) to set up an Angstrom workspace).

Building blocks:
* **Angstrom distribution**: Linux distribution for a variety of embedded devices. The distribution is the result of a unification of developers from the OpenZaurus, OpenEmbedded, and OpenSIMpad projects.
* **meta-ros project**: layer to provide ROS Groovy Galapagos in an OpenEmbedded Linux system.
* [beginner_tutorials](https://github.com/vmayoral/beginner_tutorials): Simple talker/listerner ROS package written in python.

The following sections will dive into the process of creating a recipe for the beginner_tutorials ROS package and cross-compile it:

## The ROS package

The package selected for this example is the [beginner_tutorials](https://github.com/vmayoral/beginner_tutorials), a simple package written in python that creates a *listener* and a *talker* (subscriber and publisher). The package file structure is presented below:

├── CMakeLists.txt
├── README.md
├── ipkg 
│   └── beginner-tutorials_1.0.0+gitrAUTOINC+529536a8d19371beeae62eb89b6611aa02737629-r0_armv7a-vfp-neon.ipk
├── msg
│   └── Num.msg
├── package.xml
├── scripts
│   ├── listener.py
│   └── talker.py
└── srv
    └── AddTwoInts.srv

(*Note: Do not worry about the ipkg directory. It contains the cross-compiled version of this package ready-to-install in the beaglebone*) 

If you decide to [create your own ROS package](http://www.ros.org/wiki/ROS/Tutorials/CreatingPackage), your file structure shouldn't be much different.

(*Tip: Before going into the embedded system it might be wise to build and test your package in a [ROS desktop-installation](http://www.ros.org/wiki/groovy/Installation/Ubuntu)*)

## The bitbake recipe

Once we have the package tested, we need to create a bitbake recipe that will fetch the source, cross-compile it and generate an output that meet our needs. In this example the bitbake recipe will fetch the source from a **git repository**. The source for the recipe:

```
  1 DESCRIPTION = "Beginner_tutorials, talker/listener ROS package"
  2 SECTION = "devel"
  3 LICENSE = "MIT"
  4 LIC_FILES_CHKSUM = "file://package.xml;;beginline=16;endline=16;md5=05c8b019cf5b0834bc5e547a14f26ca3"
  5 
  6 DEPENDS = "roscpp catkin rospy std-msgs"
  7 RDEPENDS = "roscpp rospy std-msgs"
  8 
  9 SRC_URI = "git://github.com/vmayoral/beginner_tutorials.git"
 10 
 11 SRCREV = "${AUTOREV}"
 12 PV = "1.0.0+gitr${SRCPV}"
 13 
 14 S = "${WORKDIR}/git"
 15 
 16 inherit catkin

```


## Cross-compiling


