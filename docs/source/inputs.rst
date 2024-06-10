.. _inputs:

Input Instructions
===============

2 files have to be provided: 

- Name file: File with names for input and output files (by default: **rw3d.nam**)
- Parameter file: File with parameters (file name to be defined in the name file)


Name file
------------

The file consists in 24 lines that must be defined as follow (even if the output option is disabled): 

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
  | 7    | File name    | Output with spatial moments of particle position           |
  +------+--------------+------------------------------------------------------------+
  | 8    | File name    | Output with particle position at control planes            |
  +------+--------------+------------------------------------------------------------+
  | 9    | File name    | Output with dilution index  (*disabled*)                   |
  +------+--------------+------------------------------------------------------------+
  | 10   | File name    | Output with radial spatial moments                         |
  +------+--------------+------------------------------------------------------------+
  | 11   | File name    | Output with temporal moments of breakthrough curves        |
  +------+--------------+------------------------------------------------------------+
  | 12   | File name    | Output with dispersivities from control planes breakthrus  |
  +------+--------------+------------------------------------------------------------+
  | 13   | File name    | Output with residence times in zonal regions (*disabled*)  |
  +------+--------------+------------------------------------------------------------+
  | 14   | File name    | Output with velocity field (for idebug :math:`\geq 1`)     |
  +------+--------------+------------------------------------------------------------+
  | 15   | File name    | Output with derivative of BTC in double log (dbtcs)        |
  +------+--------------+------------------------------------------------------------+
  | 16   | File name    | Output with debug file                                     |
  +------+--------------+------------------------------------------------------------+
  | 17   | File name    | Output with information about particle exiting the domain  |
  +------+--------------+------------------------------------------------------------+
  | 18   | File name    | Output with btcs of particle entering registration lenses  |
  +------+--------------+------------------------------------------------------------+
  | 19   | File name    | Output with cbtcs of particle entering registration lenses |
  +------+--------------+------------------------------------------------------------+
  | 20   | File name    | Output with dbtcs of particle entering registration lenses |
  +------+--------------+------------------------------------------------------------+
  | 21   | File name    | Output with btcs of particle exiting registration lenses   |
  +------+--------------+------------------------------------------------------------+
  | 22   | File name    | Output with cbtcs of particle exiting registration lenses  |
  +------+--------------+------------------------------------------------------------+
  | 23   | File name    | Output with dbtcs of particle exiting registration lenses  |
  +------+--------------+------------------------------------------------------------+
  | 24   | File name    | Output with plume history                                  |
  +------+--------------+------------------------------------------------------------+

..
  ======= ============ ===============================================================
  Line    item type    Description  
  ======= ============ ===============================================================
  1       File name	   Parameter file 
  2       File name	   Output histogram (pdf) of particle arrival times (btcs)
  3	      File name	Output with cumulative pdf particle arrival times (cbtcs) 
  4	      File name	Output with particle snapshots with time 
  5	      File name	Output with particle paths                               
  6	      File name	Output with cartesian spatial moments                      
  7	      File name	Output with spatial moments of particle position           
  8	      File name	Output with particle position at control planes            
  9	      File name	Output with dilution index  (*disabled*)                   
  10   File name	Output with radial spatial moments                         
  11   File name	Output with temporal moments of breakthrough curves        
  12   File name	Output with dispersivities from control planes breakthrus  
  13   File name	Output with residence times in zonal regions (*disabled*)  
  14   File name	Output with velocity field (for idebug :math:`\geq 1`)     
  15   File name	Output with derivative of BTC in double log (dbtcs)        
  16   File name	Output with debug file                                     
  17   File name	Output with information about particle exiting the domain  
  18   File name	Output with btcs of particle entering registration lenses  
  19   File name	Output with cbtcs of particle entering registration lenses 
  20   File name	Output with dbtcs of particle entering registration lenses 
  21   File name	Output with btcs of particle exiting registration lenses   
  22   File name	Output with cbtcs of particle exiting registration lenses  
  23   File name	Output with dbtcs of particle exiting registration lenses  
  24   File name	Output with plume history                                  

Parameter file
------------
