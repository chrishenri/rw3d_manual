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

The parameter file consists in a text file. The following blocks of information has to be sequentially provided. 
Note that 3 header lines has to be written before each block. 

- :ref:`General setup`
- :ref:`Geometry`
- :ref:`Time discretization`
- :ref:`Advection`
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


**Type of inputs**

- logical: ``T`` for True; ``F`` for False
- array: The parameter is potentially spatially variable and can be read from a file. The following information have to be provided in a single line: ``file name`` ``multiplier`` ``ivar`` ``flag``
- string
- integer
- real

.. _tbl-grid:

  +-----------------------------+--------------------+-----------------------------------------------------------------------------------------------------------+
  | Variable                    | Type               | Description                                                                                               |
  +======+======================+====================+===========================================================================================================+
  | ``file name``               | ``string``         | name of the file. Put some text even if no file is used                                                   |
  +-----------------------------+--------------------+-----------------------------------------------------------------------------------------------------------+
  | ``multiplier``              | ``real``           | multiplier of the variable                                                                                |
  +-----------------------------+--------------------+-----------------------------------------------------------------------------------------------------------+
  | ``ivar``                    | ``integer``        | variable index of the variable in the gslib array                                                         |
  +-----------------------------+--------------------+-----------------------------------------------------------------------------------------------------------+
  | ``flag``                    | ``integer``        | way to read the values of the parameter                                                                   |
  |                             |                    |                                                                                                           |
  |                             |                    | values:                                                                                                   |
  |                             |                    |                                                                                                           |
  |                             |                    | - 0: the parameter is not read from a file and is defined as the multiplier                               |
  |                             |                    | - 1: the parameter is read from the ascii file specified in ``file name``                                 |
  |                             |                    | - 2: the parameter is read from a MODFLOW type file (only available for fluxes)                           |
  |                             |                    | - 3: the parameter is read from the ascii file specified in ``file name``but from the bottom of the file  |
  |                             |                    | - 4: the parameter is read from a netcdf file                                                             |
  |                             |                    |                                                                                                           |
  +-----------------------------+--------------------+-----------------------------------------------------------------------------------------------------------+


.. _General setup:

General setup
`````````````

.. _tbl-grid:
  
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  |Line  | Variable                    | Type               | Description                                                                     |
  +======+=============================+====================+=================================================================================+
  | 10   | ``idebug``                  | ``integer``        | ``idebug``: Integer defining degree of debugging as written in rw3d_general.dbg |
  |      |                             |                    |                                                                                 |
  |      |                             |                    | values:                                                                         |
  |      |                             |                    |                                                                                 |
  |      |                             |                    |         - -1: Do not write the velocity field                                   |
  |      |                             |                    |         - 0: Normal Run                                                         |
  |      |                             |                    |         - 10: Maximum Debugging Degree                                          |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 11   | ``nspe_aq`` ``nspe_min``    | ``integer``        | ``nspe_aq``: number of aqueous (i.e., mobile) species                           |
  |      |                             |                    |                                                                                 |
  |      |                             |                    | ``nspe_min``: number of aqueous (i.e., immobile) species                        |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 12   | ``name_aq`` ``name_min``    | ``string``         | ``name_aq``: name(s) of aqueous (i.e., mobile) species                          |
  |      |                             |                    |                                                                                 |
  |      |                             |                    | ``name_min``: name(s) of aqueous (i.e., immobile) species                       |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 13   | ``t_sim``                   | ``real``           | ``t_sim``: simulation time                                                      |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+
  | 14   | ``transient_flag``          | ``logical``        | ``transient_flag``: True if transient conditions                                |
  +------+-----------------------------+--------------------+---------------------------------------------------------------------------------+


.. _Geometry:

Geometry
`````````````

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 15   | ``nx`` ``ny`` ``nz``                                                    | ``integer``        | ``nx``: number of cell in the *x* direction (i.e., columns)                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ny``: number of cell in the *y* direction (i.e., rows)                               |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``nz``: number of cell in the *z* direction (i.e., layers)                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dx``                                                                  | ``array``          | ``dx``: cell size in the *x* direction                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dy``                                                                  | ``array``          | ``dy``: cell size in the *y* direction                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dz``                                                                  | ``array, 1 option``| ``dz``: cell size in the *z* direction                                                 |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | option: Constant layer thickness                                                       |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` if constant layer thickness, ``F`` if variable layer thickess  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``floor``                                                               | ``array``          | ``floor``: floor elevation                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``inactive_cell``                                                       | ``array, 1 option``| ``inactive_cell``: binary characteriztion of active/inactive cells                     |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | values: 0: active; 1: inactive                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | option: Particle in inactive cells are killed                                          |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` particles are killed, ``F`` particles bounce at the boundary   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``ib(1,1)`` ``ib(1,2)`` ``ib(2,1)`` ``ib(2,2)`` ``ib(3,1)`` ``ib(3,2)`` | ``integer``        | Defines the particle behaviour if a domain boundary is reached.                        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ib(1,1)``: left boundary, defined by x_min                                           |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ib(1,2)``: right boundary, defined by x_max                                          |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ib(2,1)``: front boundary, defined by y_min                                          |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ib(2,2)``: back boundary, defined by y_max                                           |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ib(2,1)``: bottom boundary, defined by z_min                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ib(2,2)``: top boundary, defined by z_max                                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | values:                                                                                |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - 0: The particle is killed                                                         |
  |      |                                                                         |                    |    - 1: The particle is sent to the opposite side of the domain                        |
  |      |                                                                         |                    |    - 2: The particle bounces at the boundary                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+



.. _Time discretization:

Time discretization
`````````````

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 16   | ``dt_method``                                                           | ``string``         | Defines the way time steps are computed.                                               |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | values: description provided in the section :ref:`Time discretization process`         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``constant_dt``                                                                   |
  |      |                                                                         |                    |    - ``constant_cu``                                                                   |
  |      |                                                                         |                    |    - ``constant_damt``                                                                 |
  |      |                                                                         |                    |    - ``constant_dadecay``                                                              |
  |      |                                                                         |                    |    - ``optimum_dt``                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``dt`` ``courant`` ``peclet`` ``DaKINETIC`` ``DaDECAY`` ``DaMMT``       | ``real``           | Defines the way time steps are computed.                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+



.. _Advection:

Advection
`````````````

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 15   | ``advection_action``                                                    | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 15   | ``q_x``                                                                 | ``array``          | flux in the *x* direction                                                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 15   | ``q_y``                                                                 | ``array``          | flux in the *y* direction                                                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 15   | ``q_z``                                                                 | ``array``          | flux in the *z* direction                                                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 16   | ``porosity``                                                            | ``array, 1 option``| porosity (or water content)                                                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | option: transient conditions                                                           |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+





