.. _restart-files:

CICE Restart Files
==================

Restart files contain all of the initial condition information
necessary to restart from a previous simulation. These files are in a
standard netCDF 64-bit binary format. A restart file is not necessary
for an initial run, but is highly recommended. The initial conditions
that are internal to the ice model produce an unrealistic ice cover
that an uncoupled ice model will correct in several years. The initial
conditions from a restart file are created from an equilibrium
solution, and provide more realistic information that is necessary if
coupling to an active ocean model. The frequency at which restart
files are created is controlled by the namelist parameter ``dumpfreq``.
The names of these files are proceeded by the namelist parameter
``dump_file`` and, by default are written out yearly to the executable
directory. To change the directory where these files are located,
modify the variable $RSTDIR at the top of the setup script. The names
of the restart files follow the CESM Output Filename Requirements. The
form of the restart file names are as follows:
::

   **$CASE.cice.r.yyyy-mm-dd-sssss.nc**

For example, the file **$CASE.cice.r.0002-01-01-00000.nc** would be
written out at the end of year 1, month 12. A file containing the name
of a restart file is called a restart pointer file. This filename
information allows the model simulation to continue from the correct
point in time, and hence the correct restart file.

Changing the restart frequency is handled by the CESM driver in **env_run.xml**.
The variables are ``REST_DATE``, ``REST_N`` and ``REST_OPTION``. See the CESM
documentation here:

http://www.cesm.ucar.edu/models/cesm2/component_settings/drv_input.html

Restart Pointer Files
---------------------

A pointer file is an ascii file named **rpointer.ice** that contains the
path and filename of the latest restart file. The model uses this
information to find a restart file from which initialization data is
read. The pointer files are written to and then read from the executable
directory. For startup runs, a pointer is created by the ice setup
script Whenever a restart file is written, the existing restart pointer
file is overwritten. The namelist variable ``pointer_file`` contains the
name of the pointer file. Pointer files seldom need editing. The
contents are usually maintained by the setup script and the component
model.
