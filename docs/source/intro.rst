Getting Started
=====

.. _intro:

An Intro to RW3D
------------

RW3D (Random-Walk in 3 Dimensions) is an object-oriented Fortran code solving the fate and transport of conservative and (some) reactive solute in porous media using the random-walk particle-tracking method. 
RW3D was originally developed to run on (up to 3D) flow fields produced by MODFLOW. The code has developed over the years to solve more and more complex problem. 
Each development was subject of a code validation by comparing solutions against well established Eulerian methods and, when possible, analytical solutions :cite:t:`Henri2014,Henri2015`. 

Today, the code can solve conservative and reactive transport (first-order decay network, bimolecular reactions, linear sorption), with an option to represent all parameteres spatially and temporally variable. 
A series of preprocessing codes (Python) allow to run reactive transport on outputs from complex MIKE-She models. 


How do I get set up?
----------------

- The RW3D source code can be found on the `RW3D GitHub <https://github.com/upc-ghs/RW3D>`_.
- RW3D is a Fortran90 code build with the Intel Fortran compiler. Other compilers haven't been tested yet.
- Dependencies: `LAPACK <https://www.netlib.org/lapack/>`_, `netcdf-fortran <https://docs.unidata.ucar.edu/netcdf-fortran/current/>`_
- Brief instructions to build a solution using Microsoft Visual Studio IDE and Intel Fortran:

#. Download Visual Studio Community for free; Install
#. Download Intel Parallel Studio XE (for free for student and educators); Install (the included MKL library has to been also installed)
#. In Visual Studio: File -> New -> Project
#. In the New Project window: Template -> Intel(R) Visual Fortran (appear when the compiler has been correctly installed) -> Empty Project (provide Name and Location)
#. In the Solution Explorer panel: Drag all fortran input files into the folder "Source files"
#. Right click on the project name; the Property Pages appears
#. Select the desired Configuration: Debug (running the code will be slower but more detailed error messages will be displayed) or Release (lighter, faster solution)
#. In Configuration Properties -> Fortran -> Libraries: Select "Parallel" in Use Intel Math Kernel Library
#. In Configuration Properties -> Linker -> General: Select "Yes" in Link Library Dependencies; click OK to validate the changes
#. The solution can now be built; In Visual Studio: Build -> Build Solution
#. The generated executable, located in the newly created folder (named Debug or Release, following the chosen Configuration), is now ready to be used.
