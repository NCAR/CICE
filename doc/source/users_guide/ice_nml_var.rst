.. _cice_namelists:

**********************
 CICE Namelists 
**********************

CICE uses the same namelists for both the coupled and uncoupled models.
This section describes the namelist variables in the namelist ice\_nml,
which determine time management, output frequency, model physics, and
filenames The ice namelists for the coupled model are now located in
**$CASE/Buildconf**.

A script reads the input namelist at runtime, and writes the namelist
information to the file **ice\_in** in the directory where the model
executable is located. Therefore, the namelist will be updated even if
the ice model is not recompiled. The default values of the ice setup,
grid, tracer, and physics namelists are set in **ice\_init.F90**. The
prescribed ice option along with the history namelist variables are set
in **ice\_prescribed.F90** and **ice\_history.F90** respectively. If
they are not set in the namelist in the script, they will assume the
default values listed in Tables
[table:setup:sub:`n`\ ml]-[table:ice\ :sub:`p`\ io\ :sub:`n`\ ml], which
list all available namelist parameters. The default values shown here
are for the coupled model, which is set up for a production run. Only a
few of these variables are required to be set in the namelist; these
values are noted in the paragraphs below. An example of the default
namelist is shown in Section [example1:sub:`n`\ ml].


===============    ========== ====================        ============================================================	   
Varible            Type       Default Value   		  Description					   
===============    ========== ====================        ============================================================	   
ice\_ic            character  default        		  Filename for initial and branch runs		   
						                                                       
                                                          ’default’ uses default initialization		   
						                                                       
                                      			  ’none’ initializes with no ice			   
 xndt\_dyn         integer    1               		  Times to loop through (sub-cycle) ice dynamics	   
 diagfreq          integer    24              		  Frequency of diagnostics written 			   
						                                                       
                                                          (min, max, hemispheric sums) to standard output	   
  						                                                       
                                      			  24 =\ :math:`>` writes once every 24 timesteps	   
						                                                       
                                      			  1 =\ :math:`>` diagnostics written each timestep
						                                                       
                                      			  0 =\ :math:`>` no diagnostics written

histfreq           char array ’m’,’x’,’x’,’x’,’x’             Frequency of output written to history streams 

                                                          ’D’ or ’d’ writes daily data

							  ’W’ or ’w’ writes weekly data	
					   				
							  ’M’ or ’m’ writes monthly data	
						   				
							  ’Y’ or ’y’ writes yearly data	
						   				
							  ’1’ writes every timestep	
						   				
							  ’x’ no history data is written  

histfreq           integer    1,1,1,1,1                   Frequency history data is written  to each stream

hist\_avg          logical    .true.                      If true, averaged history information

                                                          is written out at a frequency determined by histfreq. 

						          If false, instantaneous values are written.


pointer\_file      character  ’rpointer.ice’               Pointer file that contains the name of the restart file.

lcdf64             logical    .false.                     Use 64-bit offset in netcdf files
===============    ========== ====================        ============================================================	   

The main run management namelist options are shown in Table
[table:setup:sub:`n`\ ml]. While additional namelist variables are
available in the uncoupled version, they are set by the driver in
CESM. Variables set by the driver include: dt, runid, runtype, istep0,
days\_per\_year, restart and dumpfreq. These should be changed in the
CESM configuration files:

CESM scripts (http://www.cesm.ucar.edu/models/cesm1.0/cesm\_doc/book1.html).

Changing the timestep
---------------------

dt is the timestep in seconds for the ice model thermodynamics. The
thermodynamics component is stable but not necessarily accurate for any
value of the timestep. The value chosen for dt depends on the stability
of the transport and the grid resolution. A conservative estimate of dt
for the transport using the upwind advection scheme is:

.. math:: \Delta t < \frac{min(\Delta x, \Delta y)}{4 max(u, v)} .

Maximum values for dt for the two standard CESM POP grids, assuming
:math:`max(u,v) = 0.5 m/s`, are shown in Table [table:max:sub:`d`\ t].
The default timestep for CICE is 30 minutes, which must be equal to the
coupling interval set in the CESM configuration files.

=====================  =========================================  ====================== 
Grid                   :math:`min(\Delta x, \Delta y)`            :math:`max \Delta t`
=====================  =========================================  ====================== 
gx3v5                  28845.9 m                                  4.0 hr
gx1v3                  8558.2 m                                   1.2 hr
=====================  =========================================  ====================== 

Occasionally, ice velocities are calculated that are larger than what is
assumed when the model timestep is chosen. This causes a CFL violation
in the transport scheme. A namelist option was added (xndt\_dyn) to
subcycle the dynamics to get through these instabilities that arise
during long integrations. The default value for this variable is one,
and is typically increased to two when the ice model reaches an
instability. The value in the namelist should be returned to one by the
user when the model integrates past that point.

Writing Output
--------------

The namelist variables that control the frequency of the model
diagnostics, netCDF history, and restart files are shown in Table
[table:setup:sub:`n`\ ml]. By default, diagnostics are written out once
every 48 timesteps to the ascii file **ice.log.$LID** (see section
[stdout]). $LID is a time stamp that is set in the main script.

The namelist variable histfreq controls the output frequency of the
netCDF history files; writing monthly averages is the default. The
content of the history files is described in section [history]. The
value of hist\_avg determines if instantaneous or averaged variables are
written at the frequency set by histfreq. If histfreq is set to ’1’ for
instantaneous output, hist\_avg is set to .false. within the source code
to avoid conflicts. The latest version of CICE allows for multiple
history streams, currently set to a maximum of 5. The namelist
variables, histfreq and histfreq\_n are now arrays which allow for
different frequency history file sets. More detail on this is available
in [history].

The namelist variable pointer\_file is set to the name of the pointer
file containing the restart file name that will be read when model
execution begins. The pointer file resides in the scripts directory and
is created initially by the ice setup script but is overwritten every
time a new restart file is created. It will contain the name of the
latest restart file. The default filename *ice.restart\_file* shown in
Table [table:setup:sub:`n`\ ml] will not work unless some modifications
are made to the ice setup script and a file is created with this name
and contains the name of a valid restart file; this variable must be set
in the namelist. More information on restart pointer files can be found
in section [pointer:sub:`f`\ iles].

The variables dumpfreq and dumpfreq\_n control the output frequency of
the netCDF restart files; writing one restart file per year is the
default and is set by the CESM driver. The default format for restart
files is now netCDF, but this can be changed to binary through the
namelist variable, restart\_format.

If print\_points is .true., diagnostic data is printed out for two grid
points, one near the north pole and one near the Weddell Sea. The points
are set via namelist variables latpnt and lonpnt. This option can be
helpful for debugging.

incond\_dir, restart\_dir and history\_dir are the directories where the
initial condition file, the restart files and the history files will be
written, respectively. These values are set at the top of the setup
script and have been modified from the default values to meet the
requirements of the CESM filenaming convention. This allows each type of
output file to be written to a separate directory. If the default values
are used, all of the output files will be written to the executable
directory.

incond\_file, dump\_file and history\_file are the root filenames for
the initial condition file, the restart files and the history files,
respectively. These strings have been determined by the requirements of
the CESM filenaming convention, so the default values are set by the
CESM driver. See [restart:sub:`f`\ iles] and [history:sub:`f`\ iles] for
an explanation of how the rest of the filename is created.

Model Physics
-------------

The namelist variables for the ice model physics are listed in Table
[ice:sub:`n`\ ml]. restart is almost always true since most run types
begin by reading in a binary restart file. See section [runtypes] for a
description of the run types and about using restart files and
internally generated model data as initial conditions. kcolumn is a flag
that will run the model as a single column if is set to 1. This option
has not been thoroughly tested and is not supported.

The calculation of the ice velocities is subcycled ndte times per
timestep so that the elastic waves are damped before the next timestep.
The subcycling timestep is calculated as dte = dt/ndte and must be
sufficiently smaller than the damping timescale T, which needs to be
sufficiently shorter than dt.

.. math:: dte < T < dt

This relationship is discussed in ; also see , section 4.4. The best
ratio for [dte : T : dt] is [1 : 40 : 120]. Typical combinations of dt
and ndte are (3600., 120), (7200., 240) (10800., 120). The default ndte
is 120 as set in **ice\_init.F90**.

kitd determines the scheme used to redistribute sea ice within the ice
thickness distribution (ITD) as the ice grows and melts. The linear
remapping scheme is the default and approximates the thickness
distribution in each category as a linear function (). The delta
function method represents *g(h)* in each category as a delta function
(). This method can leave some categories mostly empty at any given time
and cause jumps in the properties of *g(h)*.

kdyn determines the ice dynamics used in the model. The default is the
elastic-viscous-plastic (EVP) dynamics . If kdyn is set to o 0, the ice
dynamics is inactive. In this case, ice velocities are not computed and
ice is not transported. Since the initial ice velocities are read in
from the restart file, the maximum and minimum velocities written to the
log file will be non-zero in this case, but they are not used in any
calculations.

The value of kstrength determines which formulation is used to calculate
the strength of the pack ice. The calculation depends on mean ice
thickness and open water fraction. The calculation of is based on
energetics and should not be used if the ice that participates in
ridging is not well resolved.

evp\_damping is used to control the damping of elastic waves in the ice
dynamics. It is typically set to .true. for high-resolution simulations
where the elastic waves are not sufficiently damped out in a small
timestep without a significant amount of subcycling. This procedure
works by reducing the effective ice strength that’s used by the dynamics
and is not a supported option.

advection determines the horizontal transport scheme used. The default
scheme is the incremental remapping method (). This method is less
diffusive and is computationally efficient for large numbers of
categories or tracers. The upwind scheme is also available. The upwind
scheme is only first order accurate.

The base values of the snow and ice albedos for the CCSM3 shortwave
option are set in the namelist. The ice albedos are those for ice
thicker than ahmax, which is currently set at 0.5 m. This thickness is a
parameter that can be changed in **ice\_shortwave.F90**. The snow
albedos are for cold snow.

For the new delta-Eddington shortwave radiative transfer scheme , the
base albedos are computed based on the inherent optical properties of
snow, sea ice, and melt ponds. These albedos are tunable through
adjustments to the snow grain radius, R\_snw, temperature to transition
to melting snow, and maximum snow grain radius.

.. csv-table:: a title
   :header: "name", "firstname", "age"
   :widths: 20, 12, 12, 12, 12, 60

   "Variable Name", "Type", "CESM-CAM4 gx3 dipole-grid default", "CESM-CAM4 gx1 dipole-grid default", "CESM-CAM5 gx1 dipole-grid default", "Description"
   "ndte", "Integer", "1", "1", "1", "Number of sub-cycles in EVP dynamics."
   "kcolumn","Integer","0","0","0","Column model flag. 0 = off, 1 = column model (not tested or supported)"
   "kitd","Integer","1", "1","1", "Determines ITD conversion, 0 = delta scheme, 1=linear remapping"
   "kdyn","Integer","1","1","1","Determines ice dynamics, 0 = No ice dynamics, 1 = Elastic viscous plastic dynamics"
   "kstrength", "Integer","1", "1","1","Determines pressure formulation, 0 = parameterization, 1 = parameterization"
   "evp\_damping","Logical",".false.",".false.",".false.","If true, use damping procedure in evp dynamics (not supported)."
   "advection","Character","remap","remap","remap","Determines horizontal  advection scheme. ’remap’ = incremental remapping, ’upwind’ = first order advection"
   "shortwave","Character","dEdd","dEdd","dEdd","Shortwave Radiative Transfer Scheme,  ’default’ = CCSM3 Shortwave, ’dEdd’ = delta-Eddington Shortwave"

| albicev & Double & 0.68 & 0.75 & 0.75 & Visible ice albedo (CCSM3)

| albicei & Double & 0.30 & 0.45 & 0.45 & Near-infrared ice albedo
  (CCSM3)

| albsnowv & Double & 0.91 & 0.98 & 0.98 & Visible snow albedo (CCSM3)

| albsnowi & Double & 0.63 & 0.73 & 0.73 & Near-infrared snow albedo
  (CCSM3)

| R\_ice & Double & 0.0 & 0.0 & 0.0 & Base ice tuning parameter (dEdd)

| R\_pnd & Double & 0.0 & 0.0 & 0.0 & Base pond tuning parameter (dEdd)

| R\_snw & Double & -2.0 & 1.5 & 1.75 & Base snow grain radius tuning
  parameter (dEdd)

| dT\_mlt\_in & Double & 2.0 & 1.5 & 1.0 & Snow melt onset temperature
  parameter (dEdd)

| rsnw\_mlt\_in & Double & 2000. & 1500. & 1000. & Snow melt maximum
  radius (dEdd)

Tracer Namelist
---------------

The namelist parameters listed in Table [table:tracer:sub:`n`\ ml] are
for adding tracers. See section on tracers.

| p2.5cmp2.5cmp3cmp6.0cm Varible & Type & Default Value & Description

| tr\_iage & Logical & .true. & Ice age passive tracer

| tr\_FY & Logical & .true. & First-year ice area passive tracer

| tr\_lvl & Logical & .false. & Level ice area passive tracer

| tr\_pond & Logical & .true. & Melt pond physics and tracer

| tr\_aero & Logical & .true. & Aerosol physics and tracer

Prescribed Ice Namelist
-----------------------

The namelist parameters listed in Table
[table:ice:sub:`p`\ rescribed\ :sub:`n`\ ml] are for the prescribed ice
option as used in AMIP and F compset (standalone CAM) runs [prescribed].

| p4.0cmp2.0cmp3cmp6.0cm Varible & Type & Default Value & Description

| prescribed\_ice & Logical & .false. & Flag to turn on prescribed ice

| prescribed\_ice\_fill & Logical & .false. & Flag to turn fill option

| stream\_year\_first & Integer & 1 & First year of prescribed ice data

| stream\_year\_last & Integer & 1 & Last year of prescribed ice data

| model\_year\_align & Integer & 1 & Year in model run that aligns with
  stream\_year\_first

| stream\_domfilename & Character & & Prescribed ice stream data file

| stream\_fldfilename & Character & & Prescribed ice stream data file

| stream\_fldvarname & Character & ice\_cov & Ice fraction field name

Grid Namelist
-------------

The namelist parameters listed in Table [table:grid:sub:`n`\ ml] are for
grid and mask information. During execution, the ice model reads grid
and land mask information from the files grid\_file and kmt\_file that
should be located in the executable directory. There are commands in the
scripts that copy these files from the input data directory, rename them
from **global\_$ICE\_GRID.grid** and **global\_$ICE\_GRID.kmt** to the
default filenames shown in Table [table:grid:sub:`n`\ ml].

| p2.5cmp2.5cmp3cmp6.0cm Varible & Type & Default Value & Description

| grid\_type & Character & ’displaced\_pole’ & Determines grid type.
| & & & ’displaced\_pole’
| & & & ’tripole’
| & & & ’rectangular’

| grid\_format & Character & binary & Grid file format (binary or
  netCDF)

| grid\_file & Character & ’data.domain.grid’ & Input filename
  containing grid information.

| kmt\_file & Character & ’data.domain.kmt’ & Input filename containing
  land mask information.

| kcatbound & Integer & 0 & How category boundaries are set (0 or 1)

For coupled runs, supported grids include the ’displaced\_pole’ grids
(gx3v7 and gx1v6) and the ’tripole’ grids.

Domain Namelist
---------------

The namelist parameters listed in Table [table:domain:sub:`n`\ ml] are
for computational domain decomposition information. These are generally
set in the build configure scripts based on the number of processors.
See the CESM scripts documentation.

| p4.0cmp2cmp2cmp6.0cm Varible & Type & Default Value & Description

| processor\_shape & Character & ’square-pop’ & Approximate block shapes

| ew\_boundary\_type & Character & ’cyclic’ & Boundary conditions in E-W
  direction

| ns\_boundary\_type & Character & ’open’ & Boundary conditions in N-S
  direction

| distribution\_type & Character & ’cartesian’ & How blocks are split
  onto processors
| & & & ’cartesian’
| & & & ’spacecurve’
| & & & ’rake’

| distribution\_wght & Character & ’erfc’ & How blocks are weighted when
  using space-filling curves (erfc or file)

| distribution\_wght\_file & Character & ” & File containing
  space-filling curve weights when not using erfc weighting

PIO Namelist
------------

| The namelist parameters listed in Table
  [table:ice:sub:`p`\ io\ :sub:`n`\ ml] are for controlling parallel
  input/output. Only a brief overview will be given here, but more on
  parallel input/output can be found at:

| http://web.ncar.teragrid.org/~dennis/pio\_doc/html.

| p2.5cmp2.5cmp3cmp6.0cm Varible & Type & Default Value & Description

| ice\_num\_iotasks & Integer & -1 & Number of I/O tasks.
| & & & default -1 selects all processors.

| ice\_pio\_stride & Integer & -1 & Stride between I/O tasks.
| & & & -1 selects defaulto stride.

| ice\_pio\_type\_name & Character & netcdf & Underlying library used.
| & & & default is netcdf.
