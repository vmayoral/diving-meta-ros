diving-meta-ros
===============

This repository shows how to create a bitbake recipe for a ROS package and cross-compile it using the [meta-ros](https://github.com/bmwcarit/meta-ros) project.

(For this tutorial the Angstrom Distribution was used. Refer to this [instructions](http://www.angstrom-distribution.org/building-angstrom) to set up an Angstrom workspace).

Building blocks:
* **Angstrom distribution**: Linux distribution for a variety of embedded devices. The distribution is the result of a unification of developers from the OpenZaurus, OpenEmbedded, and OpenSIMpad projects.
* **meta-ros project**: layer to provide ROS Groovy Galapagos in an OpenEmbedded Linux system.
* [beginner_tutorials](https://github.com/vmayoral/beginner_tutorials): Simple talker/listerner ROS package written in python.

The following sections will dive into the process of creating a recipe for the beginner_tutorials ROS package and cross-compile it:

## The ROS package

The package selected for this example is the [beginner_tutorials](https://github.com/vmayoral/beginner_tutorials), a simple package written in python that creates a *listener* and a *talker* (subscriber and publisher). The package file structure is presented below:

```
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
```


If you decide to [create your own ROS package](http://www.ros.org/wiki/ROS/Tutorials/CreatingPackage), your file structure shouldn't be much different.

(Note: Do not worry about the ipkg directory. It contains the cross-compiled version of this package ready-to-install in the beaglebone).

(Tip: Before going into the embedded system it might be wise to build and test your package in a [ROS desktop-installation](http://www.ros.org/wiki/groovy/Installation/Ubuntu))

## The bitbake recipe

Once we have the package tested, we need to create a bitbake recipe that will fetch the source, cross-compile it and generate an output that meet our needs. In this example the bitbake recipe will fetch the source from a **git repository**. Take a look at the [OpenEmbedded User Manual](https://www.google.es/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&ved=0CDMQFjAA&url=http%3A%2F%2Fopenembedded.googlecode.com%2Ffiles%2Fusermanual.pdf&ei=jK_vUeqSJsa47Abd1oHgAw&usg=AFQjCNFmStDMDs65G0lo6IdzYo4Ohv5NWQ&sig2=RyN-VxEufaDaDJR_7d-6_A&bvm=bv.49641647,d.ZGU) before starting with the recipe.

The source for the recipe:

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

Let's analyze the recipe:

```
  1 DESCRIPTION = "Beginner_tutorials, talker/listener ROS package"
  2 SECTION = "devel"
  3 LICENSE = "MIT"
```
Description of the package, section and the license of it.

```
  4 LIC_FILES_CHKSUM = "file://package.xml;;beginline=16;endline=16;md5=05c8b019cf5b0834bc5e547a14f26ca3"
```
The checksum of the license line in the [package.xml file of the package](https://github.com/vmayoral/beginner_tutorials/blob/master/package.xml#L16).

```
  6 DEPENDS = "roscpp catkin rospy std-msgs"
  7 RDEPENDS = "roscpp rospy std-msgs"
```
Build and run dependencies which can be identified directly from the package.xml (lines [42-48](https://github.com/vmayoral/beginner_tutorials/blob/master/package.xml)).

----

Note that **std_msgs** is written in the recipe as **std-msgs**. This is because open-embedded package names are with hyphen instead of underscores (which is the convention used in ROS packages)

---

```
  9 SRC_URI = "git://github.com/vmayoral/beginner_tutorials.git"
```
Source of the package to cross-compile. In this case the source is fetched from a git repository. It's also common to specify the branch using **branch=hydro-devel**.

```
 11 SRCREV = "${AUTOREV}"
 12 PV = "1.0.0+gitr${SRCPV}"
```
Source revision and package version. 

```
 14 S = "${WORKDIR}/git"
``` 
Directory where the source files are located.

```
 16 inherit catkin
```
Inherit the [catkin bbclass](https://github.com/bmwcarit/meta-ros/blob/master/classes/catkin.bbclass).


## Cross-compiling


