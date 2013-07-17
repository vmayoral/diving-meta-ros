diving-meta-ros
===============

This repository shows how to create a bitbake recipe for a ROS package and cross-compile it using the [meta-ros] project.

(For this tutorial the Angstrom Distribution was used. Refer to this [instructions](http://www.angstrom-distribution.org/building-angstrom) to set up an Angstrom workspace).

Building blocks:
* Angstrom distribution: Linux distribution for a variety of embedded devices. The distribution is the result of a unification of developers from the OpenZaurus, OpenEmbedded, and OpenSIMpad projects.
* meta-ros project: layer to provide ROS Groovy Galapagos in an OpenEmbedded Linux system.
* [beginner_tutorials](https://github.com/vmayoral/beginner_tutorials): Simple talker/listerner ROS package written in python.

The following sections will dive into the process of creating a recipe for the beginner_tutorials ROS package and cross-compiling it:

## The ROS package

