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

- Flag: ``T`` for True; ``F`` for False
- 

.. _General setup:

General setup
`````````````

.. _tbl-grid:
  
  +------+-----------------------------+---------------------------------------------------------------------------------+
  |Line  | Variable                    | Description                                                                     |
  +======+=============================+=================================================================================+
  | 10   | ``idebug``                  | ``idebug``: Integer defining degree of debugging as written in rw3d_general.dbg |
  |      |                             | options:                                                                        |
  |      |                             |                                                                                 |
  |      |                             |         - ``idebug`` = -1: Do not write the velocity field                      |
  |      |                             |         - ``idebug`` = 0: Normal Run                                            |
  |      |                             |         - ``idebug`` = 10: Maximum Debugging Degree                             |
  +------+-----------------------------+---------------------------------------------------------------------------------+
  | 11   | ``nspe_aq``, ``nspe_min``   | ``nspe_aq``: number of aqueous (i.e., mobile) species                           |
  |      |                             |                                                                                 |
  |      |                             | ``nspe_min``: number of aqueous (i.e., immobile) species                        |
  +------+-----------------------------+---------------------------------------------------------------------------------+
  | 12   | ``name_aq``, ``name_min``   | ``name_aq``: name(s) of aqueous (i.e., mobile) species                          |
  |      |                             |                                                                                 |
  |      |                             | ``name_min``: name(s) of aqueous (i.e., immobile) species                       |
  +------+-----------------------------+---------------------------------------------------------------------------------+
  | 13   | ``t_sim``                   | ``t_sim``: simulation time                                                      |
  +------+-----------------------------+---------------------------------------------------------------------------------+
  | 14   | ``transient_flag``          | ``transient_flag``: Flag for transient conditions                               |
  +------+-----------------------------+---------------------------------------------------------------------------------+


.. _Geometry:

Geometry
`````````````


