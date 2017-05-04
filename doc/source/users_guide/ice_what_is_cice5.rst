.. _overview:

*******************
 What is CICE5
*******************

This User’s Guide accompanies the CESM2.0 User’s Guide, and is
intended for those who would like to run CICE coupled, on a supported
platform, and “out of the box”.  It includes a quick start guide for
downloading the CESM2 source code and input datasets, and information
on how to configure, build and run the model. The supported
configurations and scripts for building the fully coupled model are
also described in the CESM2 User’s Guide.

http://www.cesm.ucar.edu/models/cesm2.0/

The CICE User’s Guide is intended for users interested in making
modifications to the ice model scripts or namelists within the CESM.
Users interested in modifying the source code or using the standalone
version should see the CICE Code Reference/Developer’s Guide
:cite:`cice15`.

CICE5.1.2 is the latest version of the Los Alamos Sea Ice Model,
sometimes referred to as the Community Ice CodE :cite:`cice15`. It is
the result of a community effort to develop a portable, efficient sea
ice model that can be run coupled in a global climate model or
uncoupled as a stand-alone ice model. It has been released as the sea
ice component of the Community Earth System Model (CESM), a
fully-coupled global climate model that provides simulations of the
earths past, present and future climate states. CICE5 is supported on
high- and low-resolution Greenland Pole and tripole grids, which are
identical to those used by the Parallel Ocean Program (POP) ocean
model. The high resolution version is best suited for simulating
present-day and future climate scenarios while the low resolution
option is used for paleoclimate simulations and debugging. An
uncoupled version of CICE is available separately from Los Alamos
National Laboratory: http://oceans11.lanl.gov/trac/CICE.

The standalone CICE provides a means of running the sea ice model
independent of the other CESM components. It can read in atmospheric
and ocean forcing, which eliminates the need for the flux coupler, and
the atmosphere, land and ocean data models. It can be run on a reduced
number of processors, or without MPI (Message Passing Interface) for
researchers without access to these computer resources.

The physics in the uncoupled ice model are identical to those in the
ice model used in the fully coupled system.  CICE is a
dynamic-thermodynamic model that includes a subgrid-scale ice
thickness distribution (:cite:`cice15`). It uses the energy conserving
thermodynamics of :cite:`turner15` or :cite:`bitz01`, has multiple
layers in each thickness category, and accounts for the influences of
brine pockets within the ice cover. The ice dynamics utilizes the
elastic-viscous-plastic (EVP) rheology of :cite:`hunk97`. Sea ice
ridging has the options of :cite:`roth75b` and :cite:`thor75` or the
newer ridging scheme of :cite:`lipscomb07`.  A slab ocean
mixed layer model is included.  A Scientific Reference Guide
:cite:`cice15` is available that contains more detailed information on
the model physics.

An attempt has been made throughout this document to provide the
following text convention. Variable names used in the code are
``typewritten``. Subroutine are given in *italic*,
and file and directory names are in **boldface**.

======================
 What’s new in CICE5?
======================

CICE5 is very similar in code structure to the previous version CICE4
and was released in March of 2015. CICE4 was an upgraded version of 
the Community Sea Ice Model, CSIM5, which was based on CICE3. 
The major changes are:

-  The new mushy-layer thermodynamics (``ktherm = 2``) is the default :cite:`turner15`.

-  The new level melt pond scheme (``tr_pond_lvl = .true.``) is the default :cite:`hunke13`.

-  The default number of ice layers is now 8, where it used to be 4.

-  The default number of snow layers is now 3, where it used to be 1.

-  The freezing point at the sea ice-ocean interface is now salinity dependent following :cite:`assur58`.

The CICE source code is based on the Los Alamos sea ice model CICE
model version 5. The main source code is very similar in both
versions, but the drivers are significantly different. If there are
some topics that are not covered in the CICE documentation, users are
encouraged to look at the CICE documentation. It is available at Los
Alamos National Laboratory at: http://oceans11.lanl.gov/trac/CICE.
