.. _inputs:

Input Instructions
===============

2 files have to be provided: 

- Name file: File with names for input and output files (by default: **rw3d.nam**)
- Parameter file: File with parameters (file name to be defined in the name file)


Type of inputs
------------

- ``logical``: ``T`` for True; ``F`` for False
- ``string``
- ``integer``
- ``real``
- ``time_function``: A text file describing the temporal evolution of a parameter. 
- ``array``: The parameter is potentially spatially variable and can be read from a file. The following information have to be provided in a single line: ``file name`` ``multiplier`` ``ivar`` ``flag``. 
  In some specific cases, one or two additional parameters (*options*) must also be provided. 

.. container::
   :name: table-array

   .. table:: Array.

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
      |                             |                    | - 4: the parameter is read from a netcdf file. For the moment, only NETCDF3_64BIT file type is supported  |
      |                             |                    |                                                                                                           |
      +-----------------------------+--------------------+-----------------------------------------------------------------------------------------------------------+


File format for *arrays*
~~~~~~~~~~

- **ascii file**: ``flag``=1 or 3

A text file (with name ``file name``) must follow the following format: 

.. container::
   :name: table-array

   .. table:: Ascii file format.
 
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      |Line  | Variable                                                                | Type               | Description                                                                            |
      +======+=========================================================================+====================+========================================================================================+
      | 1    | ``header``                                                              | ``string``         | header line (*not used by the code*)                                                   |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | 2    | ``nvar``                                                                | ``integer``        | number of variables                                                                    |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | repeat the following line ``nvar`` times:                                                                                                                                                    |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | 3    | ``nvar_name``                                                           | ``string``         | name of the variable (*not used by the code*)                                          |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | repeat the following line :math:`nx \times ny \times nz` times:                                                                                                                              |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | 4    | ``values``                                                              | ``real``           | variable values                                                                        |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

A single file can contain information about multiple (``nvar``) variables. 
The values of each variable is defined in space separated columns. The file must contain :math:`nx \times ny \times nz` rows, where :math:`ni` is the number of cells in the *i*-th dimension. 
All values are corrected by multiplying the read values by ``multiplier``. 

The values of the variable with index ``ivar`` are read as follow: 

.. code-block:: fortran

    do k=1,nz
        do j=1,ny
            do i=1,nx
                read(iunit,*) (aline(jcol),jcol=1,nvar)    ! read all columns, i.e., all variables values, corresponding to the location (i,j,k)
                values(i,j,k) = aline(ivar) * multiplier   ! values of the selected variable (corresponding to the column ivar), corrected by a user-defined constant (multiplier) 
            end do
        end do
    end do


- **netcdf file**: ``flag``=4


File format for *time function*
~~~~~~~~~~



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

- :ref:`General setup`
- :ref:`Geometry`
- :ref:`Time discretization`
- :ref:`Advection`
- :ref:`Heads`
- :ref:`Sinks`
- :ref:`Diffusion / Dispersion`
- :ref:`Mass Transfer`
- :ref:`Reactions`
    - :ref:`Retardation`
    - :ref:`First-order decay`
    - :ref:`Bimolecular`
- :ref:`Observation` 
    - :ref:`Extraction well`
    - :ref:`Control plane`
    - :ref:`Registration lense`
- :ref:`Injection`
- :ref:`Well recirculation`
- Outputs

.. warning::
    Note that 3 header lines has to be written before each block. 


.. _General setup:

General setup
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``idebug``                                                              | ``integer``        | ``idebug``: Integer defining degree of debugging as written in rw3d_general.dbg        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*:                                                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |         - -1: Do not write the velocity field                                          |
  |      |                                                                         |                    |         - 0: Normal Run                                                                |
  |      |                                                                         |                    |         - 10: Maximum Debugging Degree                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``nspe_aq`` ``nspe_min``                                                | ``integer``        | ``nspe_aq``: number of aqueous (i.e., mobile) species                                  |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``nspe_min``: number of aqueous (i.e., immobile) species                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``name_aq``                                                             | ``string``         | ``name_aq``: name(s) of aqueous (i.e., mobile) species                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``name_min``                                                            | ``string``         | ``name_min``: name(s) of aqueous (i.e., immobile) species                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``t_sim``                                                               | ``real``           | ``t_sim``: simulation time                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9    | ``transient_flag``                                                      | ``logical``        | ``transient_flag``: True if transient conditions                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``transient_flag`` == ``F``, go to :ref:`Geometry`; if ``transient_flag`` == ``T``, fill up the following:                                                                                |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10   | ``read_dt_from_file``  ``loop_dt``                                      | ``logical``        | ``read_dt_from_file``: True if the time steps are read from an ascii file              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``read_dt_from_file`` == ``T``:                                                                                                                                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 11   | ``dt_file``                                                             | ``string``         | ``dt_file``: name of the ascii file listing the time steps                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``read_dt_from_file`` == ``T``, go to :ref:`Geometry`; if ``read_dt_from_file`` == ``F``:                                                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 12   | ``n_dt``                                                                | ``integer``        | ``n_dt``: number of time steps                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated :math:`n_{dt}` times:                                                                                                                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 13...| ``dt``                                                                  | ``real``           | ``dt``: time step                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

.. note::
    The line number in each table is reset for each block to simplify the description of the inputs. Each block is to be filled up sequentially, so the *absolute* line number will be different. 


**Example**: A problem involving 2 aqueous chemical species (named *A* and *B*) and 0 mineral species. 
The simulation will run for 150.0 time units with transient parameters. 
The temporal discretization of the transient parameters is specified in the file *time_discretization.dat* and the transient paramters are set to be looped in time until the end of the simulation. 

::

   -----------------------------------------------------------------
    General Setup
   -----------------------------------------------------------------
   0                                   !idebug
   2   0                               !nspe_aq; nspe_min
   A   B                               !name_aq
   -                                   !name_min
   150.0                               !t_sim
   T                                   !transient_flag
   T   T                               !read_dt_from_file; loop_dt
   time_discretization.dat             !dt_file


.. _Geometry:

Geometry
~~~~~~~~~~

.. raw:: latex

    \begin{landscape}

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``nx`` ``ny`` ``nz``                                                    | ``integer``        | ``nx``: number of cell in the *x* direction (i.e., columns)                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ny``: number of cell in the *y* direction (i.e., rows)                               |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``nz``: number of cell in the *z* direction (i.e., layers)                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``dx``                                                                  | ``array``          | ``dx``: cell size in the *x* direction                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``dy``                                                                  | ``array``          | ``dy``: cell size in the *y* direction                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``dz``                                                                  | ``array, 1 option``| ``dz``: cell size in the *z* direction                                                 |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: Constant layer thickness                                                     |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` if constant layer thickness, ``F`` if variable layer thickess  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``floor``                                                               | ``array``          | ``floor``: floor elevation                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9    | ``inactive_cell``                                                       | ``array, 1 option``| ``inactive_cell``: binary characteriztion of active/inactive cells                     |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*: 0: active; 1: inactive                                                       |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: Particle in inactive cells are killed                                        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` particles are killed, ``F`` particles bounce at the boundary   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10   | ``ib(1,1)`` ``ib(1,2)`` ``ib(2,1)`` ``ib(2,2)`` ``ib(3,1)`` ``ib(3,2)`` | ``integer``        | Defines the particle behaviour if a domain boundary is reached.                        |
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
  |      |                                                                         |                    | *values*:                                                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - 0: The particle is killed                                                         |
  |      |                                                                         |                    |    - 1: The particle bounces at the boundary                                           |
  |      |                                                                         |                    |    - 2: The particle is sent to the opposite side of the domain                        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

.. raw:: latex

    \end{landscape}

**Example**: The domain is discretized in 1200 cells in the *x*-direction, 1400 cells in the *y*-direction and 11 cells in the *z*-direction. 
The cell size in *x* and *y* is fixed to 100 space units. The cell size in the *z*-direction is variable in space and specified in the file *dz.dat*. 
The bottom elevation of the domain (floor) is also variable in space and specified in the file *floor.dat*.  
The location of inactive cells is provided in the file *InactCell.dat* and particles reaching an inactive cell will be killed. 
Finally, particles reaching the boundary of the domain will be killed, expect at the top of the domain, where particles will bounce.  

::

   ---------------------------------------------------------------
    Geometry
   ---------------------------------------------------------------
   1200    1400    11                               !nx; ny; nz
   not_used             100.0    1    0             !dx
   not_used             100.0    1    0             !dy
   dz.dat               1.0      1    1    F        !dz
   floor.dat            1.0      1    1             !floor
   InactCell.dat        1.0      1    1    T        !inactive_cell
   0   0   0   0   0   1                            !ib(1,1); ib(1,2); ib(2,1); ib(2,2); ib(3,1); ib(3,2)


.. _Time discretization:

Time discretization
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``dt_method``                                                           | ``string``         | Defines the way time steps are computed                                                |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*: description provided in section :ref:`Time discretization process`           |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``constant_dt``                                                                   |
  |      |                                                                         |                    |    - ``constant_cu``                                                                   |
  |      |                                                                         |                    |    - ``constant_damt``                                                                 |
  |      |                                                                         |                    |    - ``constant_dadecay``                                                              |
  |      |                                                                         |                    |    - ``optimum_dt``                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``dt`` ``courant`` ``peclet`` ``DaKINETIC`` ``DaDECAY`` ``DaMMT``       | ``real``           | Time step restrictor, as defined in section :ref:`Time discretization process`         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

**Example**: 

::

   -----------------------------------------------------------------
    Time discretization
   -----------------------------------------------------------------
   constant_cu																			!... 
   1.0  0.5  0.2  0.1  0.1  0.1															!... 
   0.99																					!... time step relaxation


.. _Advection:

Advection
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``advection_action``                                                    | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``advection_method``                                                    | ``logical``        | Method for advective motion of particles, as defined in :ref:`Advective motion`        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*:                                                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``exponential``                                                                   |
  |      |                                                                         |                    |    - ``eulerian``                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``q_x``                                                                 | ``array``          | flux in the *x* direction                                                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``q_y``                                                                 | ``array``          | flux in the *y* direction                                                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``q_z``                                                                 | ``array``          | flux in the *z* direction                                                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9    | ``porosity``                                                            | ``array, 1 option``| porosity (or water content)                                                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

**Example**: 

::

   -----------------------------------------------------------------
    Advection
   -----------------------------------------------------------------
   T																						!... advection_action
   Eulerian			    																	!... advection_method
   qx_DK1.nc  							1.0  	1  	4  	T									!... qx array
   qy_DK1.nc  							1.0   	1  	4  	T  									!... qy array
   qz_DK1.nc  							1.0   	1  	4  	T  									!... qz array
   porosity_DK1.dat   					1.0		1  	1  	F									!... porosity array


.. _Heads:

Heads
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``heads_action``                                                        | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``heads``                                                               | ``array``          | cell-by-cell head elevation                                                            |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``heads_threshold``                                                     | ``real``           | maximum head elevation for the cell to be considered dry                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. _Sinks:

Sinks
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``sinks_action``                                                        | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``n_sinks``                                                             | ``integer``        | number of sink                                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated :math:`n_{sinks}` times:                                                                                                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6... | ``sink_name`` ``Q_sink``                                                |``string`` ``array``| ``sink_name``: name of the sink                                                        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``Q_sink``: flow going into the sink (:math:`L^3/T`)                                   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. _Diffusion / Dispersion:

Dispersion / Disffusion
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``dispersion_action``                                                   | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``dispersivity_L``                                                      | ``array``          | dispersivity in the longitudinal direction                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``dispersivity_TH``                                                     | ``array``          | dispersivity in the transverse horizontal direction                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``dispersivity_TV``                                                     | ``array``          | dispersivity in the transverse vertical direction                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``diffusion_L``                                                         | ``array, 1 option``| effective molecular diffusion in the longitudinal direction                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9    | ``diffusion_TH``                                                        | ``array, 1 option``| effective molecular diffusion in the transverse horizontal direction                   |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10   | ``diffusion_TV``                                                        | ``array, 1 option``| effective molecular diffusion in the transverse vertical direction                     |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 11   | ``dispersivity_factor`` (repeat ``nspe_aq`` times)                      | ``real``           | Species dependent multiplier for the dispersivity coefficients                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *for each aqueous species, the effective dispersivity coefficients*                    |
  |      |                                                                         |                    | *is multiplied by the given factor*                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 12   | ``diffusion_factor`` (repeat ``nspe_aq`` times)                         | ``real``           | Species dependent multiplier for the diffusion coefficients                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *for each aqueous species, the effective diffusion coefficient*                        |
  |      |                                                                         |                    | *is multiplied by the given factor*                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. _Mass transfer:

Mass transfer
~~~~~~~~~~

.. _tbl-grid:

  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``mass_transfer_action``                                                | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``type_mass_transfer``                                                  | ``string``         | Defines the type of mass transfer process                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*: description provided in section :ref:`Multirate Mass Transfer process`       |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``multirate``                                                                     |
  |      |                                                                         |                    |    - ``spherical_diffusion``                                                           |
  |      |                                                                         |                    |    - ``layered_diffusion``                                                             |
  |      |                                                                         |                    |    - ``cylindral_diffusion``                                                           |
  |      |                                                                         |                    |    - ``power_law``                                                                     |
  |      |                                                                         |                    |    - ``lognormal_law``                                                                 |
  |      |                                                                         |                    |    - ``composite_law``                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``type_mass_transfer`` = ``multirate`` or ``spherical_diffusion`` or ``layered_diffusion`` or ``cylindral_diffusion``:                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``num_immobile_zones``                                                  | ``integer``        | number of immobile zones                                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated ``num_immobile_zones`` times:                                                                                                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``porosity_immobile``                                                   | ``array``          | porosity in the ith immobile zone                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``mass_transfer_coef``                                                  | ``array``          | mass transfer coefficient in the ith immobile zone                                     |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``type_mass_transfer`` = ``power_law``:                                                                                                                                                   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``num_immobile_zones``                                                  | ``integer``        | number of immobile zones                                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated ``num_immobile_zones`` times:                                                                                                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``btot``                                                                | ``array``          | total capacity                                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``Amin``                                                                | ``array``          | minimum mass transfer coefficient                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9    | ``Amax``                                                                | ``array``          | maximum mass transfer coefficient                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10   | ``power``                                                               | ``array``          | power coefficient                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``type_mass_transfer`` = ``lognormal_law``:                                                                                                                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``num_immobile_zones``                                                  | ``integer``        | number of immobile zones                                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated ``num_immobile_zones`` times:                                                                                                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``btot``                                                                | ``array``          | total capacity                                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``mean``                                                                | ``array``          | mean of the lognormal mass transfer coefficients                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9    | ``stdv``                                                                | ``array``          | standart deviation in mass transfer coefficients                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``type_mass_transfer`` = ``composite_media``:                                                                                                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``nmrate`` ``nsph`` ``ncyl`` ``nlay``                                   | ``integer``        | ``nmrate``: number of immobile zones for the multirate mass transfer model             |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``nsph``: number of immobile zones for the spherical diffusion model                   |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``ncyl``: number of immobile zones for the cylindral diffusion model                   |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``nlay``: number of immobile zones for the layered diffusion model                     |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | for each mass transfer model, fill up sequentially the corresponding parameters as described above                                                                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. _Reactions:

Reactions
~~~~~~~~~~

.. _Retardation:

Retardation
""""""""""

.. _tbl-grid:

  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``retardation_action``                                                  | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated ``nspe_aq`` times:                                                                                                                                                            |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5... | ``R``                                                                   | ``array``          | retardation factor for a given aqueous species                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``mass_transfer_action``=``T``:                                                                                                                                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``type_mass_transfer`` = ``multirate``:                                                                                                                                                   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... to be repeated ``nspe_aq`` times:                                                                                                                                                        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... to be repeated ``num_immobile_zones`` times:                                                                                                                                          |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6 ...| ``Rim``                                                                 | ``array``          | retardation factor for a given aqueous species and given imoobile zone                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``type_mass_transfer`` = ``spherical_diffusion`` or ``layered_diffusion`` or ``cylindral_diffusion`` or ``power_law`` or ``lognormal_law``:                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... to be repeated ``nspe_aq`` times:                                                                                                                                                        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6 ...| ``Rim``                                                                 | ``array``          | retardation factor for a given aqueous species (for all imoobile zones)                |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

.. note::
    Retardation is not available if ``type_mass_transfer`` = ``composite_media``. 


.. _First-order decay:

First-order decay
""""""""""

.. _tbl-grid:

  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``first_order_action``                                                  | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``nspe_decay``                                                          | ``integer``        | number of species involved in the decay network                                        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``name_spe_decay``                                                      | ``string``         | name(s) of the species involved in the decay network                                   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``type_decay_network``                                                  | ``string``         | type of the decay network                                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*:                                                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``serial``: sequential degradation (e.g., A :math:`\to` B :math:`\to` C)          |
  |      |                                                                         |                    |    - ``serial_moments``: sequential degradation solving higher moments in the          |
  |      |                                                                         |                    |    derivation of transition probabilities (slower, but more accurate for large dt)     |
  |      |                                                                         |                    |    - ``generic``: generic reaction network                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | - if ``type_decay_network`` = ``serial``:                                                                                                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... to be repeated ``nspe_decay`` times:                                                                                                                                                     |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8 ...| ``k``                                                                   | ``array``          | first-order decay rate                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... do not fill for the first species for the serial network:                                                                                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9 ...| ``y``                                                                   | ``array``          | yield coefficient                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``mass_transfer_action``=``T``:                                                                                                                                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... if ``type_mass_transfer`` = ``multirate``:                                                                                                                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... to be repeated ``nspe_decay`` times:                                                                                                                                                  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ......... to be repeated ``num_immobile_zones`` times:                                                                                                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10...| ``kim``                                                                 | ``array``          | first-order decay rate for a given aqueous species and given imoobile zone             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... if ``type_mass_transfer`` = ``spherical_diffusion`` or ``layered_diffusion`` or ``cylindral_diffusion`` or ``power_law`` or ``lognormal_law``:                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... to be repeated ``nspe_decay`` times:                                                                                                                                                  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10...| ``kim``                                                                 | ``array``          | first-order decay rate a given aqueous species (for all imoobile zones)                |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | - if ``type_decay_network`` = ``serial_moments``:                                                                                                                                            |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... to be repeated ``nspe_decay`` times:                                                                                                                                                     |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8 ...| ``k``                                                                   | ``array``          | first-order decay rate                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... do not fill for the first species for the serial network:                                                                                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9 ...| ``y``                                                                   | ``array``          | yield coefficient                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | - if ``type_decay_network`` = ``generic``:                                                                                                                                                   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... to be repeated ``nspe_decay`` times:                                                                                                                                                     |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8 ...| ``k``                                                                   | ``array``          | first-order decay rate                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... to be repeated ``nspe_decay`` x ``nspe_decay`` times:                                                                                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9 ...| ``y``                                                                   | ``array``          | yield coefficient                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``mass_transfer_action``=``T``:                                                                                                                                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... if ``type_mass_transfer`` = ``multirate``:                                                                                                                                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... to be repeated ``nspe_decay`` times:                                                                                                                                                  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ......... to be repeated ``num_immobile_zones`` times:                                                                                                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10...| ``kim``                                                                 | ``array``          | first-order decay rate for a given aqueous species and given imoobile zone             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ... if ``type_mass_transfer`` = ``spherical_diffusion`` or ``layered_diffusion`` or ``cylindral_diffusion`` or ``power_law`` or ``lognormal_law``:                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | ...... to be repeated ``nspe_decay`` times:                                                                                                                                                  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 10...| ``kim``                                                                 | ``array``          | first-order decay rate a given aqueous species (for all imoobile zones)                |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. note::
    ``serial_moments`` option is not available if ``mass_transfer_action`` = ``T``. 

.. note::
    Linear reaction solver is not available if ``type_mass_transfer`` = ``composite_media``. 


.. _Bimolecular:

Bimolecular reactions
""""""""""

.. _tbl-grid:

  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``kinetic_action``                                                      | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``n_reactions``                                                         | ``integer``        | number of reactions in the network                                                     |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated ``n_reactions`` times:                                                                                                                                                        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``reaction_string``                                                     | ``string``         | string describing a reaction                                                           |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *instructions*:                                                                        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - following the form: [name_sp1] + [name_sp1] --> [name_sp3]                        |
  |      |                                                                         |                    |    - each specie names in brakets (``[]``)                                             |
  |      |                                                                         |                    |    - reactants and products separeted by an arrow (``-->``)                            |
  |      |                                                                         |                    |    - The name of the species must follow the names specified in :ref:`General setup`   |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *available reaction so far*:                                                           |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - one reactant and zero product: A --> 0                                            |
  |      |                                                                         |                    |    - one reactant and one product: A --> C                                             |
  |      |                                                                         |                    |    - one reactant and two product: A --> C + D                                         |
  |      |                                                                         |                    |    - two reactants and zero product: A + B --> 0                                       |
  |      |                                                                         |                    |    - two reactants and one product: A + B --> C                                        |
  |      |                                                                         |                    |    - two reactants and two product: A + B --> C + D                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated ``n_reactions`` times:                                                                                                                                                        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``kf``                                                                  | ``array``          | reaction rate                                                                          |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. _Observation:

Observation
~~~~~~~~~~

.. note::
    Information about all observation surfaces (extraction wells, planes, registration lenses) have to be provided in a single block, without header lines between them,  


.. _Extraction well:

Extraction well
""""""""""

.. note::
    Extraction wells acting as a sink (strong or weak) can be specified in :ref:`Sinks` if the sink is considered uniformly in the cell where a well is located.
    In :ref:`Observation`, extraction wells are considered as a sink at the well location, with converging velocity leading to the actual well location. 
    See :ref:`Sink process` for more details about the implementation. 


.. _tbl-grid:

  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  |Line  | Variable                                                                 | Type                                         | Description                                                                                                                |
  +======+==========================================================================+==============================================+============================================================================================================================+
  | 4    | ``n_well``                                                               | ``integer``                                  | number of wells                                                                                                            |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | to be repeated ``n_well`` times:                                                                                                                                                                                                                            |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | 6    | ``wellID`` ``xw`` ``yw`` ``rw`` ``zbot`` ``ztop`` ``partOUT`` ``SaveBTC``| ``string`` ``real`` (x5) ``logical`` (x2)    | ``wellID``: name of the well                                                                                               |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``xw``: x-coordinate of the center of the well                                                                             |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``yw``: y-coordinate of the center of the well                                                                             |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``rw``: radius of the well                                                                                                 |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``zbot``: z-coordinate of the bottom of the well (or well screen)                                                          |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``ztop``: z-coordinate of the top of the well (or well screen)                                                             |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``partOUT``: True (T) if particles reaching the observation location are killed                                            |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | ``SaveBTC``:  True (T) if breakthrough curves are saved and printed                                                        |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | 7    | ``Qwell_method``                                                         | ``string``                                   | Method with which extraction fluxes (*Q_well*) are read                                                                    |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              | *values*:                                                                                                                  |
  |      |                                                                          |                                              |                                                                                                                            |
  |      |                                                                          |                                              |    - ``CONSTANTQ``: total *Q_well* is uniformly distributed along the well screen                                          |
  |      |                                                                          |                                              |    - ``WELL_PACKAGE``: *Q_well* is cell-by-cell defined in a external file following Modflow's *well* package              |
  |      |                                                                          |                                              |    - ``MNW2_PACKAGE``: *Q_well* is cell-by-cell defined in a external file following Modflow's *mnw2* package              |
  |      |                                                                          |                                              |                                                                                                                            |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | - if ``Qwell_method`` = ``CONSTANTQ``:                                                                                                                                                                                                                      |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | ... to be repeated ``n_well`` times:                                                                                                                                                                                                                        |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | 8... | ``Qw``                                                                   | ``real``                                     | total flux extracted by the given well                                                                                     |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | - if ``Qwell_method`` = ``WELL_PACKAGE`` or ``MNW2_PACKAGE``:                                                                                                                                                                                               |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+
  | 8    | ``filename``                                                             | ``string``                                   | name of the file following the Modflow's package                                                                           |
  +------+--------------------------------------------------------------------------+----------------------------------------------+----------------------------------------------------------------------------------------------------------------------------+



.. _Control plane:

Control plane
""""""""""

.. _tbl-grid:

  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type                          | Description                                                                                                           |
  +======+=========================================================================+===============================+=======================================================================================================================+
  | 5    | ``n_plane``                                                             | ``integer``                   | number of control planes                                                                                              |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | There are 2 options to define the control planes:                                                                                                                                                                                      |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | - option 1, to be repeated ``n_planes`` times:                                                                                                                                                                                         |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | 6    | ``dist`` ``type`` ``partOUT``                                           | ``string``                    | ``dist``: distance of the control plane with respect to the x,y or z coordinate axis                                  |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | ``type``: type of control plane                                                                                       |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | *values*:                                                                                                             |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               |    - ``XX``: plane parallel to the x coordinate                                                                       |
  |      |                                                                         |                               |    - ``YY``: plane parallel to the y coordinate                                                                       |
  |      |                                                                         |                               |    - ``ZZ``: plane parallel to the z coordinate                                                                       |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | ``partOUT``: True (T) if particles reaching the observation location are killed                                       |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | - option 2, to be repeated ``n_planes`` times:                                                                                                                                                                                         |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | 6    | ``A`` ``B`` ``C`` ``D`` ``partOUT``                                     | ``string`` (x4) ``logical``   | ``A``, ``B``, ``C``, ``D``: parameters of the equation defining a plane as: :math:`A x + B y + C z + D = 0`           |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | ``partOUT``: True (T) if particles reaching the observation location are killed                                       |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+



.. _Registration lense:

Registration lense
""""""""""

.. table::

    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    |Line  | Variable                                                                | Type                                                | Description                                                                                                                      |
    +======+=========================================================================+=====================================================+==================================================================================================================================+
    | 6    | ``n_reg``                                                               | ``integer``                                         | number of registration lenses                                                                                                    |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 7    | ``nx_reg`` ``ny_reg``                                                   | ``integer``                                         | ``nx_reg``: number of cell in the *x* direction (i.e., columns)                                                                  |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``ny_reg``: number of cell in the *y* direction (i.e., rows)                                                                     |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 8    | ``dx_reg``                                                              | ``array``                                           | ``dx_reg``: cell size in the *x* direction                                                                                       |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 9    | ``dy_reg``                                                              | ``array``                                           | ``dy_reg``: cell size in the *y* direction                                                                                       |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | to be repeated ``n_reg`` times:                                                                                                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 10   | ``regcode`` ``partOUT`` ``saveBTC`` ``horizontal_extent_flag``          | ``integer`` ``integer`` ``logical`` ``logical``     | ``regcode``: index of the registration lense (arrivals to lenses with the same ``regcode`` will be saved in a single BTC)        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``partOUT``: True (T) if particles reaching the observation location are killed                                                  |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``saveBTC``: True (T) if BTCs are saved in a file                                                                                |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``horizontal_extent_flag``: True (T) if the lense extent horizontally over a given area that is defined in a file                |
    |      |                                                                         |                                                     |                                                                                                                                  |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 11   | ``bottom_reg``                                                          | ``array, 1 option``                                 | depth or elevation of the bottom of the registration lense                                                                       |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *option*: relative to topography                                                                                                 |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``logical``: ``T`` depth relative to topography, ``F`` actual elevation                                                     |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 12   | ``top_reg``                                                             | ``array, 1 option``                                 | depth or elevation of the top of the registration lense                                                                          |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *option*: relative to topography                                                                                                 |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``logical``: ``T`` depth relative to topography, ``F`` actual elevation                                                     |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | ... if ``horizontal_extent_flag``=``T``:                                                                                                                                                                                                                                |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 13   | ``horizontal_extent``                                                   | ``array``                                           | binary array defining the horizontal extent of the registration lense                                                            |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+


.. _Injection:

Injection
~~~~~~~~~~

.. table::

    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    |Line  | Variable                                                                | Type                                                | Description                                                                                                                      |
    +======+=========================================================================+=====================================================+==================================================================================================================================+
    | 7    | ``n_inj``                                                               | ``integer``                                         | number of injections                                                                                                             |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | to be repeated ``n_inj`` times:                                                                                                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 8    | ``name_inj`` ``type_inj``                                               | ``integer``                                         | ``name_inj``: name of the injection                                                                                              |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *values*:                                                                                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``point``: injection at a give x,y,z location                                                                               |
    |      |                                                                         |                                                     |    - ``vertical_line``                                                                                                           |
    |      |                                                                         |                                                     |    - ``vertical_line_flux_weighted``                                                                                             |
    |      |                                                                         |                                                     |    - ``line_by_points``                                                                                                          |
    |      |                                                                         |                                                     |    - ``line_by_points_random``                                                                                                   |
    |      |                                                                         |                                                     |    - ``line_flux_weighted``                                                                                                      |
    |      |                                                                         |                                                     |    - ``horizontal_line_flux_weighted``                                                                                           |
    |      |                                                                         |                                                     |    - ``block``                                                                                                                   |
    |      |                                                                         |                                                     |    - ``circle``                                                                                                                  |
    |      |                                                                         |                                                     |    - ``radial``                                                                                                                  |
    |      |                                                                         |                                                     |    - ``plane``                                                                                                                   |
    |      |                                                                         |                                                     |    - ``plane_random``                                                                                                            |
    |      |                                                                         |                                                     |    - ``layer``                                                                                                                   |
    |      |                                                                         |                                                     |    - ``vertical_block_flux_weighted``                                                                                            |
    |      |                                                                         |                                                     |    - ``cell_file_flux_weighted``                                                                                                 |
    |      |                                                                         |                                                     |    - ``cell_file_flux_inv_weighted``                                                                                             |
    |      |                                                                         |                                                     |    - ``read_particle_file``: reads the initial coordinates of all injected particles from a text file                            |
    |      |                                                                         |                                                     |    - ``read_concentration_file``: reads the cell-by-cell concentration from a text file                                          |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``type_inj``: type of the injection                                                                                              |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *values*:                                                                                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``dirac``: pulse injection at a given time                                                                                  |
    |      |                                                                         |                                                     |    - ``general``: transient mass flux to be specified in a file containing a *time function*                                     |
    |      |                                                                         |                                                     |    - ``constant_concentration``: only available for a ``block`` injection                                                        |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | ... if ``name_inj``≠``read_particle_file``:                                                                                                                                                                                                                             |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 9    | ``mp`` ``zone`` ``specie``                                              | ``real`` ``integer`` ``integer``                    | ``mp``: mass of a single particle                                                                                                |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``zone``: zone where particles are injected                                                                                      |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *values*:                                                                                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``0``: injection in the mobile domain                                                                                       |
    |      |                                                                         |                                                     |    - ``1``: injection in the immobile domain                                                                                     |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``specie``: specie index of the injected particles                                                                               |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 10   | ``name_inj`` specific input, described in the following table           |                                                     |                                                                                                                                  |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | ... if ``type_inj``=``dirac``:                                                                                                                                                                                                                                          |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 11   | ``t_inj``                                                               | ``real``                                            | ``t_inj``: time of the particle injection                                                                                        |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | ... if ``type_inj``=``general``:                                                                                                                                                                                                                                        |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 11   | ``inj_time_function`` ``const`` ``freq``                                | ``time_function`` ``real`` ``integer``              | ``inj_time_function``: file with the *time function*                                                                             |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``const``: multiplier to apply to the mass flux specified in the *time function*                                                 |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``freq``: frequency for consideration of the mass flux time function                                                             |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | ... if ``type_inj``=``constant_concentration``:                                                                                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 11   | ``t_inj`` ``conc``                                                      | ``real`` ``real``                                   | ``t_inj``: time of the start of the injection                                                                                    |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``conc``: fixed concentration                                                                                                    |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+


.. table:: Parameter of each type of injection

    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``name_inj``                                                            | Parameters                                                                   | Description                                                                                                       |
    +=========================================================================+==============================================================================+===================================================================================================================+
    | ``block``                                                               | ``i0`` ``j0`` ``k0`` ``i1`` ``j1`` ``k1``                                    | - ``idwn``: cell index                                                                                                       |
    |                                                                         |                                                                              | - ``jdwn``:                                                                                                       |
    |                                                                         |                                                                              | - ``kdwn``:                                                                                                       |
    |                                                                         |                                                                              | - ``iup``:                                                                                                        |
    |                                                                         |                                                                              | - ``jup``:                                                                                                        |
    |                                                                         |                                                                              | - ``kup``:                                                                                                        |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``point``                                                               | ``xinj`` ``yinj`` ``zinj``                                                   | - ``xinj``: x-coordinate                                                                                          |
    |                                                                         |                                                                              | - ``yinj``: y-coordinate                                                                                          |
    |                                                                         |                                                                              | - ``zinj``: z-coordinate                                                                                          |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``line``                                                                | ``xinj`` ``yinj`` ``zbot`` ``ztop``                                          | - ``xinj``:                                                                                                       |
    |                                                                         |                                                                              | - ``yinj``:                                                                                                       |
    |                                                                         |                                                                              | - ``zbot``:                                                                                                       |
    |                                                                         |                                                                              | - ``ztop``:                                                                                                       |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``circle``                                                              | ``xinj`` ``yinj`` ``zbot`` ``ztop`` ``rcyr``                                 |                                                                                                                   |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``radial``                                                              | same as ``circle``                                                           |                                                                                                                   |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+

..
    block:                                      idwn,jdwn,kdwn,iup,jup,kup
    point:                                      xinj,yinj,zinj
    line:                                       xinj,yinj,zbot,ztop
    circle or radial:                           xinj,yinj,zbot,ztop,rcyr
    plane or plane_random:                      xdist,width,height
    line_by_points or line_by_points_random:    xinj_1,yinj_1,zinj_1,xinj_2,yinj_2,zinj_2
    read_particle_file:                         file
    read_concentration_file:                    file, const
    line_flux_weighted:                         xinj_1,yinj_1,zinj_1,xinj_2,yinj_2,zinj_2
    vertical_line_flux_weighted:                i,j,kdwn,kup
    horizontal_line_flux_weighted:              idwn,iup,j,k
    vertical_block_flux_weighted:               i,j,kdwn,kup    looks the same than vertical_line_flux_weighted
    cell_file_flux_weighted:                    file
    cell_file_flux_inv_weighted:                file
    layer:                                      lay_inj,np_cell,lay_loc



.. _Well recirculation:

Well recirculation
~~~~~~~~~~

.. table::

    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    |Line  | Variable                                                                | Type                                                | Description                                                                                                                      |
    +======+=========================================================================+=====================================================+==================================================================================================================================+
    | 4    | ``recirculation_action``                                                | ``logical``                                         | True if the package is activated                                                                                                 |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 5    | ``n_connection``                                                        | ``integer``                                         | number of connections between extraction/recirculation wells                                                                     |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | to be repeated ``n_connection`` times:                                                                                                                                                                                                                                  |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 6    | ``connection_string``                                                   | ``string``                                          | string describing a connection, i.e., a transfer of particles between 2 sets of well                                             |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *instructions*:                                                                                                                  |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - following the form: [``wellID1`` AND ``wellID2`` AND ...]  --> [``wellID3`` AND ``wellID4`` AND ...]                        |
    |      |                                                                         |                                                     |    - well names in a set of well separated by the word `AND`                                                                     |
    |      |                                                                         |                                                     |    - transfer between sets of wells separeted by an arrow (``-->``)                                                              |
    |      |                                                                         |                                                     |    - The name of the wells must follow the names specified in :ref:`Extraction well`                                             |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
    | 7    | ``connection_time_function``                                            | ``time_function``                                   | *time function* specifying at which periods the connection is active or not.                                                     |
    |      |                                                                         |                                                     | Active: :math: `$\gt$ 0` ; Inactive: :math:`$\leqslant$ 0`                                                                       |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+
