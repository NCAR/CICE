.. _ice_setup:

********************************
Configuring and Building CICE
********************************

Overview
========

The setup scripts for the coupled model are located in **cesm2/scripts**. 

The directory structure of CICE5 within CESM is shown below.

::

				       cesm2         (main directory)
                                         |
				         |
		      components --------+--------- scripts
			 |                        |
			 |               * * * * *|* * * * * 
		        cice             *build scripts for*
		         |               *  coupled model  *
	                 |               * * * * * * * * * *
			 |
                 bld ----+---- cime_config ---- docs ------ src
                         |                       |           |
		       (Build scripts          (CICE         |
		        and CIME config)    documentation)   |
           				  	             |
	           				             |                  
            	           		  drivers --- mpi ---+--- io_pio --- serial --- source
			                     | 
			                     |
			                     |
                            cesm --- cice ---+--- hadley


The CIME scripts generate a set of “resolved scripts” for a specific configuration
determined by the user. The configuration includes components,
resolution, run type, and machine. The run and setup scripts that were
in the **/scripts** directory for previous versions are now generated
automatically. See the CESM2 User’s Guide for information on how to
use the new scripts.

The file that contains the ice model namelist is now located in
**$CASE/CaseDocs**. The script containing the environment variables
used for building the executable file for the ice model is in
**$CASE/Buildconf**. The contents of the ice model namelist are
described in section [namelist].

Building the CICE library
==========================

The Build Environment
---------------------

The **cime_config/build_cpp** script sets all compile time parameters, such
as the horizontal grid, the sea ice mode (prognostic or prescribed),
tracers, etc. Additional options can be set using the **configure**
utility such as the decomposition, and the number of tasks, but these
are typically set via CESM enviroment variables. 

::

    #--------------------------------------------------------------------
    # Invoke cice configure
    #--------------------------------------------------------------------

    set hgrid = "-hgrid $ICE_GRID"
    if ($ICE_GRID =~ *T*) set hgrid = "-hgrid ${ICE_NX}x${ICE_NY}"

    set mode = "-cice_mode $CICE_MODE"

    cd $CASEBUILD/ciceconf || exit -1
    $CODEROOT/ice/cice/bld/configure $hgrid $mode -nodecomp $CICE_CONFIG_OPTS || exit -1

This example sets the horizontal grid and the mode (prognostic or
prescribed). The **build namelist** utility sets up the namelist which
controls the run time options for the CICE model. This utility sets
namelist flags based on compile time settings from **configure** and
some standard defaults based on horizontal grids and other options. 
Again, the typical usage of the **build namelist** tool is through the
CESM scripts, but can be called via the command line interface.

CICE Preprocessor Flags
---------------------------

Preprocessor flags are activated in the form -Doption in the
**buildcpp** script. Only advanced users should change these
options. See the CESM User’s Guide or the CICE reference guide for more
information on these. The flags specific to the ice model are:

::

    CPPDEFS :=  $(CPPDEFS) -DCESMCOUPLED -Dcoupled -Dncdf -DNCAT=5 -DNXGLOB=$()
    -DNYGLOB=$() -DNTR_AERO=3 -DBLCKX=$() -DBLCKY=$() -DMXBLCKS=$()

The options -DCESMCOUPLED and -Dcoupled are set to activate the coupling
interface. This will include the source code in **ice\_comp\_mct.F90**,
for example. In coupled runs, the CESM coupler multiplies the fluxes by
the ice area, so they are divided by the ice area in CICE to get the
correct fluxes. Note that the **ice\_forcing.F90** module is not used in
coupled runs.

The options -DBLCKX=$(CICE\_BLCKX) and -DBLCKY=$(CICE\_BLCKY) set the
block sizes used in each grid direction. These values are set
automatically in the scripts for the coupled model. Note that BLCKX and
BLCKY must divide evenly into the grid, and are used only for MPI grid
decomposition. If BLCX or BLCKY do not divide evenly into the grid,
which determines the number of blocks in each direction, the model setup
will exit from the setup script and print an error message to the
**ice.bldlog\*** (build log) file.

The flag -DMXBLCKS is essentially the threading option. This controls
the number of “blocks” per processor. This can describe the number of
OpenMP threads on an MPI task, or can simply be that a single MPI task
handles a number of blocks.

The flat -DNTR\_AERO=n flag turns on the aerosol deposition physics in
the sea ice where n is the number of tracer species and 0 turns off the
tracers. More details on this are in the section on tracers.

The flag -D\_MPI sets up the message passing interface. This must be set
for runs using a parallel environment. To get a better idea of what code
is included or excluded at compile time, grep for ifdef and ifndef in
the source code or look at the **.f90** files in the /**obj** directory.
