.. _ice-thickness-categories:

*******************************
CICE Thickness Categories
*******************************

The number of ice thickness categories affects ice model input files in
three places:

-  $NCAT in the run script

-  The source code module **ice\_model\_size.F90**

-  The initial condition (restart) file in the input file directory

The number of ice thickness categories is set in
**$CASE/env_build.xml** using the xml variable CICE_CONFIG_OPTS. 
The default value is 5 categories. $NCAT is used to determine the CPP
variable setting (NCAT) in **ice\_model\_size.F90**. $RES is the
resolution of the grid, 100x116 (gx3) and 320x384 (gx1) for low and
medium resolution grids, respectively.

.. note:: To use one ice thickness category, the following changes will need to  be made in the namelist:

::

      , kitd          = 0
      , kstrength     = 0

With these settings, the model will use the delta scheme instead of
linear remapping and a strength parameterization based on open water
area and mean ice thickness.

The information in the initial restart file is dependent on the number
of ice thickness categories and the total number of layers in the ice
distribution. An initial condition file exists only for the default case
of 5 ice thickness categories, with four layers in each category. To
create an initial condition file for a different number of categories or
layers, these steps should be followed:

-  Set ncat to the desired number of categories in **$CASE/env_build.xml**.

-  Set the namelist variable dumpfreq = ’m’ in **$CASE/user\_nl\_cice** to print out restart files monthly.

- Set the namelist variable restart = .false. in **$CASE/user\_nl\_cice** to use the initial conditions within the ice model.

-  Run the model to equilibrium.

-  The last restart file can be used as an initial condition file.

-  Change the name of the last restart file to *iced.0001-01-01.$GRID*.

-  Copy the file into the input data directory or directly into the the
   executable directory.

-  There are a few restart files available in **$DIN_LOC_ROOT/ice/cice**.

Note that the date printed inside the binary restart file will not be
the same as 0001-01-01. For coupled runs, $BASEDATE will be the starting
o date and the date inside the file will not be used.
