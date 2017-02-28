.. _overview:

*******************
 What is CICE5
*******************

This User’s Guide accompanies the CESM2.0 User’s Guide, and is
intended for those who would like to run CICE coupled, on a supported
platform, and “out of the box”.  It includes a quick start guide for
downloading the CESM1 source code and input datasets, and information
on how to configure, build and run the model. The supported
configurations and scripts for building the fully coupled model are
also described in the CESM1 User’s Guide.  The CICE User’s Guide is
intended for users interested in making modifications to the ice model
scripts or namelists or running the uncoupled ice model.  Users
interested in modifying the source code should see the CICE Code
Reference/ Developer’s Guide.

CICE4 is the latest version of the Los Alamos Sea Ice Model, sometimes
referred to as the Community Ice CodE. It is the result of a community
effort to develop a portable, efficient sea ice model that can be run
coupled in a global climate model or uncoupled as a stand-alone ice
model. It has been released as the sea ice component of the Community
Earth System Model (CESM), a fully-coupled global climate model that
provides simulations of the earths past, present and future climate
states. CICE4 is supported on high- and low-resolution Greenland Pole
and tripole grids, which are identical to those used by the Parallel
Ocean Program (POP) ocean model. The high resolution version is best
suited for simulating present-day and future climate scenarios while
the low resolution option is used for paleoclimate simulations and
debugging. An uncoupled version of CICE is available separately from
Los Alamos National Laboratory: http://oceans11.lanl.gov/trac/CICE.

It provides a means of running the sea ice model independent of the
other CESM components. It reads in atmospheric and ocean forcing,
which eliminates the need for the flux coupler, and the atmosphere,
land and ocean data models. It can be run on a reduced number of
processors, or without MPI (Message Passing Interface) for researchers
without access to these computer resources.

The physics in the uncoupled ice model are identical to those in the
ice model used in the fully coupled system.  CICE is a
dynamic-thermodynamic model that includes a subgrid-scale ice
thickness distribution (; ). It uses the energy conserving
thermodynamics of , has multiple layers in each thickness category,
and accounts for the influences of brine pockets within the ice
cover. The ice dynamics utilizes the elastic-viscous-plastic (EVP)
rheology of. Sea ice ridging follows and. A slab ocean mixed layer
model is included. A Scientific Reference is available that contains
more detailed information on the model physics.

An attempt has been made throughout this document to provide the
following text convention. Variable names used in the code are
typewritten. Subroutine names are given in *italic*, and file names
are in **boldface**.

======================
 What’s new in CICE5?
======================

CICE4 is an upgraded version of the Community Sea Ice Model, CSIM5,
which was based on CICE3, and was released in June 2004. The model
physics are similar to that of CSIM5, but it was decided to move to
CICE, the LANL sea ice model for practical reasons. The major changes
are:

-  The incremental remapping transport scheme is now the default and is
   available in the modules called **ice\_transport\_driver.F90** and
   **ice\_transport\_remap.F90**. The MPDATA transport scheme, is no
   longer supported in CICE4. The upwind advection scheme is the only
   additional option and is contained in **ice\_transport\_driver.F90**.

-  The standalone ice model is now only available through Los Alamos
   National Laboratory.

-  Several physics options have been shifted around into other or new
   modules. For example, most of **ice\_albedo.F90** is now in
   **ice\_shortwave.F90**. The new module contains all of the shortwave
   radiative transfer plus the basic albedo calculations.

-  The mechanical redistribution scheme has been changed significantly
   and is available in **ice\_mechred.F90**.

-  A new drivers area has been created for modules that are specific to
   the CESM as opposed to the standalone CICE model. The new CESM
   drivers are contained in the cpl\_mct and cpl\_share subdirectories.
   The ESMF driver (cpl\_esmf) is still under development. The source
   subdirectory now contains driver independent source code for the most
   part.

-  A new bld subdirectory has been introduced which contains CESM
   specific build and configure scripts. These scripts handle the
   namelist generation, defaults, and configuration details.

The CICE source code is based on the Los Alamos sea ice model CICE
model version 4. The main source code is very similar in both
versions, but the drivers are significantly different. If there are
some topics that are not covered in the CICE documentation, users are
encouraged to look at the CICE documentation . It is available at Los
Alamos National Laboratory at: http://oceans11.lanl.gov/trac/CICE.
