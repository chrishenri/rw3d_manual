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
- Compile all `.f90` files into object files
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

    1. Build the `netCDF-Fortran` library. Some issues has been observed on Windows. If this is your case, this `thread <https://community.intel.com/t5/Intel-Fortran-Compiler/Include-netCDF-in-my-Fortran-projet/m-p/1529236#M168379/>`_ provides some guidance.  

    2. Download and Install `Microsoft Visual Studio <https://visualstudio.microsoft.com/>`_ following these `instructions <https://www.intel.com/content/www/us/en/developer/articles/guide/installing-microsoft-visual-studio-2019-for-use-with-intel-compilers.html>`_
    
    3. Download `Intel Fortran Essentials <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=windows>`_; Install (make sure that oneMKL is installed; this will install the `LAPACK` and `BLAS` libraries)
    
    **Buildind RW3D**

    Follow these steps to build the RW3D project using Visual Studio with Intel® Visual Fortran:

    1. **Create a New Project**
    - Open **Visual Studio**.
    - Navigate to **File :math:`\to` New :math:`\to` Project**.
    - In the **New Project** window:
        - Select **Template :math:`\to` Intel® Visual Fortran** (this appears only if the compiler is correctly installed).
        - Choose **Empty Project**.
        - Provide a **Name** and **Location** for your project.

    2. **Add Source Files**
    - In the **Solution Explorer** panel:
        - Drag all Fortran input files into the **"Source Files"** folder.

    3. **Open Project Properties**
    - Right-click on the project name.
    - Select **Properties** to open the **Property Pages**.

    4. **Select Build Configuration**
    - Choose either:
        - **Debug**: Slower execution, but detailed error messages.
        - **Release**: Faster execution, optimized for performance.

    5. **Configure Fortran Settings**
    - Go to **Configuration Properties :math:`\to` Fortran :math:`\to` General**:
        - Set **Additional Include Directories** to the path of the `netcdff.lib` (netCDF-Fortran library).

    6. **Enable Parallel Math Kernel Library**
    - Go to **Configuration Properties :math:`\to` Fortran :math:`\to` Libraries**:
        - Set **Use Intel Math Kernel Library** to **Parallel**.

    7. **Linker Settings**
    - Under **Configuration Properties :math:`\to` Linker :math:`\to` General**:
        - Set **Link Library Dependencies** to **Yes**.
    - Under **Linker :math:`\to` Input**:
        - Add the paths to the **netCDF-Fortran** and **netCDF-C** libraries in **Additional Dependencies**.
        - Click **OK** to apply changes.

    8. **Build the Solution**
    - In Visual Studio, go to **Build :math:`\to` Build Solution**.

    9. **Locate the Executable**
    - The compiled executable will be located in the **Debug** or **Release** folder, depending on the selected configuration.