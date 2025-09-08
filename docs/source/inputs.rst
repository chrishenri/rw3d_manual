.. _inputs:

Input Instructions
===============

To run RW3D, a single **parameter file** has to be provided.


Type of inputs
------------

In the *Parameter file*, input parameters can be specified following these different formats: 

- ``logical``: ``T`` for True; ``F`` for False
- ``string``
- ``integer``
- ``real``
- ``time_function``: A text file describing the temporal evolution of a non-array parameter. 
- ``array``: The parameter is potentially spatially and/or temporally variable and can be read from a file. The following information have to be provided in a single line: ``file name`` ``multiplier`` ``ivar`` ``flag`` (see specifications in the Table below). 
  In some specific cases, one or two additional parameters (*options*) must also be provided. 

.. 
  .. only:: html
..
    .. list-table:: Variable Descriptions
        :widths: 20 20 60
        :header-rows: 1

        * - Variable
          - Type
          - Description
        * - ``file name``
          - ``string``
          - name of the file. Put some text even if no file is used
        * - ``multiplier``
          - ``real``
          - Fixed parameter values (for ``flag``=0), or multiplier of the variable
        * - ``ivar``
          - ``integer``
          - variable index of the variable in the gslib array
        * - ``flag``
          - ``integer``
          - way to read the values of the parameter:
            
            - 0: spatially and temporally constant parameter, defined as the multiplier 
            - 1: read from the ascii file specified in ``file name`` 
            - 2: read from a MODFLOW type file (only for fluxes) 
            - 3: read from a DFS file 
            - 4: read from a NetCDF file 


  .. only:: latex
..
    \usepackage[table]{xcolor}
    \usepackage{booktabs}
    \rowcolors{2}{gray!10}{white}

    \begin{table}[H]
    \centering
    \renewcommand{\arraystretch}{1.3}
    \begin{tabular}{p{3.5cm} p{2.5cm} p{9cm}}
    \toprule
    \textbf{Variable} & \textbf{Type} & \textbf{Description} \\
    \midrule
    \texttt{file name} & \texttt{string} & name of the file. Put some text even if no file is used \\
    \texttt{multiplier} & \texttt{real} & Fixed parameter values (for \texttt{flag}=0), or multiplier of the variable \\
    \texttt{ivar} & \texttt{integer} & variable index of the variable in the gslib array \\
    \texttt{flag} & \texttt{integer} & way to read the values of the parameter:

    \begin{itemize}
      \item 0: not read from a file, defined as the multiplier
      \item 1: read from the ascii file specified in \texttt{file name}
      \item 2: read from a MODFLOW type file (only for fluxes)
      \item 3: read from a DFS file
      \item 4: read from a NetCDF file
    \end{itemize} \\
    \bottomrule
    \end{tabular}
    \end{table}


.. container::
   :name: table-variable

    .. list-table:: Required inputs for an Array-type parameter
        :widths: 20 20 60
        :header-rows: 1

        * - Variable
          - Type
          - Description
        * - ``file name``
          - ``string``
          - name of the file. Put some text even if no file is used
        * - ``multiplier``
          - ``real``
          - Fixed parameter values (for ``flag`` = 0), or multiplier of the variable
        * - ``ivar``
          - ``integer``
          - variable index of the variable in the gslib array
        * - ``flag``
          - ``integer``
          - way to read the values of the parameter:
            
            - 0: spatially and temporally constant parameter, defined as the multiplier 
            - 1: read from the ascii file specified in ``file name`` 
            - 2: read from a MODFLOW type file (only for fluxes) 
            - 3: read from a DFS file 
            - 4: read from a NetCDF file 



File format for a parameter of type ``array``
~~~~~~~~~~

A parameter of type ``array`` can be read from a file (with name ``file name``) using a ``flag`` integer other than 0. 
The file can be an **text** file, a **DFS** file, or a **NetCDF** file. 

- **Text file**

A text file (``flag`` set to *1*) must follow the following format: 

.. container::
   :name: table-array

   .. table:: Ascii file format
 
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

To read a parameter from a text file, use the usual inputs for an ``array`` parameter: ``file name`` ``multiplier`` ``ivar`` ``flag``: 

  - ``file name`` (``string``) is the name of the DFS file; 
  - ``multiplier`` (``real``) is a number multiplying all values; 
  - ``ivar`` (``integer``) is the column index to be used (set to 1 for a unique column of values in the text file); 
  - ``flag`` (``integer``) is set to *1*.


- **DFS file**

RW3D supports reading input data from **DFS** files (``flag`` set to *3*). 
The **DFS (Data File System)** is a binary data file format typically used in the MIKE Powered by DHI software. 

For an extensive description of what DFS files are, follow the link https://docs.mikepoweredbydhi.com/core_libraries/dfs/dfs-file-system/

To read a parameter from a DFS file, use the usual inputs for an ``array`` parameter: ``file name`` ``multiplier`` ``ivar`` ``flag``: 

  - ``file name`` (``string``) is the name of the DFS file; 
  - ``multiplier`` (``real``) is a number multiplying all values; 
  - ``ivar`` (``integer``) is the item index to be used (set to 1 for a unique item in the DFS file); 
  - ``flag`` (``integer``) is set to *3*.


- **NetCDF file**

RW3D supports reading input data from **NetCDF** files (``flag`` set to *4*). 
**NetCDF (Network Common Data Form)** is a widely used, self-describing binary file format designed for storing array-oriented scientific data. 
For more information on the NetCDF format, see the official documentation: https://www.unidata.ucar.edu/software/netcdf/

The NetCDF file must follow a specific format. It must contain **4 dimensions** (*t, x, y, z*) that fits the temporal and spatial discretizations of the model, even for time-invariant and 1D/2D parameters. 
Also, the NetCDF dataset must contain only one variable (i.e., the parameter to be specified). The name of the variable that the code read and use is recalled in the log file. 

Note that the NetCDF format of the file will have to follow the specifications of your instalation of the NetCDF library. 

To read a parameter from a NetCDF file, use the usual inputs for an ``array`` parameter: ``file name`` ``multiplier`` ``ivar`` ``flag``:

  - ``file name`` (``string``) is the name of the NetCDF file; 
  - ``multiplier`` (``real``) is a number multiplying all values; 
  - ``ivar`` (``integer``) has to be specified but will not be used; 
  - ``flag`` (``integer``) is set to *4*.


File format for *time function*
~~~~~~~~~~

This file is a plain text file used to define a time-dependent function. 
It provides a sequence of time-value pairs that describe how a quantity evolves over time. The file is read when `flag == 1`.

**File Structure**

The file must follow this structure:

1. *Heading line* (ignored by the subroutine).
2. *Name line*: a string that will be stored in `func%name`.
3. *Number of time points*: an integer value `nt`.
4. *Time-function data*: `nt` lines, each containing:
   - A real number representing the time value.
   - A real number representing the corresponding function value.

Ensure that the number of time-value pairs matches the integer specified in line 3.

**Example**

.. code-block:: text

   # Time function data for simulation
   MyTimeFunction
   4
   0.0  1.0
   1.0  2.0
   2.0  1.5
   3.0  0.5


Parameter file
------------

The parameter file consists in a text file. The following blocks of information has to be sequentially provided. 

- :ref:`General setup`
- :ref:`Species and Phases`
- :ref:`Time`
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
- :ref:`Outputs`

.. warning::
    Note that 3 header lines has to be written before each block. 


.. _General setup:

General setup
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``path_outputs``                                                        | ``string``         | ``path_outputs``: path to the output files                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``base_outputs``                                                        | ``string``         | ``base_outputs``: base name for all output files                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 4    | ``idebug``                                                              | ``integer``        | ``idebug``: Integer defining degree of debugging as written in rw3d_general.dbg        |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *values*:                                                                              |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |         - -1: Do not write the velocity field                                          |
  |      |                                                                         |                    |         - 0: Normal Run                                                                |
  |      |                                                                         |                    |         - 10: Maximum Debugging Degree                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

.. note::
    The line number in each table is reset for each block to simplify the description of the inputs. Each block is to be filled up sequentially, so the *absolute* line number will be different. 

.. _Species and Phases:

Species and Phases
~~~~~~~~~~

.. _tbl-grid:  

  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``nspe_aq`` ``nspe_min``                                                | ``integer``        | ``nspe_aq``: number of aqueous (i.e., mobile) species                                  |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | ``nspe_min``: number of aqueous (i.e., immobile) species                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``name_aq``                                                             | ``string``         | ``name_aq``: name(s) of aqueous (i.e., mobile) species                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``name_min``                                                            | ``string``         | ``name_min``: name(s) of aqueous (i.e., immobile) species                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


.. _Time:

Time
~~~~~~~~~~

.. _tbl-grid:  

  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``t_sim``                                                               | ``real``           | ``t_sim``: simulation time                                                             |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``transient_flag``                                                      | ``logical``        | ``transient_flag``: True if transient conditions                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``transient_flag`` == ``F``, go to :ref:`Geometry`; if ``transient_flag`` == ``T``, fill up the following:                                                                                |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``read_dt_from_file``  ``loop_dt``                                      | ``logical``        | ``read_dt_from_file``: True if the time steps are read from a text file                |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``read_dt_from_file`` == ``T``:                                                                                                                                                           |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``dt_file``                                                             | ``string``         | ``dt_file``: name of the text file listing the time steps                              |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | if ``read_dt_from_file`` == ``T``, go to :ref:`Geometry`; if ``read_dt_from_file`` == ``F``:                                                                                                 |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 8    | ``n_dt``                                                                | ``integer``        | ``n_dt``: number of time steps                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | to be repeated :math:`n_{dt}` times:                                                                                                                                                         |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 9 ...| ``dt``                                                                  | ``real``           | ``dt``: time step                                                                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


**Example**: A problem involving 2 aqueous chemical species (named *A* and *B*) and 0 mineral species. 
The simulation will run for 150.0 time units with transient parameters. 
The temporal discretization of the transient parameters is specified in the file *time_discretization.dat* and the transient paramters are set to be looped in time until the end of the simulation. 
The first first blocs of the input file would look like that: 

::

   -----------------------------------------------------------------
    General Setup
   -----------------------------------------------------------------
   C:\Path\To\Ouputs                   !... path_outputs
   test_case                           !... basename_outputs
   0                                   !idebug
   -----------------------------------------------------------------
    Species and Phases
   -----------------------------------------------------------------
   2   0                               !nspe_aq; nspe_min
   A   B                               !name_aq
   -                                   !name_min
   -----------------------------------------------------------------
    Time
   -----------------------------------------------------------------
   150.0                               !t_sim
   T                                   !transient_flag
   T   T                               !read_dt_from_file; loop_dt
   time_discretization.dat             !dt_file


.. _Geometry:

Geometry
~~~~~~~~~~

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
  | 8    | ``topography``                                                          | ``array``          | ``topography``: topography elevation                                                   |
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
  | 8    | ``write_vtu``                                                           | ``logical``        | ``T`` Write the grid, (in)active cells and topography in a vtu file, ``F`` otherwise   |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+


**Example**: The domain is discretized in 1200 cells in the *x*-direction, 1400 cells in the *y*-direction and 11 cells in the *z*-direction. 
The cell size in *x* and *y* is fixed to 100 space units. The cell size in the *z*-direction is variable in space and specified in the first column of the text file *dz.dat*. 
The top elevation of the domain (topography) is also variable in space and specified in the text file *topography.dat*. 
The location of inactive cells is provided in the text file *InactCell.dat* and particles reaching an inactive cell will be killed. 
No multipliers are to be used (all set to 1.0). 
Finally, particles reaching the boundary of the domain will be killed, expect at the top of the domain, where particles will bounce.  
We would also like to produce a vtu file to visualize the grid, the active/innactive cells and the topography elevation in Paraview. 

::

   ---------------------------------------------------------------
    Geometry
   ---------------------------------------------------------------
   1200    1400    11                               !nx; ny; nz
   not_used             100.0    1    0             !dx
   not_used             100.0    1    0             !dy
   dz.dat               1.0      1    1    F        !dz
   topography.dat       1.0      1    1             !topography
   InactCell.dat        1.0      1    1    T        !inactive_cell
   0   0   0   0   0   1                            !ib(1,1); ib(1,2); ib(2,1); ib(2,2); ib(3,1); ib(3,2)
   T                                                !write_vtu


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
  |      |                                                                         |                    |    - ``constant_move``                                                                 |
  |      |                                                                         |                    |    - ``optimum_dt``                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``dt`` ``mult_adv`` ``mult_disp`` ``mult_kf`` ``mult_kd`` ``mult_mt``   | ``real``           | Time step restrictors, as defined in section :ref:`Time discretization process`        |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``dt_relax``                                                            | ``real``           | Time step relaxation factor, as defined in section :ref:`Time discretization process`  |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

**Example**: The time step is determined from the advective characteristic times only. The time step restrictors have to be provided, but only ``mult_adv`` will be used. It is fixed to 0.5.  
The time step relaxation factor is set to 0.99, meaning that the 1% more restrictive characteristic times (1% fastest particles) will be disregarded in the time step determination. 

::

   -----------------------------------------------------------------
    Time discretization
   -----------------------------------------------------------------
   constant_move                                           !... dt_method
   1.0  0.5  0.2  0.1  0.1  0.1                            !... dt, mult_adv, mult_disp, mult_kf, mult_kd, mult_mt
   0.99                                                    !... time step relaxation


.. _Advection:

Advection
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                                                         |
  +======+=========================================================================+====================+=====================================================================================================================+
  | 4    | ``advection_action``                                                    | ``logical``        | True if the package is activated                                                                                    |
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
  | 5    | ``advection_method``                                                    | ``logical``        | Method for advective motion of particles, as defined in :ref:`Advective motion`                                     |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *values*:                                                                                                           |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``exponential``                                                                                                |
  |      |                                                                         |                    |    - ``eulerian``                                                                                                   |
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
  | 6    | ``q_x``                                                                 | ``array, 2 option``| flow/flux in the *x* direction                                                                                      |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 1*: transient conditions                                                                                    |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                                                   |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 2*: flow to flux                                                                                            |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` if flows are provided and need to be converted into fluxes, ``F`` if fluxes are provided    |
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
  | 7    | ``q_y``                                                                 | ``array, 2 option``| flow/flux in the *y* direction                                                                                      |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 1*: transient conditions                                                                                    |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                                                   |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 2*: flow to flux                                                                                            |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` if flows are provided and need to be converted into fluxes, ``F`` if fluxes are provided    |
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
  | 8    | ``q_z``                                                                 | ``array, 2 option``| flow/flux in the *z* direction                                                                                      |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 1*: transient conditions                                                                                    |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                                                   |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 2*: flow to flux                                                                                            |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` if flows are provided and need to be converted into fluxes, ``F`` if fluxes are provided    |
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+
  | 9    | ``porosity``                                                            | ``array, 1 option``| porosity (or water content)                                                                                         |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    | *option 1*: transient conditions                                                                                    |
  |      |                                                                         |                    |                                                                                                                     |
  |      |                                                                         |                    |    - ``logical``: ``T`` if flows are provided and need to be converted into fluxes, ``F`` if fluxes are provided    |
  +------+-------------------------------------------------------------------------+--------------------+---------------------------------------------------------------------------------------------------------------------+

**Example**: Advective displacements are simulated. The Eulerian scheme is used to interpolate velocities. 
Darcy flows in x, and y directions are provided in a respective *netcdf* file. Flows will then have to be converted into fluxes within RW3D.
The fluxes in z are directly provided, also in a *netcdf* file. All flows and fluxes are transient. The fluxes in z are to be multiplied by a factor of 2.0. 
The porosity field does not change in time and its spatial distribution is defined in a text file. 

::

   -----------------------------------------------------------------
    Advection
   -----------------------------------------------------------------
   T                                                                              !... advection_action
   Eulerian                                                                       !... advection_method
   qx_DK1.nc                            1.0   1   4   T   T                       !... qx array
   qy_DK1.nc                            1.0   1   4   T   T                       !... qy array
   qz_DK1.nc                            2.0   1   4   T   T                       !... qz array
   porosity_DK1.dat                     1.0   1   1   F                           !... porosity array


.. _Heads:

Heads
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``heads_action``                                                        | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``heads``                                                               | ``array, 1 option``| cell-by-cell head elevation                                                            |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``heads_threshold``                                                     | ``real``           | maximum head elevation for the cell to be considered dry                               |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

**Example**: Hydrualic heads are accounted for to track particles reaching the water table. 
Heads are provided in a netcdf file. A cell will be considered dry if heads are below 0.05 (space unit). 

::

   --------------------------------------------------------------------------------------------
    Heads
   --------------------------------------------------------------------------------------------
   T                                                                              !... heads_action
   heads_DK1.nc                        1.0   1    4   F                           !... heads
   0.05                                                                           !... heads_threshold


.. _Sinks:


Sinks
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+------------------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type                         | Description                                                                            |
  +======+=========================================================================+==============================+========================================================================================+
  | 4    | ``sinks_action``                                                        | ``logical``                  | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+------------------------------+----------------------------------------------------------------------------------------+
  | 5    | ``n_sinks``                                                             | ``integer``                  | number of sink                                                                         |
  +------+-------------------------------------------------------------------------+------------------------------+----------------------------------------------------------------------------------------+
  | to be repeated :math:`n_{sinks}` times:                                                                                                                                                                |
  +------+-------------------------------------------------------------------------+------------------------------+----------------------------------------------------------------------------------------+
  | 6... | ``sink_name`` ``Q_sink``                                                |``string`` ``array, 2 option``| ``sink_name``: name of the sink                                                        |
  |      |                                                                         |                              |                                                                                        |
  |      |                                                                         |                              | ``Q_sink``: flow going into the sink (:math:`L^3/T`)                                   |
  |      |                                                                         |                              |                                                                                        |
  |      |                                                                         |                              | *option 1*: transient conditions                                                       |
  |      |                                                                         |                              |                                                                                        |
  |      |                                                                         |                              |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  |      |                                                                         |                              |                                                                                        |
  |      |                                                                         |                              | *option 2*: print_BTC                                                                  |
  |      |                                                                         |                              |                                                                                        |
  |      |                                                                         |                              |    - ``logical``: ``T`` BTC is printed, ``F`` BTC not printed                          |
  +------+-------------------------------------------------------------------------+------------------------------+----------------------------------------------------------------------------------------+


**Example**: 4 types of cell sinks are considered: river, drain, uz, well. All sinks are read from a respective a netcdf file. 
Breakthrough curves for all sinks will be saved, expect for the *well* sink. All sink fluxes are temporally variable. 


::

   --------------------------------------------------------------------------------------------
    Sinks
   --------------------------------------------------------------------------------------------
   T                                                                              !... sink_action
   4                                                                              !... number of sink
   river     Qriver_DK1.nc          1.0   1   4   T   T                           !... name, qsink array
   drain     Qdrain_DK1.nc          1.0   1   4   T   T                           !... name, qsink array
   uz        Q_uz_DK1.nc            1.0   1   4   T   T                           !... name, qsink array
   well      Qwell_DK1.nc           1.0   1   4   T   F                           !... name, qsink array


.. _Diffusion / Dispersion:

Dispersion / Diffusion
~~~~~~~~~~

.. _tbl-grid:
  
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  |Line  | Variable                                                                | Type               | Description                                                                            |
  +======+=========================================================================+====================+========================================================================================+
  | 4    | ``dispersion_action``                                                   | ``logical``        | True if the package is activated                                                       |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 5    | ``dispersivity_L``                                                      | ``array, 1 option``| dispersivity in the longitudinal direction                                             |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 6    | ``dispersivity_TH``                                                     | ``array, 1 option``| dispersivity in the transverse horizontal direction                                    |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
  +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
  | 7    | ``dispersivity_TV``                                                     | ``array, 1 option``| dispersivity in the transverse vertical direction                                      |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    | *option*: transient conditions                                                         |
  |      |                                                                         |                    |                                                                                        |
  |      |                                                                         |                    |    - ``logical``: ``T`` transient field, ``F`` steady-state field                      |
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


**Example**: Dispersion and diffusion processes are simulated. All parameters are considered spatially homogeneous. 
Longitudinal, transverse horizonal, and transverse vertical dispersivities are set to 5.0, 5.0 and 0.1, respectively. 
Diffusion coefficients in all directions are set to 0.01. Two single aqueous species were considered. 
Diffusion coefficients for the second specie are two times larger than the set values (*diffusion_factor* set to 2.0). 
Set dispervities and diffusion coefficients are used otherwise (*dispersivity_factor* and *diffusion_factor* set to 1.0). 


::

   --------------------------------------------------------------------------------------------
    Dispersion / diffusion
   --------------------------------------------------------------------------------------------
   T                                                                              !... dispersion_action
   not_used                             5.0   1   0                               !... alpha_L array
   not_used                             5.0   1   0                               !... alpha_TH array
   not_used                             0.1   1   0                               !... alpha_TV array
   not_used                             0.01   1   0   F                          !... Dm_L array
   not_used                             0.01   1   0   F                          !... Dm_TH array
   not_used                             0.01   1   0   F                          !... Dm_TV array
   1.0   1.0                                                                      !... mult_alpha
   1.0   2.0                                                                      !... mult_diff


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
  | 6    | ``dist`` ``type`` ``partOUT`` ``SaveBTC``                               | ``string``                    | ``dist``: distance of the control plane with respect to the x,y or z coordinate axis                                  |
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
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | ``SaveBTC``:  True (T) if breakthrough curves are saved and printed                                                   |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | - option 2, to be repeated ``n_planes`` times:                                                                                                                                                                                         |
  +------+-------------------------------------------------------------------------+-------------------------------+-----------------------------------------------------------------------------------------------------------------------+
  | 6    | ``A`` ``B`` ``C`` ``D`` ``partOUT`` ``SaveBTC``                         | ``string`` (x4) ``logical``   | ``A``, ``B``, ``C``, ``D``: parameters of the equation defining a plane as: :math:`A x + B y + C z + D = 0`           |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | ``partOUT``: True (T) if particles reaching the observation location are killed                                       |
  |      |                                                                         |                               |                                                                                                                       |
  |      |                                                                         |                               | ``SaveBTC``:  True (T) if breakthrough curves are saved and printed                                                   |
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
    | 8    | ``name_inj`` ``mode_inj`` ``type_inj`` ``keep_sat``                     | ``string`` ``string`` ``string`` ``logical``        | ``name_inj``: name of the injection                                                                                              |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *values*:                                                                                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``point``: injection at a give x,y,z location                                                                               |
    |      |                                                                         |                                                     |    - ``line``: injection in a line defined by 2 points coordinates                                                               |
    |      |                                                                         |                                                     |    - ``layer``: injection over a layer, at a given elevation in the layer; horizontal extent can be specified                    |
    |      |                                                                         |                                                     |    - ``block``: injection over a continuous block of cells                                                                       |
    |      |                                                                         |                                                     |    - ``yz_plane``: injection over a yz plane, specifying a x coordinate of the plane; the plane is centrally located in y and z  |
    |      |                                                                         |                                                     |    - ``vertical_cylinder``: injection in a vertical (z-oriented) cylinder; particles are injected all over the cylinder          |
    |      |                                                                         |                                                     |    - ``vertical_cylinder_hollow``: injection over the circonference of a vertical (z-oriented) cylinder                          |
    |      |                                                                         |                                                     |    - ``read_cells_files``: reads cell indices where to injected particles from a text file                                       |
    |      |                                                                         |                                                     |    - ``read_particles_file``: reads the initial coordinates of all injected particles from a text file                           |
    |      |                                                                         |                                                     |    - ``read_concentration_file``: reads the cell-by-cell concentration from a text file                                          |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``mode_inj``: type of the injection                                                                                              |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *values*:                                                                                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``random``: particles are randomly ditributed                                                                               |
    |      |                                                                         |                                                     |    - ``uniform``: particles are uniformly ditributed                                                                             |
    |      |                                                                         |                                                     |    - ``flux_weighted``: particles are injected in a flux-weighted fashion (particle density proportional to fluxes)              |
    |      |                                                                         |                                                     |    - ``inverse_flux_weighted``: particles are injected in an inverse flux-weighted fashion                                       |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``type_inj``: type of the injection                                                                                              |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | *values*:                                                                                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     |    - ``dirac``: pulse injection at a given time                                                                                  |
    |      |                                                                         |                                                     |    - ``general``: transient mass flux to be specified in a file containing a *time function*                                     |
    |      |                                                                         |                                                     |    - ``constant_concentration``: only available for a ``block`` injection                                                        |
    |      |                                                                         |                                                     |                                                                                                                                  |
    |      |                                                                         |                                                     | ``keep_sat``: True (T) if particles with elevation higher than the local head are to be moved to the head elevation              |
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
    | 10   | *injection parameters line*;  see Table `Injection_parameters`_         |                                                     | inputs specific to the injection, described in the following table                                                               |
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


.. _Injection_parameters:

.. table:: Parameters to be defined for each type of injection

    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``name_inj``                                                            | Parameters                                                                   | Description                                                                                                       |
    +=========================================================================+==============================================================================+===================================================================================================================+
    | ``point``                                                               | ``x`` ``y`` ``z``                                                            | - ``x``: x-coordinate of the injection point                                                                      |
    |                                                                         |                                                                              | - ``y``: y-coordinate of the injection point                                                                      |
    |                                                                         |                                                                              | - ``z``: z-coordinate of the injection point                                                                      |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``line``                                                                | ``x1`` ``x2`` ``y1`` ``y2`` ``z1`` ``z2``                                    | - ``x1``: left coordinate of the injection line                                                                   |
    |                                                                         |                                                                              | - ``x2``: right coordinate of the injection line                                                                  |
    |                                                                         |                                                                              | - ``y1``: front coordinate of the injection line                                                                  |
    |                                                                         |                                                                              | - ``y2``: back coordinate of the injection line                                                                   |
    |                                                                         |                                                                              | - ``z1``: bottom coordinate of the injection line                                                                 |
    |                                                                         |                                                                              | - ``z2``: top coordinate of the injection line                                                                    |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``layer``                                                               | ``lay_inj`` ``np_cell`` ``lay_loc`` ``horizontal_extent_flag``               | - ``lay_inj``: index of the layer where particles are injected                                                    |
    |                                                                         |                                                                              | - ``np_cell``: number of particles per cell                                                                       |
    |                                                                         |                                                                              | - ``lay_loc``: proportion of the cell (between 0 and 1) where particles are injected                              |
    |                                                                         |                                                                              | - ``horizontal_extent_flag``: logical flag; True (T) if an horizontal extent file is provided                     |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``block``                                                               | ``idwn`` ``jdwn`` ``kdwn`` ``iup`` ``jup`` ``kup``                           | - ``idwn``: lower left cell index of the injection block                                                          |
    |                                                                         |                                                                              | - ``jdwn``: lower front cell index of the injection block                                                         |
    |                                                                         |                                                                              | - ``kdwn``: bottom cell index of the injection block                                                              |
    |                                                                         |                                                                              | - ``iup``: upper right cell index of the injection block                                                          |
    |                                                                         |                                                                              | - ``jup``: upper back cell index of the injection block                                                           |
    |                                                                         |                                                                              | - ``kup``: upper cell index of the injection block                                                                |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``yz_plane``                                                            | ``xdist`` ``width`` ``height``                                               | - ``xdist``: x-coordinate of the vertical injection plane                                                         |
    |                                                                         |                                                                              | - ``width``: width (y-direction) of the plane (the plane will be centered over the domain's y-length)             |
    |                                                                         |                                                                              | - ``height``: height (z-direction) of the plane (the plane will be centered over the domain's z-length)           |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``vertical_cylinder`` or ``vertical_cylinder_hollow``                   | ``x`` ``y`` ``zbot`` ``ztop`` ``rcyr``                                       | - ``x``: x-coordinate of the center of the injection cylinder                                                     |
    |                                                                         |                                                                              | - ``y``: y-coordinate of the center of the injection cylinder                                                     |
    |                                                                         |                                                                              | - ``zbot``: z-coordinate of the bottom of the injection cylinder                                                  |
    |                                                                         |                                                                              | - ``ztop``: x-coordinate of the top of the injection cylinder                                                     |
    |                                                                         |                                                                              | - ``rcyr``: radius the injection cylinder                                                                         |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``read_cells_files``                                                    | ``file``                                                                     | - ``file``: path to the file where injection cells indices are specified                                          |
    |                                                                         |                                                                              |                                                                                                                   |
    |                                                                         |                                                                              | *file format*:                                                                                                    |
    |                                                                         |                                                                              |                                                                                                                   |
    |                                                                         |                                                                              |    - line 0: header line; not used                                                                                |
    |                                                                         |                                                                              |    - line 1: ``ncell``: number of cells                                                                           |
    |                                                                         |                                                                              |    - line 3-line ``ncell+1`: ix, iy, iz; cell indices (one cell per line)                                         |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+
    | ``read_particles_file``                                                 | ``file``                                                                     | - ``file``: path to the file where particles characteristics are specified                                        |
    |                                                                         |                                                                              |                                                                                                                   |
    |                                                                         |                                                                              | *file format*:                                                                                                    |
    |                                                                         |                                                                              |                                                                                                                   |
    |                                                                         |                                                                              |    - line 0: header line; not used                                                                                |
    |                                                                         |                                                                              |    - line 1: ``np``: number of particles                                                                          |
    |                                                                         |                                                                              |    - line 3-line ``np+1`: x, y, z, mp, izone, ispecie; particle coordinates, mass, zone and specie index          |
    +-------------------------------------------------------------------------+------------------------------------------------------------------------------+-------------------------------------------------------------------------------------------------------------------+


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
    |      |                                                                         |                                                     | Active: :math:`> 0` ; Inactive: :math:`\leqslant 0`                                                                              |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------+


.. _Ouputs:

Ouputs
~~~~~~~~~~

.. table::

    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    |Line  | Variable                                                                | Type                                                | Description                                                                                                                                                                                                                                        |
    +======+=========================================================================+=====================================================+====================================================================================================================================================================================================================================================+
    | 4    | ``ixmom`` ``format``                                                    | ``integer``                                         | ``1`` if print cartesian spatial moments at snapshots                                                                                                                                                                                              |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``format``: ``0`` for printing outputs in a csv file; ``1`` for printing in a binary file.                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 5    | ``iwcshot`` ``format``                                                  | ``integer``                                         | ``1`` if print particle cloud at snapshots                                                                                                                                                                                                         |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``format``: ``0`` for printing outputs in a csv file; ``1`` for printing in a binary file.                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | if ``ixmom`` = 0 and ``iwcshot`` = 0: the following line will still be read but not considered.                                                                                                                                                                                                                                                                                           |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | There are 3 options to specify the times of snapshots:                                                                                                                                                                                                                                                                                                                                    |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | - option 1                                                                                                                                                                                                                                                                                                                                                                                |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 6    | ``ALWAYS``                                                              | ``string``                                          | explicitly writting the word ``ALWAYS`` will print the spatial moments and/or particle plume locations at every time step.                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | - option 2: snapshot times are defined in a series:                                                                                                                                                                                                                                                                                                                                       |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 6    | ``t_end`` ``n_snap`` ``t_mult``                                         | ``real`` ``integer`` ``real``                       | ``t_end``: time of the last snapshot                                                                                                                                                                                                               |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``n_snap``: number of snapshot                                                                                                                                                                                                                     |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``t_mult``: multiplier between two following time step                                                                                                                                                                                             |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | - option 3: snapshot times are given in a file:                                                                                                                                                                                                                                                                                                                                           |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 6    | ``file``                                                                | ``string``                                          | file with the time at which the spatial moments and/or particle plume locations are printed.                                                                                                                                                       |
    |      |                                                                         |                                                     | The format of the file is given in the following table :ref:`table-snap`.                                                                                                                                                                          |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 7    | ``itmom``                                                               | ``integer``                                         | ``1`` if print temporal moments at snapshots                                                                                                                                                                                                       |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 8    | ``iwbtc`` ``ngrid`` ``method`` ``bw`` ``tmin`` ``tmax`` ``format``      | ``integer`` ``integer`` ``string``                  | ``iwbtc``: ``1`` if print breakthrough curves                                                                                                                                                                                                      |
    |      |                                                                         | ``real`` ``real`` ``real``                          |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``ngrid``: number bins for histogram evaluation                                                                                                                                                                                                    |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``method``: method for the kernel density estimation                                                                                                                                                                                               |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | *values*:                                                                                                                                                                                                                                          |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     |    - ``plugin``: optimizes the bandwidth with an iterative algorithm that minimizes the mean integrated squared error of the density function. In this case, the resulting bandwidth is the standard deviation of the Gaussian density function.   |
    |      |                                                                         |                                                     |    - ``box``: shape of the kernel function                                                                                                                                                                                                         |
    |      |                                                                         |                                                     |    - ``gauss``: shape of the kernel function                                                                                                                                                                                                       |
    |      |                                                                         |                                                     |    - ``triangle``: shape of the kernel function                                                                                                                                                                                                    |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``bw``: half bandwidth support for histogram evaluation (if :math:`<0` then estimated internally)                                                                                                                                                  |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``tmin``: minimum value of the histogram bin (if :math:`<0` then estimated internally)                                                                                                                                                             |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``tmax``: maximum value of the histogram bin (if :math:`<0` then estimated Internally)                                                                                                                                                             |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``format``: ``0`` for printing outputs in a csv file; ``1`` for printing in a binary file.                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 9    | ``iwcbtc`` ``inc`` ``format``                                           | ``integer`` ``integer`` ``integer``                 | ``iwcbtc``: ``1`` if print cumulative breakthrough curves                                                                                                                                                                                          |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``inc``: informs the incremental printing of the CBTC. ``1`` for printing every values; ``2`` for every other values and so on.                                                                                                                    |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``format``: ``0`` for printing outputs in a csv file; ``1`` for printing in a binary file.                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 910  | ``iwhistory`` ``print_out`` ``format``                                  | ``integer`` ``integer`` ``integer``                 | ``iwhistory``: ``1`` if print the plume history                                                                                                                                                                                                    |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``print_out``: ``1`` for printing particles killed during the simulation; ``0`` for not printing them.                                                                                                                                             |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``format``: ``0`` for printing outputs in a csv file; ``1`` for printing in a binary file.                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
    | 11   | ``iwpath`` ``pathfreq`` ``pathpart`` ``format``                         | ``integer`` ``integer`` ``integer``                 | ``iwpath``: ``1`` if print particle paths                                                                                                                                                                                                          |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``pathfreq``: frequency of printing particle path                                                                                                                                                                                                  |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``pathpart``: ID of the particle for which the path is printed                                                                                                                                                                                     |
    |      |                                                                         |                                                     |                                                                                                                                                                                                                                                    |
    |      |                                                                         |                                                     | ``format``: ``0`` for printing outputs in a csv file; ``1`` for printing in a binary file.                                                                                                                                                         |
    +------+-------------------------------------------------------------------------+-----------------------------------------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+


.. container::
   :name: table-snap

   .. table:: Format of the file defining the times for snapshots.
 
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      |Line  | Variable                                                                | Type               | Description                                                                            |
      +======+=========================================================================+====================+========================================================================================+
      | 1    | ``header``                                                              | ``string``         | header line (*not used by the code*)                                                   |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | 2    | ``n_snap``                                                              | ``integer``        | number of snapshots                                                                    |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | repeat the following line ``n_snap`` times:                                                                                                                                                  |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+
      | 3    | ``t_snap``                                                              | ``string``         | time of the snapshot                                                                   |
      +------+-------------------------------------------------------------------------+--------------------+----------------------------------------------------------------------------------------+

**Example**:  

::

   --------------------------------------------------------------------------------------------
    Outputs
   --------------------------------------------------------------------------------------------
   0                                                                     !... ixmom
   0                                                                     !... iwcshot
   150.0   50   1                                                        !... tlen,ntstep,tmult
   0                                                                     !... itmom
   0   100   plugin   -10.   0.0   250.0                                 !... iwbtc, ngrid, Kernel, bw, tmin, tmax
   0   1   1                                                             !... iwcbtc, inc, bin
   1   0   0                                                             !... iwhistory, bin, print_out
   0   1   35496                                                         !... iwpath, pathfreq, pathpart



.. 
  Inputs from MIKE-SHE 
  ------------
..
  A Python script (`../python_utilities/rw3d_inputs_from_mikeshe.py`) has been designed to extract and convert hydrological model outputs from MIKE-SHE simulations into input files for RW3D. 
  It processes a variety of hydrological variables from `.dfs2` and `.dfs3` files and outputs them as text (`.dat`) or NetCDF (`.nc`) files. 
..
  NetCDF files are typically used for transient parameters. At each time step of the MIKE-SHE flow solution, RW3D will read/update the corresponding parameter values. 
  Text files are only read at the start of the RW3D simulation. 
..
  Input Files
  ~~~~~~~~~~
..
  The script expects the following MIKE-SHE output files for a given domain:
..
  - **3D Saturated Zone Head**: `*_3DSZ.dfs3`
  - **3D Saturated Zone Flow**: `*_3DSZflow.dfs3`
  - **2D Unsaturated Zone**: `*_2DUZ_AllCells.dfs2`
  - **2D Saturated Zone Flow**: `*_2DSZflow.dfs2`
  - **3D Preprocessed Data**: `*_PreProcessed_3DSZ.dfs3`
..
  Output Files
  ~~~~~~~~~~
..
  The script generates the following series of `.dat` and `.nc` (NetCDF) files:
..
  *Domain description*: 
..
  - `dz.dat`: Layer thicknesses
  - `floor.dat`: Bottom elevations
  - `InactCell.dat`: Inactive cells
..
  *Water and Flow*:
..
  - `porosity.dat`: Porosity field
  - `heads.nc`: Groundwater heads
  - `qx.nc`: Horizontal fluxes (in x axis) from flow
  - `qy.nc`: Horizontal fluxes (in y axis) from flow
  - `qz.nc`: Vertical groundwater flux
..
  *Sinks* (delete or add more):
..
  - `Qtotal_recharge.nc`: Total recharge
  - `Qriver.nc`: River exchange fluxes
  - `Qdrain.nc`: Drainage fluxes
  - `Qwell.nc`: Groundwater extractions

..
  How to Use
  ~~~~~~~~~~
..
  1. **Set Parameters**:
    - `simul_time`: Start and end dates of the simulation (e.g., `["2000-1-5", "2010-1-6"]`)
    - `domain`: Spatial extent `[left, bottom, right, top]` in model coordinates
..
  2. **Define Input Paths**:
    - Set the paths to the required `.dfs2` and `.dfs3` files using `types.SimpleNamespace()`.
..
  3. **Run the Script**:
    - Call `get_rw3d_inputs(filein, pathout, simul_time[0], simul_time[1], domain)` to generate the output files.
