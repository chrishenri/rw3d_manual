Getting Started
=====

.. _intro:

An Intro to RW3D
------------

**RW3D (Random-Walk in 3 Dimensions)** is an object-oriented Fortran code designed to solve the fate and transport of conservative and certain reactive solutes in porous media using the random-walk particle-tracking method. 
Originally developed to operate on up to 3D flow fields produced by MODFLOW, RW3D has evolved over the years to tackle increasingly complex problems.

Each development phase has undergone rigorous code validation by comparing solutions against well-established Eulerian methods and, when possible, analytical solutions :cite:p:`Henri2014,Henri2015`.

Today, RW3D can solve both conservative and reactive transport problems, including first-order decay networks, bimolecular reactions, and linear sorption. 
The code also offers the flexibility to represent all parameters as spatially and temporally variable.

Additionally, a series of preprocessing codes written in Python enable the execution of reactive transport simulations on outputs from relatively complex MIKE-She models.


How do I get set up?
----------------

Get the code
~~~~~~~~~~

The code will be released soon (expected during spring 2025). To access it, clone (or download) the project's repository here:

.. code-block::
    
    git clone https://github.com/upc-ghs/RW3D


Build a solution
~~~~~~~~~~

- RW3D is a Fortran 90 code. Intel Fortran compiler (ifort) and gfortran have been successfully used to build the code. Other compilers, including the newest Intel compiler (ifx), haven't been tested yet. Especially, issues concerning the compiling of the `netCDF-Fortran` library using ifx has been reported. In case you choose to work with Intel, the use of ifort is then still advised. 
- Dependencies: `LAPACK <https://www.netlib.org/lapack/>`_ (llapack), `BLAS <https://www.netlib.org/blas/>`_ (lblas), `netCDF-Fortran <https://docs.unidata.ucar.edu/netcdf-fortran/current/>`_ (lnetcdff)

Makefile
""""""""""

A makefile is provided in the folder ``make``. 

*Project Structure*

- **Source files** are located in `../src/`
- **Object files** are compiled into `./objtemp/`
- The final **executable** is created in the current directory (`./`) and named `rw3d`


*Build the Project*

To compile all source files and create the executable:

.. code-block::
    
    make


This will:

- Create the `objtemp` directory if it doesn't exist
- Compile all `.f`, `.f90`, `.c`, etc. files into object files
- Link them into the final executable `rw3d`


.. tip::
    
    *Using MinGW via MSYS2*
    
    To build the project using the provided Makefile on Windows, we recommend using **MinGW** along with the **MSYS2** shell.
    From our experience, this handles required dependencies in the most straighforward and stable manner. 
    This `link <https://code.visualstudio.com/docs/cpp/config-mingw>`_ provides useful information to install MinGW and configure Viusal Studio Code. 
    Here, we summarize the installation process:
    
    **Install MSYS2**
    
    1. Download and install MSYS2 from `https://www.msys2.org <https://www.msys2.org>`_ or directly from `here <https://github.com/msys2/msys2-installer/releases/>`_.
    
    2. Open the **MSYS2 MSYS** terminal and run:
    
    .. code-block::
    
        pacman -S --needed base-devel mingw-w64-ucrt-x86_64-toolchain
    
    3. Add the path of your MinGW-w64 bin folder to the Windows PATH environment variable (by default this path should be ``C:\\msys64\\ucrt64\\bin``)
    
    **Install Required Packages**
    
    Install the required compilers and tools:
    
    .. code-block::
        
        pacman -S mingw-w64-x86_64-gcc-fortran 
        pacman -S mingw-w64-x86_64-gcc make
        pacman -S mingw-w64-ucrt-x86_64-lapack
        pacman -S mingw-w64-ucrt-x86_64-netcdf-fortran
    
    You should now be able to build the code using the ``make`` command. 


Visual Studio
""""""""""

The code was mostly developed on Windows, using Visual Studio. The IDE has the benefit of streamlining the software development process by offering intelligent code completion, some debugging tools, and an integration with version control systems like Git.
From our experience, one of the main challenge in using Visual Studio is the linking with the netcdf-Fortran library that needs to be previously built. 
If you are a relatively new Windows developers, here are some brief instructions (as of 15/01/2025) to build a solution using Visual Studio IDE and Intel Fortran:

.. tip::

    **Download and Install**

    #. Build the `netCDF-Fortran` library. Some issues has been observed on Windows. If this is your case, this `thread <https://community.intel.com/t5/Intel-Fortran-Compiler/Include-netCDF-in-my-Fortran-projet/m-p/1529236#M168379/>`_ provides some guidance.  
    #. Download and Install `Microsoft Visual Studio <https://visualstudio.microsoft.com/>`_ following these `instructions <https://www.intel.com/content/www/us/en/developer/articles/guide/installing-microsoft-visual-studio-2019-for-use-with-intel-compilers.html>`_
    #. Download `Intel Fortran Essentials <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=windows>`_; Install (make sure that oneMKL is installed; this will install the `LAPACK` and `BLAS` libraries)
    
    **Buildind RW3D**

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
