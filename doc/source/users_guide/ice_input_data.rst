.. _cice-input-data:


********************
CICE Input Data
********************

The coupled CICE model requires a minimum of two files to run:

-  **grid\_file** is a binary or netcdf file containing grid information such as the latitude, longitude, grid cell area, etc.

-  **kmt\_file** is a binary or netcdf file containing land mask information. This points to the ocean model KMT file or the depths of the ocean columns.

Depending on the grid selected in the scripts, the appropriate
**grid_file*** and **kmt_file*** files will be used in the executable
directory. These files are read directly from the system input data
directory and not copied to the executable directory. Currently, only
the POP resolutions of gx3, gx1, tx1, and tx0.1 grids are supported for the ice and
ocean models. Note that these files can now be used in netCDF format.

A third namelist variable that is required for initial or hybrid runs is the 
**ice\_ic** variable. 

- ice\_ic \= 'none' initializes the sea ice to zero everywhere

- ice\_ic \= 'default' initializes the sea ice to 100% concentration with 2m or 1m thickness in the Northern and Southern Hemispheres respectively where the SST is -1.8C or below

- ice\_ic \= "filename.nc" will read the state information from an initial file named "filename.nc"
 
For the third option, the initial file resolution must match that of the grid\_file
specified as above. Restart or branch runs are discussed later.
