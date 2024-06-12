.. _inputs:

Input Instructions
===============

2 files have to be provided: 

- Name file: File with names for input and output files (by default: **rw3d.nam**)
- Parameter file: File with parameters (file name to be defined in the name file)


Name file
------------

The file consists in 15 lines that must be defined as follow (even if the output option is disabled in the parameter file): 

.. _tbl-grid:
 
  +------+--------------+------------------------------------------------------------+
  |Line  | item type    | Description                                                |
  +======+==============+============================================================+
  | 1    | File name    | Parameter file                                             |
  +------+--------------+------------------------------------------------------------+
  | 2    | File name    | Output histogram (pdf) of particle arrival times (btcs)    |
  +------+--------------+------------------------------------------------------------+
  | 3    | File name    | Output with cumulative pdf particle arrival times (cbtcs)  |
  +------+--------------+------------------------------------------------------------+
  | 4    | File name    | Output with particle snapshots with time                   |
  +------+--------------+------------------------------------------------------------+
  | 5    | File name    | Output with particle paths                                 |
  +------+--------------+------------------------------------------------------------+
  | 6    | File name    | Output with cartesian spatial moments                      |
  +------+--------------+------------------------------------------------------------+
  | 7    | File name    | Output with temporal moments of breakthrough curves        |
  +------+--------------+------------------------------------------------------------+
  | 8    | File name    | Output with velocity field (for idebug :math:`\geq 1`)     |
  +------+--------------+------------------------------------------------------------+
  | 9    | File name    | Output with debug file                                     |
  +------+--------------+------------------------------------------------------------+
  | 10   | File name    | Output with information about particle exiting the domain  |
  +------+--------------+------------------------------------------------------------+
  | 11   | File name    | Output with btcs of particle entering registration lenses  |
  +------+--------------+------------------------------------------------------------+
  | 12   | File name    | Output with cbtcs of particle entering registration lenses |
  +------+--------------+------------------------------------------------------------+
  | 13   | File name    | Output with btcs of particle exiting registration lenses   |
  +------+--------------+------------------------------------------------------------+
  | 14   | File name    | Output with cbtcs of particle exiting registration lenses  |
  +------+--------------+------------------------------------------------------------+
  | 15   | File name    | Output with plume history                                  |
  +------+--------------+------------------------------------------------------------+


Parameter file
------------

- :ref:`General setup`
- :ref:`Geometry`
- Time discretization
- Advection
- Heads
- Sinks
- Diffusion / Dispersion
- Mass Transfer
- Reactions
    - Retardation
    - First-order reactions
    - Bimolecular 
- Observations 
- Injection
- Recirculation
- Outputs


Type of inputs: 

- Logical flag: ``T`` for True; ``F`` for False
- Array: The parameter is potentially spatially variable and can be read from a file. 
      The following information have to be provided in a single line: ``file name`` ``multiplier`` ``ivar`` ``flag``

.. _tbl-grid:

  +--------------+-----------------------------------------------------------------------------------------------------------+
  | file name    | name of the file. Put some text even if no file is used                                                   |
  | multiplier   | multiplier of the variable                                                                                |
  | ivar         | variable index of the variable in the gslib array                                                         |
  | flag         | 0: the parameter is not read from a file and is defined as the multiplier                                 |
  |              |                                                                                                           |
  |              | 1: the parameter is read from the ascii file specified in ``file name``                                   |
  |              |                                                                                                           |
  |              | 2: the parameter is read from a MODFLOW type file (only available for fluxes)                             |
  |              |                                                                                                           |
  |              | 3: the parameter is read from the ascii file specified in ``file name``but from the bottom of the file    | 
  |              |                                                                                                           |
  |              | 4: the parameter is read from a netcdf file                                                               | 
  +--------------+-----------------------------------------------------------------------------------------------------------+


.. _General setup:

General setup
`````````````

.. _tbl-grid:
  
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  |Line  | Variable                    | Type               | Description                                                                     |
  +======+=============================+====================+=================================================================================+
  | 10   | ``idebug``                  | ``integer``        | ``idebug``: Integer defining degree of debugging as written in rw3d_general.dbg |
  |      |                             |                    | options:                                                                        |
  |      |                             |                    |                                                                                 |
  |      |                             |                    |         - ``idebug`` = -1: Do not write the velocity field                      |
  |      |                             |                    |         - ``idebug`` = 0: Normal Run                                            |
  |      |                             |                    |         - ``idebug`` = 10: Maximum Debugging Degree                             |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 11   | ``nspe_aq``, ``nspe_min``   | ``integer``        | ``nspe_aq``: number of aqueous (i.e., mobile) species                           |
  |      |                             |                    |                                                                                 |
  |      |                             |                    | ``nspe_min``: number of aqueous (i.e., immobile) species                        |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 12   | ``name_aq``, ``name_min``   | ``string``         | ``name_aq``: name(s) of aqueous (i.e., mobile) species                          |
  |      |                             |                    |                                                                                 |
  |      |                             |                    | ``name_min``: name(s) of aqueous (i.e., immobile) species                       |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 13   | ``t_sim``                   | ``real``           | ``t_sim``: simulation time                                                      |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 14   | ``transient_flag``          | ``logical``        | ``transient_flag``: Flag for transient conditions                               |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+


.. _Geometry:

Geometry
`````````````

.. _tbl-grid:
  
  +------+-----------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                    | Type               | Description                                                                            |
  +======+=============================+====================+========================================================================================+
  | 15   | ``nx``, ``ny``, ``nz``      | ``integer``        | ``nx``: number of cell in the *x* direction (i.e., columns)                            |
  |      |                             |                    | ``ny``: number of cell in the *y* direction (i.e., rows)                               |
  |      |                             |                    | ``nz``: number of cell in the *z* direction (i.e., layers)                             |
  +------+-----------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dx``                      | ``array``          | ``dx``: cell size in the *x* direction                                                 |
  +------+-----------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dy``                      | ``array``          | ``dy``: cell size in the *y* direction                                                 |
  +------+-----------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dz``                      | ``array, 1 option``| ``dz``: cell size in the *z* direction                                                 |
  |      |                             |                    | option: Constant layer thickness                                                       |
  |      |                             |                    |                                                                                        |
  |      |                             |                    |    - ``logical``: ``T`` if constant layer thickness, ``F`` if variable layer thickess  |
  +------+-----------------------------+--------------------+----------------------------------------------------------------------------------------+