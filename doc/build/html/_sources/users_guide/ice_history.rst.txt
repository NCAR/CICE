.. _history-files:

*******************************
 CICE History Files
*******************************

History files contain gridded data values written at specified times
during a model run. By default, the history files will be written to
the directory history\_dir defined in the namelist. The netCDF file
names are prepended by the character string given by history\_file in
the ice\_nml namelist. This character string has been set according to
CESM Output Filename Requirements. If history\_file is not set in the
namelist, the default character string ’iceh’ is used. The user can
specify the frequency at which the data are written. Options are also
available to record averaged or instantaneous data. The form of the
history file names are as follows:

- Yearly averaged: **$CASE.cice.h?.yyyy.nc**
- Monthly averaged: **$CASE.cice.h?.yyyy-mm.nc**
- Daily averaged: **$CASE.cice.h?.yyyy-mm-dd.nc**
- Instantaneous (histfreq = ’y’, ’m’, or ’d’):  **$CASE.cice.h?.yyyy-mm-dd-sssss.nc**
- Instantaneous (written every dt, histfreq = 1): **$CASE.cice.h?.yyyy-mm-dd-sssss.nc**

$CASE is set in the main setup script. Note that the ? denotes the
multiple stream option where the first stream is just .h. and subsequent
streams are h1, h2, etc. All history files are written in the executable
directory. Changes to the frequency and averaging will affect all output
fields. The best description of the history data comes from the file
itself using the netCDF command ncdump -h filename.nc. Variables
containing grid information are written to every file and are listed in
Table [table:time:sub:`g`\ rid\ :sub:`h`\ ist]. In addition to the
history files, a netCDF file containing a snapshot of the initial ice
state can be created at the start of each run. The file name is
**$CASE.cice.i.yyyy-mm-dd-sssss.nc** and is written in the executable
directory.

Caveats Regarding Averaged Fields
----------------------------------------

In computing the monthly averages for output to the history files, most
arrays are zeroed out before being filled with data. These zeros are
included in the monthly averages where there is no ice. For some fileds,
this is not a problem, for example, ice thickness and ice area. For
other fields, this will result in values that are not representative of
the field when ice is present. Some of the fields affected are:

-  Flat, Fsens - latent and sensible heat fluxes

-  evap - evaporative water flux

-  Fhnet - ice/ocn net heat flux

-  Fswabs - snow/ice/ocn absorbed solar flux

-  strairx, strairy - zonal and meridional atm/ice stress

-  strcorx, strcory - zonal and meridional coriolis stress

For some fields, a non-zero value is set where there is no ice. For
example, Tsfc has the freezing point averaged in, and Flwout has
:math:`\sigma T_f^4` averaged in. At lower latitudes, these values can
be erroneous.

To aid in the interpretation of the fields, a field called
*ice\_present* is written to the history file. It contains information
on the fraction of the time-averaging interval when any ice was present
in the grid cell during the time-averaging interval in the history file.
This will give an idea of how many zeros were included in the average.

The second caveat results from the coupler multiplying fluxes it
receives from the ice model by the ice area. Before sending fluxes to
the coupler, they are divided by the ice area in the ice model. These
are the fluxes that are written to the history files, they are not what
affects the ice, ocean or atmosphere, nor are they useful for
calculating budgets. The division by the ice area also creates large
values of the fluxes at the ice edge. The affected fields are:

-  Flat, Fsens - latent and sensible heat fluxes

-  Flwout - outgoing longwave

-  evap - evaporative water flux

-  Fresh - ice/ocn fresh water flux

-  Fhnet - ice/ocn net heat flux

-  Fswabs - snow/ice/ocn absorbed solar flux

When applicable, two of the above fields will be written to the history
file: the value of the field that is sent to the coupler (divided by ice
area) and a value of the flux that has been multiplied by ice area (what
affects the ice). Fluxes multiplied by ice area will have the suffix
\_aice appended to the variable names in the history files. Fluxes sent
to the coupler will have “sent to coupler” appended to the long\_name.
Fields of rainfall and snowfall multiplied by ice area are written to
the history file, since the values are valid everywhere and represent
the precipitation rate on the ice cover.

Changing Frequency and Averaging
----------------------------------------

The frequency at which data are written to a history file as well as the
interval over which the time average is to be performed is controlled by
the namelist variable histfreq. Data averaging is invoked by the
namelist variable hist\_avg. The averages are constructed by
accumulating the running sums of all variables in memory at each
timestep. The options for both of these variables are described in Table
[table:setup:sub:`n`\ ml]. If hist\_avg is true, and histfreq is set to
monthly, for example, monthly averaged data is written out on the last
day of the month.

Changing Content
----------------------------------------

The second namelist in the setup script controls what variables are
written to the history file. To remove a field from this list, add the
name of the character variable associated with that field to the
**$CASE/user\_nl\_cice** file and assign it a value
of ’xxxxx’. For example, to remove ice thickness and snow cover from the
history file, add

::

    &icefields_nml
        f_hi   =  'xxxxx'
      , f_hs   =  'xxxxx'
    /

to the namelist.

+----------------+---------------------------------------------------------------+-------------------+
| Field          | Description                                                   | Units             |
+================+===============================================================+===================+
|                | model time                                                    | days              |
+----------------+---------------------------------------------------------------+-------------------+
| time\_bounds   | boundaries for time-averaging interval                        | days              |
+----------------+---------------------------------------------------------------+-------------------+
| TLON           | T grid center longitude                                       | degrees           |
+----------------+---------------------------------------------------------------+-------------------+
| TLAT           | T grid center latitude                                        | degrees           |
+----------------+---------------------------------------------------------------+-------------------+
| ULON           | U grid center longitude                                       | degrees           |
+----------------+---------------------------------------------------------------+-------------------+
| ULAT           | U grid center latitude                                        | degrees           |
+----------------+---------------------------------------------------------------+-------------------+
| tmask          | ocean grid mask (0=land, 1=ocean)                             |                   |
+----------------+---------------------------------------------------------------+-------------------+
| tarea          | T grid cell area                                              | m\ :math:`^{2}`   |
+----------------+---------------------------------------------------------------+-------------------+
| uarea          | U grid cell area                                              | m\ :math:`^{2}`   |
+----------------+---------------------------------------------------------------+-------------------+
| dxt            | T cell width through middle                                   | m                 |
+----------------+---------------------------------------------------------------+-------------------+
| dyt            | T cell height through middle                                  | m                 |
+----------------+---------------------------------------------------------------+-------------------+
| dxu            | U cell width through middle                                   | m                 |
+----------------+---------------------------------------------------------------+-------------------+
| dyu            | U cell height through middle                                  | m                 |
+----------------+---------------------------------------------------------------+-------------------+
| HTN            | T cell width North side                                       | m                 |
+----------------+---------------------------------------------------------------+-------------------+
| HTE            | T cell width East side                                        | m                 |
+----------------+---------------------------------------------------------------+-------------------+
| ANGLET         | angle grid makes with latitude line on T grid                 | radians           |
+----------------+---------------------------------------------------------------+-------------------+
| ANGLE          | angle grid makes with latitude line on U grid                 | radians           |
+----------------+---------------------------------------------------------------+-------------------+
| ice\_present   | fraction of time-averaging interval that any ice is present   |                   |
+----------------+---------------------------------------------------------------+-------------------+

Table: Time and Grid Information Written to History File

[table:history:sub:`f`\ ields]

| lll Logical Variable & Description & Units
| ``f_hi`` & grid box mean ice thickness & m
| ``f_hs`` & grid box mean snow thickness & m
| ``f_fs`` & grid box mean snow fraction & %
| ``f_Tsfc`` & snow/ice surface temperature & C
| ``f_aice`` & ice concentration (aggregate) & %
| ``f_aice1`` & ice concentration (category 1) & %
| ``f_aice2`` & ice concentration (category 2) & %
| ``f_aice3`` & ice concentration (category 3) & %
| ``f_aice4`` & ice concentration (category 4) & %
| ``f_aice5`` & ice concentration (category 5) & %
| ``f_aice6`` & ice concentration (category 6) & %
| ``f_aice7`` & ice concentration (category 7) & %
| ``f_aice8`` & ice concentration (category 8) & %
| ``f_aice9`` & ice concentration (category 9) & %
| ``f_aice10`` & ice concentration (category 10) & %
| ``f_vice1`` & ice volume (category 1) & m
| ``f_vice2`` & ice volume (category 2) & m
| ``f_vice3`` & ice volume (category 3) & m
| ``f_vice4`` & ice volume (category 4) & m
| ``f_vice5`` & ice volume (category 5) & m
| ``f_vice6`` & ice volume (category 6) & m
| ``f_vice7`` & ice volume (category 7) & m
| ``f_vice8`` & ice volume (category 8) & m
| ``f_vice9`` & ice volume (category 9) & m
| ``f_vice10`` & ice volume (category 10) & m
| ``f_uvel`` & zonal ice velocity & cm s\ :math:`^{-1}`
| ``f_vvel`` & meridional ice velocity & cm s\ :math:`^{-1}`
| ``f_fswdn`` & downwelling solar flux & W m\ :math:`^{-2}`
| ``f_flwdn`` & downwelling longwave flux & W m\ :math:`^{-2}`
| ``f_snow`` & snow fall rate received from coupler & cm
  day\ :math:`^{-1}`
| ``f_snow_ai`` & snow fall rate on ice cover & cm day\ :math:`^{-1}`
| ``f_rain`` & rain fall rate received from coupler & cm
  day\ :math:`^{-1}`
| ``f_rain_ai`` & rain fall rate on ice cover & cm day\ :math:`^{-1}`
| ``f_sst`` & sea surface temperature & C
| ``f_sss`` & sea surface salinity & g kg\ :math:`^{-1}`
| ``f_uocn`` & zonal ocean current & cm s\ :math:`^{-1}`
| ``f_vocn`` & meridional ocean current & cm s\ :math:`^{-1}`
| ``f_frzmlt`` & freeze/melt potential & W m\ :math:`^{-2}`
| ``f_fswabs`` & absorbed solar flux sent to coupler & W
  m\ :math:`^{-2}`
| ``f_fswabs_ai`` & absorbed solar flux in snow/ocn/ice & W
  m\ :math:`^{-2}`
| ``f_aldvr`` & visible direct albedo & %
| ``f_aldvi`` & near-infrared direct albedo & %
| ``f_flat`` & latent heat flux sent to coupler & W m\ :math:`^{-2}`
| ``f_flat_ai`` & ice/atm latent heat flux & W m\ :math:`^{-2}`
| ``f_fsens`` & sensible heat flux sent to coupler & W m\ :math:`^{-2}`
| ``f_fsens_ai``\ & ice/atm sensible heat flux & W m\ :math:`^{-2}`
| ``f_flwout`` & outgoing longwave flux sent to coupler & W
  m\ :math:`^{-2}`
| ``f_flwout_ai`` & ice/atm outgoing longwave flux & W m\ :math:`^{-2}`
| ``f_evap`` & evaporative water flux sent to coupler & cm
  day\ :math:`^{-1}`
| ``f_evap_ai`` & ice/atm evaporative water flux & cm day\ :math:`^{-1}`
| ``f_Tref`` & 2 m reference temperature & C
| ``f_Qref`` & 2 m reference specific humidity & g/kg
| ``f_congel`` & basal ice growth & cm day\ :math:`^{-1}`
| ``f_frazil`` & frazil ice growth & cm day\ :math:`^{-1}`
| ``f_snoice`` & snow-ice formation & cm day\ :math:`^{-1}`
| ``f_meltb`` & basal ice melt & cm day\ :math:`^{-1}`
| ``f_meltt`` & surface ice melt & cm day\ :math:`^{-1}`
| ``f_meltl`` & lateral ice melt & cm day\ :math:`^{-1}`
| ``f_fresh`` & ice/ocn fresh water flux sent to coupler & cm
  day\ :math:`^{-1}`
| ``f_fresh_ai`` & ice/ocn fresh water flux & cm day\ :math:`^{-1}`
| ``f_fsalt`` & ice to ocn salt flux sent to coupler & kg
  m\ :math:`^{-2}` day\ :math:`^{-1}`
| ``f_fsalt_ai`` & ice to ocn salt flux & kg m\ :math:`^{-2}`
  day\ :math:`^{-1}`
| ``f_fhnet`` & ice/ocn net heat flux sent to coupler& W
  m\ :math:`^{-2}`
| ``f_fhnet_ai`` & ice/ocn net heat flux & W m\ :math:`^{-2}`
| ``f_fswthru`` & SW transmitted through ice to ocean sent to coupler &
  W m\ :math:`^{-2}`
| ``f_fswthru_ai`` & SW transmitted through ice to ocean & W
  m\ :math:`^{-2}`
| ``f_strairx`` & zonal atm/ice stress & N m\ :math:`^{-2}`
| ``f_strairy`` & meridional atm/ice stress & N m\ :math:`^{-2}`
| ``f_strtltx`` & zonal sea surface tilt & m m\ :math:`^{-1}`
| ``f_strtlty`` & meridional sea surface tilt & m m\ :math:`^{-1}`
| ``f_strcorx`` & zonal coriolis stress & N m\ :math:`^{-2}`
| ``f_strcory`` & meridional coriolis stress & N m\ :math:`^{-2}`
| ``f_strocnx`` & zonal ocean/ice stress & N m\ :math:`^{-2}`
| ``f_strocny`` & meridional ocean/ice stress & N m\ :math:`^{-2}`
| ``f_strintx`` & zonal internal ice stress & N m\ :math:`^{-2}`
| ``f_strinty`` & meridional internal ice stress & N m\ :math:`^{-2}`
| ``f_strength``\ & compressive ice strength & N m\ :math:`^{-1}`
| ``f_divu`` & velocity divergence & % day\ :math:`^{-1}`
| ``f_shear`` & strain rate & % day\ :math:`^{-1}`
| ``f_opening`` & lead opening rate & % day\ :math:`^{-1}`
| ``f_sig1`` & normalized principal stress component &
| ``f_sig2`` & normalized principal stress component &
| ``f_daidtt`` & area tendency due to thermodynamics & %
  day\ :math:`^{-1}`
| ``f_daidtd`` & area tendency due to dynamics & % day\ :math:`^{-1}`
| ``f_dvidtt`` & ice volume tendency due to thermo. & cm
  day\ :math:`^{-1}`
| ``f_dvidtd`` & ice volume tendency due to dynamics & cm
  day\ :math:`^{-1}`
| ``f_mlt_onset`` & melt onset date &
| ``f_frz_onset`` & freeze onset date &
