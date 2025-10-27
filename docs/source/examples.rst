.. _examples:

Examples
=====

Bornholm's groundwater bodies characterization
----------------

.. figure:: Bornholm_topography.png
    :align: center
    :width: 100%

    Grid and topography of the domain visualized in Paraview


In this example, we will simulate the arrival of particle in a series of groundwater bodies located in the island of Bornholm, Denmark. 
All input files are located in the folder ``examples\Bornholm_groundwater_bodies``

Here we detail the provided parameter file: 

General Setup
~~~~~~~~~~~~~~~~~~
Here, we specify in which folder outputs (and log file) will be printed, as well as the base name of those outputs. 
Remember to set the absolute path of your output folder. 

- ``..\Bornholm_outputs                                       !... path_outputs``
- ``Bornholm                                                  !... basename_outputs``


Species and Phases
~~~~~~~~~~~~~~~~~~
Our problem involves a single aqueous chemical species that we will call "A". No mineral species are considered in this simulation.

- ``1  0                                                      !... nspe_aq, nspe_min``
- ``A                                                         !... name_aq``

Simulation Time and Transport modes
~~~~~~~~~~~~~~~~~~
The simulation runs for a total of 1000 time units using forward water transport. It is a steady-state simulation.

- ``1000.0    FW                                              !... t_sim, transport_mode``
- ``F                                                         !... transient_flag``

Spatial discretization and Boundaries
~~~~~~~~~~~~~~~~~~
The model domain is defined with a specific origin and the grid consists of 330 cells in the *x*-direction, 360 cells in the *y*-direction and 7 layers. 
Horizontal spacing is uniform (100 meters), while vertical spacing varies and is read from an external file (*DFS3* format). Topography and inactive cells are also specified in a *DFS2* and *DFS3* file, respectively.
All borders of the domain are "open", i.e., particles will be "killed" if they leave the domain. 
The grid, topgether will the domain topography and active/inactive cells will be printed in a vtu file for visualization in, e.g., Paraview. 

- ``861050.0   6109050.0                                      !... x_origin2, y_origin2``
- ``330   360   7                                             !... nx2, ny2, nz2``
- ``not_used                       100.0   1   0              !... dx array``
- ``not_used                       100.0   1   0              !... dy array``
- ``dz_opl71.dfs3                  1.0     1   3   F          !... dz array``
- ``topography_opl71.dfs2          1.0     1   3              !... topography elevation array``
- ``InactCell_opl71.dfs3           1.0     1   3   T          !... inactive``
- ``0 0 0 0 0 0                                               !... ibound``
- ``T                                                         !... print grid to vtu file (Paraview)``

Time Discretization
~~~~~~~~~~~~~~~~~~
Time stepping is controlled using a constant Courant number method. Additional parameters include Peclet number and coefficients for kinetic, decay, and MRMT processes. Those will not be used, but need to be specified. 
The slowest 1% particles will be disregarded in the time step calculation by setting the relaxation factor to 0.99.  

- ``constant_cu                                               !... dt_method``
- ``0.5  0.2  0.2  0.1  0.1  0.1                              !... dt, cu, pe, da_kinetic, da_decay, da_mrmt``
- ``0.99                                                      !... time_step relaxation``

Advection
~~~~~~~~~~~~~~~~~~
Advection is enabled and handled using an Eulerian approach. Velocity fields in all three directions and porosity are read from external files (*DFS3* files).
Flows are provided in *x* and *y* directions and need then be converted to fluxes within the code, while fluxes are directly provided in the *z*-direction. 

- ``T                                                         !... advection_action``
- ``Eulerian                                                  !... advection_method``
- ``Qx_opl71.dfs3                  1.0   1   3   F   T        !... qx array``
- ``Qy_opl71.dfs3                  1.0   1   3   F   T        !... qy array``
- ``qz_opl71.dfs3                  1.0   1   3   F   F        !... qz array``
- ``porosity_opl71.DFS3            1.0   1   3   F            !... porosity array``

Heads
~~~~~~~~~~~~~~~~~~
Hydraulic heads are included in the simulation and read from a *DFS3* file. A minimum head threshold is set to 5 cm to identify dry cells.

- ``T                                                         !... heads_action``
- ``heads_opl71.dfs3               1.0   1   3   F            !... heads array``
- ``0.05                                                      !... dry_cell_threshold``

Sinks
~~~~~~~~~~~~~~~~~~
Four types of sinks are defined: river, drain, unsaturated zone, and well. Each is associated with a specific input file (all read from a DFS file). 
Arrival into each sink will be recorded in a breakthrough curve. 

- ``T                                                         !... sink_action``
- ``4                                                         !... sink_number``
- ``river  Qriver_opl71.dfs3       1.0   1   3   F   T        !... name, qsink array``
- ``drain  Qdrain_opl71.dfs3       1.0   1   3   F   T        !... name, qsink array``
- ``uz     Qrech_opl71.dfs2        1.0   1   3   F   T        !... name, qsink array``
- ``well   Qwell_opl71.dfs3        1.0   1   3   F   T        !... name, qsink array``

Dispersion / Diffusion
~~~~~~~~~~~~~~~~~~
Dispersion and diffusion processes are not considered in this test case.

- ``F                                                         !... dispersion_action``

Mass transfer
~~~~~~~~~~~~~~~~~~
Mass transfer between mobile and immobile zones is disabled.

- ``F                                                         !... mrmt_action``

Retardation
~~~~~~~~~~~~~~~~~~
Chemical retardation effects are not included in this simulation.

- ``F                                                         !... retardation_action``

Linear reaction
~~~~~~~~~~~~~~~~~~
Linear decay reactions are not modeled in this test case.

- ``F                                                         !... decay_action``

Bimolecular reaction
~~~~~~~~~~~~~~~~~~
No bimolecular reactions are considered.

- ``F                                                         !... kinetic_action``

Control surface
~~~~~~~~~~~~~~~~~~
Six regional lenses are defined for breakthrough curve analysis. 
The extent and boundaries (top and bottom elevations) are specified using DFS2 files. 
For each lense, a DFS2 file specifying registration codes cell by cell is provided. Each registration code correspond to a specific groundwater body. 
Time and location of every particle entery and exit into/from each registration code (i.e., groundwater body) will be recorded. 

- ``0   F                                                     !... nwell``
- ``0   F                                                     !... nplane``
- ``6                                                         !... nreglens``
- ``330   360                                                 !... nx2, ny2``
- ``not_used                         100.0   1   0            !... dx array``
- ``not_used                         100.0   1   0            !... dy array``
- ``F   T``
- ``reglenses\bottom_layer1.dfs2     1.0   1   3   F``
- ``reglenses\top_layer1.dfs2        1.0   1   3   F``
- ``reglenses\regcodes_layer1.dfs2   1.0   1   3``
- ``F   T``
- ``reglenses\bottom_layer2.dfs2     1.0   1   3   F``
- ``reglenses\top_layer2.dfs2        1.0   1   3   F``
- ``reglenses\regcodes_layer2.dfs2   1.0   1   3``
- ``F   T``
- ``reglenses\bottom_layer3.dfs2     1.0   1   3   F``
- ``reglenses\top_layer3.dfs2        1.0   1   3   F``
- ``reglenses\regcodes_layer3.dfs2   1.0   1   3``
- ``F   T``
- ``reglenses\bottom_layer4.dfs2     1.0   1   3   F``
- ``reglenses\top_layer4.dfs2        1.0   1   3   F``
- ``reglenses\regcodes_layer4.dfs2   1.0   1   3``
- ``F   T``
- ``reglenses\bottom_layer5.dfs2     1.0   1   3   F``
- ``reglenses\top_layer5.dfs2        1.0   1   3   F``
- ``reglenses\regcodes_layer5.dfs2   1.0   1   3``
- ``F   T``
- ``reglenses\bottom_layer6.dfs2     1.0   1   3   F``
- ``reglenses\top_layer6.dfs2        1.0   1   3   F``
- ``reglenses\regcodes_layer6.dfs2   1.0   1   3``

Injection
~~~~~~~~~~~~~~~~~~

.. figure:: Bornholm_plume_t0.png
    :align: center
    :width: 100%

    Initial location of particles


One injection event is defined using a DIRAC pulse in a specific layer. The horizontal extent is read from a DFS2 file.
All particles are injected at time 0. 

- ``1                                                         !... ninj``
- ``layer  random   DIRAC  T                                  !... name_inj, type_inj``
- ``1   0     1                                               !... pmass,zone,specie``
- ``6   10   0.9   T                                          !... lay_inj2,np_cell,lay_loc,horizontal_extent``
- ``injection_extent.dfs2      1.0   1   3                    !... horizontal_extent``
- ``0.0                                                       !... tinj``

Recirculation
~~~~~~~~~~~~~~~~~~
Recirculation is not enabled in this test case.

- ``F                                                         !... recirculation_action``

Outputs
~~~~~~~~~~~~~~~~~~
Finally, we want to print 100 plume snapshots, from t=0 to t=1000 years. We will also print the cumulative breakthrough curves, as well of the full plume history.   

- ``0                                                         !... ixmom``
- ``1   0                                                     !... iwcshot, output_format``
- ``1000   100   1``
- ``0                                                         !... itmom``
- ``0   100   plugin   -10.   0.0   250.0    0                !... iwbtc, ngrid, Kernel, bw, tmin, tmax, output_format``
- ``1   1   0                                                 !... iwcbtc, inc, output_format``
- ``1   0   0                                                 !... iwhistory, print_out, output_format``
- ``0   1   1   0                                             !... iwpath, pathfreq, pathpart, output_format``


Output Analysis
~~~~~~~~~~~~~~~~~~
Running RW3D using this parameter file will produce a series of csv files corresponding to breakthrough curves of particles *in* and *out* each groundwater body, and each sink: 

.. figure:: Bornholm_all_cbtc_in_gwb.png
    :align: center
    :width: 100%

    Breakthrough curves for all particles entering groundwater bodies. 

A series of particle snapshots (location and key properties at a given time) will also be printed into a corresponding series of csv files. 
Those can be directly imported into Paraview for 3D visualization. 

.. figure:: plume_Bornholm_top.gif
    :align: center
    :width: 100%

    Plume snapshots. 


Reactive transport
----------------

*... coming soon*


