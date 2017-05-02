.. _namelist:

**********************
 CICE Namelists 
**********************

CICE uses the same namelists for both the coupled and uncoupled models.
This section describes the namelist variables in the namelist ice\_nml,
which determine time management, output frequency, model physics, and
filenames The ice namelists for the coupled model are now located in
**$CASE/CaseDocs**.

A script reads the input namelist at runtime, and writes the namelist
information to the file **ice\_in** in the directory where the model
executable is located. Therefore, the namelist will be updated even if
the ice model is not recompiled. The default values of the ice setup,
grid, tracer, and physics namelists are set in **ice\_init.F90**. The
prescribed ice option along with the history namelist variables are set
in **ice\_prescribed.F90** and **ice\_history.F90** respectively. If
they are not set in the namelist in the script, they will assume the
default values listed in the following tables, which
list all available namelist parameters. The default values shown here
are for the coupled model, which is set up for a production run. Only a
few of these variables are required to be set in the namelist; these
values are noted in the paragraphs below. An example of the default
namelist is shown in Section :ref:`cice_namelist_examples`.

The main run management namelist options are shown in :ref:`setupnml`. 
While additional namelist variables are
available in the uncoupled version, they are set by the driver in
CESM. For a full list of namelist variables, you should consult the CICE
Reference Guide :cite:`cice15`. 
Variables set by the driver include: dt, runid, runtype, istep0,
days\_per\_year, restart and dumpfreq. These should be changed in the
CESM configuration files.

.. _setupnml:

.. table:: Table 1: Setup Namelist
   :widths: 20,12,12,60

   ================    ==========  ====================        ============================================================  
   Variable            Type        Default Value               Description
   ================    ==========  ====================        ============================================================
   bfbflag             logical     .false.                     Require bit-for-bit global sums.
   days\_per\_year     integer     365                         Standard number of days per year for calendar. Does interact
                                                               with Gregorian calendar setting (set by driver).
   diagfreq            integer     24                          Frequency of diagnostics written
                                                               (min, max, hemispheric sums) to standard output
                                                               24 =\ :math:`>` writes once every 24 timesteps 
                                                               1 =\ :math:`>` diagnostics written each timestep
                                                               0 =\ :math:`>` no diagnostics written
   dumpfreq            character   'x'                         Unit for frequency of dump files. Currently set by driver.
   dumpfreq_n          integer     1                           Frequency of dumpfreq dump files. Set by driver.
   hist\_avg           logical     .true.                      If true, averaged history information 
                                                               is written out at a frequency determined by histfreq. 
                                                               If false, instantaneous values are written in all streams.
   histfreq            char array  ’m’,’x’,’x’,’x’,’x’         Unit for frequency of output written to history streams 
                                                               ’D’ or ’d’ writes daily data
                                                               ’H’ or ’h’ writes hourly data
                                                               ’M’ or ’m’ writes monthly data
                                                               ’Y’ or ’y’ writes yearly data
                                                               ’1’ writes every timestep
                                                               ’x’ no history data is written  
   histfreq            integer     1,1,1,1,1                   Frequency of histfreq history data is written to each stream.
   history\_file       character   'unknown'                   History file prefix. Set by driver.
   ice\_ic             character   default                     Filename for initial and branch runs. Set by driver scripts.
                                                               ’default’ uses default initialization 
                                                               ’none’ initializes with no ice  
   latpnt              float arr   90.0, -65.0                 Latitudes for diagnostic points (print\_points)
   lonpnt              float arr   0.0, -45.0                  Longitudes for diagnostic points (print\_points)
   lcdf64              logical     .false.                     Use 64-bit offset in netcdf files
   ndtd                integer     1                           Number of dynamic timesteps per thermodynamic timestep.
   pointer\_file       character   ’rpointer.ice’              Pointer file that contains the name of the restart file.
   print\_global       logical     .true.                      Print global diagnostics.
   print\_points       logical     .true.                      Print diagnostics at latpnt and lonpnt.
   restart\_ext        logical     .false.                     Write ghost cells as a part of restarts.
   restart\_file       character   none                        Restart file prefix. Set by driver.
   use\_leap\_years    logical     .false.                     Calendar set by driver. Do not use.
   year\_init          integer     1                           Used in leap year calculation. Do not change.
   ================    ==========  ====================        ============================================================ 

Changing the timestep
---------------------

dt is the timestep in seconds for the ice model thermodynamics. The
thermodynamics component is stable but not necessarily accurate for any
value of the timestep. The value chosen for dt depends on the stability
of the transport and the grid resolution. A conservative estimate of dt
for the transport using the upwind advection scheme is:

.. math:: \Delta t < \frac{min(\Delta x, \Delta y)}{4 max(u, v)} .

Maximum values for dt for the two standard CESM POP grids, assuming
:math:`max(u,v) = 0.5\ m/s`, are shown in :ref:`timestep`.
The default timestep for CICE is 30 minutes for gx1, 
which must be equal to the coupling interval (**ICE_NCPL** and **ATM_NCPL**) 
set in the CESM configuration files **env\_run.xml**.

.. _timestep:

.. csv-table:: Table 2: Recommended timesteps
   :header: "Grid",":math:`min(\Delta x, \Delta y)`",":math:`max \Delta t`"
   :widths: 20,60,20

   gx3,28845.9 m,4.0 hr
   gx1,8558.2 m,1.2 hr

Occasionally, ice velocities are calculated that are larger than what is
assumed when the model timestep is chosen. This causes a CFL violation
in the transport scheme. A namelist option was added (ndtd) to
subcycle the dynamics to get through these instabilities that arise
during long integrations. The default value for this variable is one,
and is typically increased to two when the ice model reaches an
instability. The value in the namelist should be returned to one by the
user when the model integrates past that point.

Writing Output
--------------

The namelist variables that control the frequency of the model
diagnostics, netCDF history, and restart files are shown in
:ref:`setupnml`. By default, diagnostics are written out once
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
:ref:`setupnml` will not work unless some modifications
are made to the ice setup script and a file is created with this name
and contains the name of a valid restart file; this variable must be set
in the namelist. More information on restart pointer files can be found
in Section .

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
CESM driver. See X and Y for
an explanation of how the rest of the filename is created.

Model Physics
-------------

Some of the most commonly used namelist variables for the ice model physics 
are listed in the following tables. More information can be found in the 
CICE reference guide at:

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

advection determines the horizontal transport scheme used. The default
scheme is the incremental remapping method (). This method is less
diffusive and is computationally efficient for large numbers of
categories or tracers. The upwind scheme is also available. The upwind
scheme is only first order accurate.

A new thermodynamics option (ktherm = 2) is now the default. This is the
so-called mushy-layer thermodyanmics of Turner and Hunke 2015. The basic
idea of this is that prognostic salinity is now used in the vertical
thermodynamic calculation where this used to be a constant profile. The
older option of Bitz and Lipscomb 1999 (ktherm = 1) is still available.

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

.. _dynamics:

.. csv-table:: Table 3: CICE Dynamics Settings
   :header: "Variable Name","Type","Default","Description"
   :widths: 20,12,12,60

   "kdyn","Integer","1","Determines ice dynamics, 0 = No ice dynamics, 1 = Elastic viscous plastic dynamics"
   "revised_evp","Logical",".false.","Revised EVP formulation"
   "ndte", "Integer", "1","Number of sub-cycles in EVP dynamics."
   "advection","Character","remap","Determines horizontal advection scheme. ’remap’ = incremental remapping, ’upwind’ = first order advection"
   "kstrength","Integer","1","Determines pressure formulation, 0 = parameterization, 1 = parameterization"
   "krdg_partic","Integer","1","Ridging participation function, 0 = Thorndike, 1 = Expontential"
   "krdg_redist","Integer","1","Ridging distribution function, 0 = Hibler , 1 = Expontential"
   "mu_rdg","Real","4.0","e-folding scale of ridged ice"
   "Cf","Real","17.0","Ratio of ridging work to PE change"

.. _thermo:

.. csv-table:: Table 4: CICE Thermodynamic Settings
   :header: "Variable Name","Type","Default","Description"
   :widths: 20,12,12,60

   "kitd","Integer","1","Determines ITD conversion, 0 = delta scheme, 1=linear remapping"
   "ktherm","Integer","1","Determines ice thermodynamics, 1 = BL99, 2 = mushy layer"
   "conduct","Character","MU71","Determines conductivity formulation used with ktherm = 1, MU71, bubbly"

.. _shortwave:

.. csv-table:: Table 5: CICE Radiation Settings
   :header: "Variable Name","Type","CESM-CAM4 gx3","CESM-CAM4 gx1","CESM-CAM5 gx1","Description"
   :widths: 20,12,12,12,12,60

   "shortwave","Character","dEdd","dEdd","dEdd","Shortwave Radiative Transfer Scheme,  ’default’ = CCSM3 Shortwave, ’dEdd’ = delta-Eddington Shortwave"
   "albicev","Real",0.68,0.75,0.75,"Visible ice albedo (CCSM3)"
   "albicei","Real",0.30,0.45,0.45,"Near-infrared ice albedo (CCSM3)"
   "albsnowv","Real",0.91,0.98,0.98,"Visible snow albedo (CCSM3)"
   "albsnowi","Real",0.63,0.73,0.73,"Near-infrared snow albedo (CCSM3)"
   "R\_ice","Real",0.0,0.0,0.0,"Base ice tuning parameter (dEdd)"
   "R\_pnd","Real",0.0,0.0,0.0,"Base pond tuning parameter (dEdd)"
   "R\_snw","Real",-2.0,1.5,1.75,"Base snow grain radius tuning parameter (dEdd)"
   "dT\_mlt\_in","Real",2.0,1.5,1.0,"Snow melt onset temperature parameter (dEdd)"
   "rsnw\_mlt\_in","Real",2000.,1500.,1000.,"Snow melt maximum radius (dEdd)"

Tracer Namelist
---------------

The namelist parameters listed in :ref:`tracers` are
for adding tracers. The tracers should be added through the CESM
driver scripts via the CICE\_CONFIG\_OPTS variable.

.. _tracers:

.. csv-table:: Table 6: Tracer Namelist
   :header: "Variable","Type","Default Value","Description"
   :widths: 20,12,12,60

   "tr\_iage",Logical,.true.,"Ice age passive tracer"
   "tr\_FY",Logical,.true.,"First-year ice area passive tracer"
   "tr\_lvl",Logical,.false.,"Level ice area passive tracer"
   "tr\_pond\_cesm",Logical,.false.,"The older CESM melt pond option."
   "tr\_pond\_lvl",Logical,.true.,"The Hunke et al. level ice pond formulation"
   "tr\_pond\_topo",Logical,.true.,"The Felthem et al. topographic pond formulation"
   "tr\_aero",Logical,.true.,"Aerosol physics and tracer"

Prescribed Ice Namelist
-----------------------

The namelist parameters listed in :ref:`prescribed` are for the prescribed ice
option as used in AMIP and F compset (standalone CAM) runs [prescribed].

.. _prescribed:

.. csv-table:: Table 7: Prescribed Ice Namelist
   :header: "Variable","Type","Default Value","Description"
   :widths: 20,12,12,60

   "prescribed\_ice",Logical,.false.,"Flag to turn on prescribed ice"
   "prescribed\_ice\_fill",Logical,.false.,"Flag to turn fill option"
   "stream\_year\_first",Integer,1,"First year of prescribed ice data"
   "stream\_year\_last",Integer,1,"Last year of prescribed ice data"
   "model\_year\_align",Integer,1,"Year in model run that aligns with stream\_year\_first"
   "stream\_domfilename",Character,none,"Prescribed ice stream data file"
   "stream\_fldfilename",Character,none,"Prescribed ice stream data file"
   "stream\_fldvarname",Character,ice\_cov,"Ice fraction field name"

Grid Namelist
-------------

The namelist parameters listed in :ref:`grid` are for
grid and mask information. During execution, the ice model reads grid
and land mask information from the files grid\_file and kmt\_file that
should be located in the executable directory. There are commands in the
scripts that copy these files from the input data directory, rename them
from **global\_$ICE\_GRID.grid** and **global\_$ICE\_GRID.kmt** to the
default filenames shown in Table .

.. _grid:

.. csv-table:: Table 8: Grid Settings Namelist
   :header: "Variable","Type","Default Value","Description"
   :widths: 20,12,12,60

   "grid\_type",Character,displaced\_pole,"Determines grid type."
   " "," "," ","displaced\_pole"
   " "," "," ","tripole"
   " "," "," ","rectangular"
   "grid\_format",Character,binary,"Grid file format (binary or netCDF)"
   "grid\_file",Character,data.domain.grid,"Input filename containing grid information."
   "kmt\_file",Character,data.domain.kmt,"Input filename containing land mask information."
   "kcatbound",Integer,0,"How category boundaries are set (0 or 1)"

For coupled runs, supported grids include the ’displaced\_pole’ grids
(gx3 and gx1) and the ’tripole’ grids.

Domain Namelist
---------------

The namelist parameters listed in :ref:`domain` are
for computational domain decomposition information. These are generally
set in the build configure scripts through the variables CICE\_DECOMPTYPE and CICE\_DECOMSETTING
based on the number of processors.
See the CESM scripts documentation.

.. _domain:

.. csv-table:: Table 9: Domain Settings Namelist
   :header: "Variable","Type","Default Value","Description"
   :widths: 20,12,12,60

   "processor\_shape",Character,square-pop,"Approximate block shapes"
   "","","","slenderX1"
   "","","","slenderX2"
   "","","","square-ice"
   "","","","square-pop"
   "","","","blocks"
   "distribution\_type",Character,cartesian,"How domain is split into blocks and distributed onto processors"
   "","","","cartesian"
   "","","","rake"
   "","","","roundrobin"
   "","","","sectcart"
   "","","","sectrobin"
   "","","","spacecurve"
   "distribution\_wght",Character,erfc,"How blocks are weighted when using space-filling curves"
   "","","","block"
   "","","","latitude"
   "","","","erfc"
   "","","","file"
   "distribution\_wght\_file",Character,none,"File containing space-filling curve weights when using file weighting"
   "ew\_boundary\_type",Character,cyclic,"Boundary conditions in E-W direction"
   "ns\_boundary\_type",Character,open,"Boundary conditions in N-S direction"

PIO Namelist
------------

PIO settings are now handled via the CESM driver.
