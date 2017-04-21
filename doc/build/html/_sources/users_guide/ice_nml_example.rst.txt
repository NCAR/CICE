.. _cice_namelist_examples:


**************************
 CICE Namelist Examples
**************************

This section shows several examples of namelists from the coupled ice
model. These examples are taken directly from **$CASE/CaseDocs/ice\_in** for
the coupled model. Most of the variables in the namelist are determined
from environment variables set elsewhere in the scripts. Since the
namelists from the coupled model are “resolved” by the scripts, meaning
that the values of most of the shell script variables are put directly
into the namelist, examples are shown for the most commonly used
configurations. Variables that are commonly changed directly in the
namelist are the timestep dt and the number of subcycles per timestep in
the ice dynamics ndte.

Example 1: CESM Fully Coupled
-------------------------------

The following example is the namelist used for CESM fully coupled, or
the B configuration. A completely resolved version of the namelist will 
be written to **$CASE/CaseDocs/ice\_in** and **ice\_in** in the executable 
directory. Note that modifications to the CICE namelist go in **$CASE/user\_nl\_cice**.

::

    &setup_nml
     diagfreq               =  24   
     hist_avg               = .true.  
     histfreq               = 'm','x','x','x','x'
     histfreq_n             = 1,1,1,1,1           
     ice_ic         = 'b40.1850.track1.1deg.006.cice.r.0301-01-01-00000.nc'
     lcdf64         = .false. 
     pointer_file           = 'rpointer.ice'
     ndtd                   =  1
    /
    &grid_nml
     grid_file              = '/fis/cgd/cseg/csm/inputdata/ice/cice/global_gx1v6_200
    10402.grid'
     grid_format            = 'bin'
     grid_type              = 'displaced_pole'
     kcatbound              =  0 
     kmt_file               = '/fis/cgd/cseg/csm/inputdata/ice/cice/global_gx1v6_200
    90204.kmt'
    /
    &ice_nml
     advection              = 'remap'
     albedo_type            = 'default'
     albicei                = 0.45
     albicev                = 0.75
     albsnowi               = 0.73
     albsnowv               = 0.98
     evp_damping            = .false.
     kdyn           =  1 
     kitd           =  1 
     krdg_partic            = 1
     krdg_redist            = 1
     kstrength              =  1 
     ndte           =  120 
     r_snw          =  1.5 
     shortwave              = 'dEdd'
    /
    &tracer_nml
     tr_aero        = .true.
     tr_FY          = .true.
     tr_iage        = .true.
     tr_pond        = .true.
    /
    &domain_nml
     distribution_type              = 'cartesian'
     ew_boundary_type               = 'cyclic'
     ns_boundary_type               = 'open'
     processor_shape                = 'square-pop'
    /
    &ice_prescribed_nml
     prescribed_ice         = .false.
    /

Example 2: History File Namelist
-------------------------------------

The second namelist controls what variables are written to the history
file. By default, all files are written to the history file. Variables
that are not output are set in the namelist icefields\_nml. Some of the
following fields are not written to the history file since they can be
retrieved from the ocean history files. The melt and freeze onset fields
are not used, since the information they contain may not be correct if
the model is restarted mid-year. The ice areas and volumes for
categories six through ten are not used, since the default thickness
distribution consists of five ice categories.

::

     f_aero         = 'mxxxx' 
     f_aicen                = 'mxxxx' 
     f_aisnap               = 'mdxxx'
     f_apondn               = 'mxxxx' 
     f_congel               = 'mxxxx' 
     f_daidtd               = 'mxxxx' 
     f_daidtt               = 'mxxxx' 
     f_divu         = 'mxxxx' 
     f_dvidtd               = 'mxxxx' 
     f_dvidtt               = 'mxxxx' 
     f_faero_atm            = 'mxxxx' 
     f_faero_ocn            = 'mxxxx' 
     f_fhocn                = 'mxxxx' 
     f_fhocn_ai             = 'mxxxx' 
     f_frazil               = 'mxxxx' 
     f_fresh                = 'mxxxx' 
     f_fresh_ai             = 'mxxxx' 
     f_frz_onset            = 'xxxxx'
     f_frzmlt               = 'xxxxx'
     f_fsalt                = 'mxxxx' 
     f_fsalt_ai             = 'mxxxx' 
     f_fy           = 'mdxxx'
     f_hisnap               = 'mdxxx'
     f_icepresent           = 'mxxxx'
     f_meltb                = 'mxxxx' 
     f_meltl                = 'mxxxx' 
     f_meltt                = 'mxxxx' 
     f_mlt_onset            = 'xxxxx'
     f_opening              = 'mxxxx' 
     f_shear                = 'mxxxx' 
     f_sig1         = 'mxxxx' 
     f_sig2         = 'mxxxx' 
     f_snoice               = 'mxxxx' 
     f_sss          = 'xxxxx'
     f_sst          = 'xxxxx'
     f_strairx              = 'mxxxx' 
     f_strairy              = 'mxxxx' 
     f_strcorx              = 'mxxxx' 
     f_strcory              = 'mxxxx' 
     f_strength             = 'mxxxx' 
     f_strintx              = 'mxxxx' 
     f_strinty              = 'mxxxx' 
     f_strocnx              = 'mxxxx' 
     f_strocny              = 'mxxxx' 
     f_strtltx              = 'xxxxx'
     f_strtlty              = 'xxxxx'
     f_uocn         = 'xxxxx'
     f_uvel         = 'mxxxx' 
     f_vicen                = 'mxxxx' 
     f_vocn         = 'xxxxx'
     f_vvel         = 'mxxxx' 
    /
