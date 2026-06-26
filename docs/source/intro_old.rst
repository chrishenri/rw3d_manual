Getting Started
=====

.. _intro:

An Intro to RW3D
------------

**RW3D (Random-Walk in 3 Dimensions)** is an object-oriented Fortran code designed to solve the fate and transport of conservative and certain reactive solutes in porous media using the random-walk particle-tracking method. 
Originally developed as a research tool to better understand solute transport in heterogeneous porous media, RW3D has evolved over the years to tackle increasingly complex and realistic problems.

Each development phase has undergone rigorous code validation by comparing solutions against well-established Eulerian methods and, when possible, analytical solutions :cite:p:`Henri2014,Henri2015`.

Today, RW3D can solve both conservative and reactive transport problems, including first-order decay networks, bimolecular reactions, and linear sorption. 
The code also offers the flexibility to represent all parameters as spatially and temporally variable.

Additionally, the code is now able to read different types of input file formats, including *NetCDF* and *DFS*, a binary data file format typically used in the MIKE Powered by DHI software.

As an open-source project, any contribution to improve and expend the code are welcome.  


How do I get set up?
----------------

Get the code
~~~~~~~~~~

The code will be released soon (expected during fall 2025). To access it, clone (or download) the project's repository here:

.. code-block::
    
    git clone https://github.com/upc-ghs/RW3D


Run the executable
~~~~~~~~~~

A built Windows executable and required dll files is provided in the folder ``exe``.
We can not guarantee that the exe file will run on your machine. Alternatively, the code can be build to insure compatibility with your system. 


Build a solution
~~~~~~~~~~

- RW3D is a Fortran 90 code. Intel Fortran compiler (ifort) and gfortran have been successfully used to build the code. Other compilers, including the newest Intel compiler (ifx), haven't been tested yet. Especially, issues concerning the compiling of the `netCDF-Fortran` library using ifx has been reported. In case you choose to work with Intel, the use of ifort is then still advised. 
- Dependencies: `LAPACK <https://www.netlib.org/lapack/>`_ (llapack), `BLAS <https://www.netlib.org/blas/>`_ (lblas), `netCDF-Fortran <https://docs.unidata.ucar.edu/netcdf-fortran/current/>`_ (lnetcdff), DFS file reader provided by DHI (see below for instructions).


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
    From our experience, this handles required dependencies (especially the NetCDF librairy) in the most straighforward and stable manner. 
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

The code was (and still is )mostly developed on Windows, using Visual Studio. The IDE has the benefit of streamlining the software development process by offering intelligent code completion, some debugging tools, and an integration with version control systems like Git.
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








Visual Studio
""""""""""""""

The code was (and still is) mostly developed on Windows using Visual Studio. The IDE streamlines the software development process by offering intelligent code completion, debugging tools, and integration with version control systems like Git.

The main challenges when building RW3D with Visual Studio are:

- Linking with the ``netCDF-Fortran`` library, which needs to be previously built
- Building the DFS reader libraries required to read DHI file formats

The following instructions (as of 15/01/2025) guide you through the full process.


Download and Install
''''''''''''''''''''

#. Download and install `Microsoft Visual Studio <https://visualstudio.microsoft.com/>`_ following these `instructions <https://www.intel.com/content/www/us/en/developer/articles/guide/installing-microsoft-visual-studio-2019-for-use-with-intel-compilers.html>`_
#. Download and install `Intel Fortran Essentials <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=windows>`_ (make sure that oneMKL is selected during installation; this will provide the ``LAPACK`` and ``BLAS`` libraries)


Build the netCDF-Fortran library
'''''''''''''''''''''''''''''''''

The ``netCDF-Fortran`` library must be built before compiling RW3D. Some issues have been observed on Windows. If you encounter difficulties, this `thread <https://community.intel.com/t5/Intel-Fortran-Compiler/Include-netCDF-in-my-Fortran-projet/m-p/1529236#M168379/>`_ provides some guidance.

The build process will produce two libraries that are needed when linking RW3D:

- ``netcdff.lib`` — the Fortran interface library
- ``netcdf.lib`` — the underlying C library

Note the paths to these files, as they will be needed when configuring the RW3D project properties.

.. tip::

   A common approach on Windows is to use pre-built binaries distributed by `Unidata <https://docs.unidata.ucar.edu/netcdf-c/current/>`_ for the C library, and to build only the Fortran wrapper against it. This can simplify the process significantly.


Build the DFS reader libraries
''''''''''''''''''''''''''''''''

This step is only required if you intend to read DHI ``DFS`` file formats. If you do not need this functionality, skip to `Building RW3D`_.

**Step 1 – Install the DHI NuGet packages**

#. In Visual Studio, navigate to **Tools → NuGet Package Manager → Manage NuGet Packages for Solution**
#. Search for and install the package **DHI.DFS** (version 23.0.3 recommended; other versions may also work)
#. This will automatically install dependent packages and create a ``packages`` folder containing subfolders such as:

   - ``DHI.DFS.23.0.3``
   - ``DHI.DHIfl.23.0.3``
   - ``DHI.EUM.23.0.3``
   - ``DHI.PFS.23.0.3``

.. important::

   The ``packages`` folder must be located in the same folder as the Visual Studio solution file (``.sln``).

**Step 2 – Extract the Fortran wrapper source files**

#. Locate ``MzFortranWrappers_VS_with_dependencies.zip`` in the RW3D repository (under ``lib/dfs/``)
#. Extract the archive into the **same folder** as the ``packages`` folder. The resulting structure should look like:

.. code-block:: text

   your_folder/
   ├── packages/
   │   ├── DHI.DFS.23.0.3/
   │   ├── DHI.DHIfl.23.0.3/
   │   └── ...
   ├── MzFortranWrappers_VS_with_dependencies/
   │   ├── MzF90.sln
   │   ├── MzF90_lib/   (C source files)
   │   └── MzF90/       (Fortran source files)

**Step 3 – Build the libraries**

#. Open ``MzF90.sln`` in Visual Studio
#. Select the build configuration (**Debug** or **Release**) matching the one you intend to use for RW3D
#. Build the projects **in the following order** (the Fortran library depends on the C library):

   #. Build the static C library: **MzF90_lib**
   #. Build the static Fortran library: **MzF90**

The following files will be generated in the ``x64\Release\`` (or ``x64\Debug\``) subfolder:

- ``MzF90.lib`` — static Fortran interface library
- ``MzF90c.lib`` — static C library
- Several ``.dll`` files — required at runtime (see `Runtime setup`_ below)

Note the path to these files, as they will be needed when configuring the RW3D project properties.


Building RW3D
''''''''''''''

#. In Visual Studio: **File** :math:`\to` **New** :math:`\to` **Project**
#. In the New Project window: **Template** :math:`\to` **Intel(R) Visual Fortran** (appears when the compiler has been correctly installed) :math:`\to` **Empty Project** (provide a name and location)
#. In the Solution Explorer panel: drag all Fortran source files into the folder **Source files**
#. Right-click on the project name to open **Property Pages**
#. Select the desired configuration: **Debug** (slower but with detailed error messages) or **Release** (faster, lighter)
#. Under **Configuration Properties** :math:`\to` **Fortran** :math:`\to` **General**: fill **Additional Include Directories** with the path to the ``netCDF-Fortran`` include files
#. Under **Configuration Properties** :math:`\to` **Fortran** :math:`\to` **Libraries**: select **Parallel** in **Use Intel Math Kernel Library**
#. Under **Configuration Properties** :math:`\to` **Linker** :math:`\to` **General**: set **Link Library Dependencies** to **Yes**
#. Under **Configuration Properties** :math:`\to` **Linker** :math:`\to` **Input**: fill **Additional Dependencies** with the following libraries (including their full paths):

   - ``netcdff.lib`` — netCDF Fortran library
   - ``netcdf.lib`` — netCDF C library
   - ``MzF90.lib`` — DFS Fortran wrapper (if building with DFS support)
   - ``MzF90c.lib`` — DFS C wrapper (if building with DFS support)

   .. tip::

      To keep the solution portable across machines, use the ``$(SolutionDir)`` macro to specify relative 
      paths rather than absolute ones. For example:

      .. code-block:: text

         $(SolutionDir)..\MzFortranWrappers_VS_with_dependencies\x64\Release\MzF90.lib

#. Click **OK** to validate the changes
#. Build the solution: **Build** :math:`\to` **Build Solution**
#. The generated executable will be located in the newly created ``Debug\`` or ``Release\`` folder


Runtime setup
''''''''''''''

Before running ``rw3d.exe``, make sure the following ``.dll`` files are present in the **same folder as the executable**:

- The ``.dll`` files generated when building the DFS libraries (from ``x64\Release\`` or ``x64\Debug\``)
- Any ``.dll`` files required by the ``netCDF`` libraries (typically distributed alongside the pre-built binaries)

Without these files, the executable will fail to launch.

How to run RW3D?
----------------

Once you have located the executable file (`rw3d.exe`), just run it and follow the code's instruction (i.e., provide a parameter file, as described here :ref:`Inputs`). 