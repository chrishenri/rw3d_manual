Getting Started
=====

.. _intro:

An Intro to RW3D
------------

RW3D (Random-Walk in 3 Dimensions) is an object-oriented Fortran code solving the fate and transport of conservative and (some) reactive solute in porous media using the random-walk particle-tracking method. 
RW3D was originally developed to run on (up to 3D) flow fields produced by MODFLOW. The code has developed over the years to solve more and more complex problem. 
Each development was subject of a code validation by comparing solutions against well established Eulerian methods and, when possible, analytical solutions :cite:p:`Henri2014,Henri2015`. 

Today, the code can solve conservative and reactive transport (first-order decay network, bimolecular reactions, linear sorption), with an option to represent all parameteres spatially and temporally variable. 
Ampng other, a series of preprocessing codes (Python) allow to run reactive transport on outputs from (relatively) complex MIKE-She models. 


How do I get set up?
----------------

Get the code
~~~~~~~~~~

Clone the repository: 

The code will be publically release here soon:

```
git clone https://github.com/upc-ghs/RW3D
```

Build a solution
~~~~~~~~~~

- RW3D is a Fortran 90 code. Intel Fortran compiler and gfortran have been successfully used to build the code. Other compilers haven't been tested yet.
- Dependencies: `LAPACK <https://www.netlib.org/lapack/>`_ (llapack), `BLAS <https://www.netlib.org/blas/>`_ (lblas), `netCDF-Fortran <https://docs.unidata.ucar.edu/netcdf-fortran/current/>`_ (lnetcdff)

Makefile
""""""""""

A makefile is provided in the folder ``make``. For Windows user, we advise using `MinGW <MinGW>`_ and its libraries to build the code. 
From our experience, this handles required dependencies in the most straighforward and stable manner. Instructions will come soon.  


Visual Studio
""""""""""

.. tip::
    For Windows developers, here are some brief instructions (as of 15/01/2025) to build a solution using Microsoft Visual Studio IDE and Intel Fortran:

    *Download and Install:*

    #. Build the `netCDF-Fortran` library. Some issues has been observed on Windows. If this is your case, this `thread <https://community.intel.com/t5/Intel-Fortran-Compiler/Include-netCDF-in-my-Fortran-projet/m-p/1529236#M168379/>`_ provides some guidance.  
    #. Download and Install `Microsoft Visual Studio <https://visualstudio.microsoft.com/>`_ following these `instructions <https://www.intel.com/content/www/us/en/developer/articles/guide/installing-microsoft-visual-studio-2019-for-use-with-intel-compilers.html>`_
    #. Download `Intel Fortran Essentials <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=windows>`_; Install (make sure that oneMKL is installed; this will install the `LAPACK` and `BLAS` libraries)
    
    *Buildind RW3D:*

    #. In Visual Studio: File :math:`\to` New :math:`\to` Project
    #. In the New Project window: Template :math:`\to` Intel(R) Visual Fortran (appear when the compiler has been correctly installed) :math:`\to` Empty Project (provide Name and Location)
    #. In the Solution Explorer panel: Drag all fortran input files into the folder "Source files"
    #. Right click on the project name; the Property Pages appears
    #. Select the desired Configuration: Debug (running the code will be slower but more detailed error messages will be displayed) or Release (lighter, faster solution)
    #. In Configuration Properties :math:`\to` Fortran :math:`\to` General: Fill "Additional Include Directories" with the path to the netCDF-Fortran library (`netcdff.lib`)
    #. In Configuration Properties :math:`\to` Fortran :math:`\to` Libraries: Select "Parallel" in Use Intel Math Kernel Library
    #. In Configuration Properties :math:`\to` Linker :math:`\to` General: Select "Yes" in Link Library Dependencies
    #. In Configuration Properties :math:`\to` Linker :math:`\to` Input: Fill "Additional Dependencies" with the `netCDF-Fortran` and `netCDF-C` libraries (with their paths); click OK to validate the changes
    #. The solution can now be built; In Visual Studio: Build :math:`\to` Build Solution
    #. The generated executable, located in the newly created folder (named Debug or Release, following the chosen Configuration), is now ready to be used.
