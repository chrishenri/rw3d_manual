Getting Started
===============

.. _intro:

An Intro to RW3D
----------------

**RW3D (Random-Walk in 3 Dimensions)** is an object-oriented Fortran code designed to solve the fate and transport of conservative and certain reactive solutes in porous media using the random-walk particle-tracking method.
Originally developed as a research tool to better understand solute transport in heterogeneous porous media, RW3D has evolved over the years to tackle increasingly complex and realistic problems.

Each development phase has undergone rigorous code validation by comparing solutions against well-established Eulerian methods and, when possible, analytical solutions :cite:p:`Henri2014,Henri2015`.

Today, RW3D can solve both conservative and reactive transport problems, including first-order decay networks, bimolecular reactions, and linear sorption.
The code also offers the flexibility to represent all parameters as spatially and temporally variable.

Additionally, the code is now able to read different types of input file formats, including *NetCDF* and *DFS*, a binary data file format typically used in the MIKE Powered by DHI software.

As an open-source project, any contribution to improve and extend the code is welcome.


How do I get set up?
--------------------

Get the code
~~~~~~~~~~~~

The code will be released soon (expected during fall 2025). To access it, clone (or download) the project's repository here:

.. code-block:: bash

    git clone https://github.com/upc-ghs/RW3D


Run the executable
~~~~~~~~~~~~~~~~~~

A built Windows executable and required dll files are provided in the folder ``exe``.
We cannot guarantee that the exe file will run on your machine. Alternatively, the code can be built to ensure compatibility with your system.


Build a solution
~~~~~~~~~~~~~~~~

RW3D is a Fortran 90 code. The Intel Fortran compiler (ifort) and gfortran have been successfully used to build the code. Other compilers, including the newest Intel compiler (ifx), have not been tested yet. In particular, issues concerning the compilation of the ``netCDF-Fortran`` library using ifx have been reported. If you choose to work with Intel, the use of ifort is advised.

Two build paths are available depending on your platform:

- **Visual Studio** (Windows): recommended for Windows users, supports both NetCDF and DFS file formats
- **Makefile** (Linux/Mac): recommended for Linux and Mac users, supports NetCDF only

.. important::

   RW3D source files contain ``use netcdf`` and ``use MzDfs`` statements, meaning the code
   requires both the ``netCDF-Fortran`` and DFS reader libraries to compile by default.
   On Linux and Mac, DFS support is disabled at compile time using a preprocessor flag
   (see `Makefile`_ section below), and only ``netCDF-Fortran`` is required.


Dependencies
''''''''''''

The following libraries are required to build RW3D:

- `LAPACK <https://www.netlib.org/lapack/>`_ (``-llapack``)
- `BLAS <https://www.netlib.org/blas/>`_ (``-lblas``)
- `netCDF-Fortran <https://docs.unidata.ucar.edu/netcdf-fortran/current/>`_ (``-lnetcdff``)
- DFS file reader provided by DHI — **Windows only** (see `DFS reader libraries`_ below)


DFS reader libraries
''''''''''''''''''''

This section describes how to build the DHI DFS reader libraries on Windows. This is required
when building RW3D with Visual Studio. On Linux and Mac, this step can be skipped.

**Step 1 — Install the DHI NuGet packages**

#. In Visual Studio, navigate to **Tools → NuGet Package Manager → Manage NuGet Packages for Solution**
#. Search for and install the package **DHI.DFS** (version 23.0.3 recommended; other versions may also work)
#. This will automatically install dependent packages and create a ``packages`` folder containing subfolders such as:

   - ``DHI.DFS.23.0.3``
   - ``DHI.DHIfl.23.0.3``
   - ``DHI.EUM.23.0.3``
   - ``DHI.PFS.23.0.3``

.. important::

   The ``packages`` folder must be located in the same folder as the Visual Studio solution file (``.sln``).

**Step 2 — Extract the Fortran wrapper source files**

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

**Step 3 — Build the libraries**

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


Makefile
''''''''

A Makefile is provided in the folder ``make``. It is the recommended build method on Linux and Mac,
and can also be used on Windows via MinGW/MSYS2.

*Project structure*

- **Source files** are located in ``../src/``
- **Object files** are compiled into ``./objtemp/``
- The final **executable** is created in the current directory (``./``) and named ``rw3d``

*Install dependencies*

On Linux, the required libraries can be installed via:

.. code-block:: bash

   sudo apt install liblapack-dev libblas-dev libnetcdff-dev

On Mac, using Homebrew:

.. code-block:: bash

   brew install lapack netcdf

*Build the project*

To compile all source files and create the executable:

.. code-block:: bash

   make

This will:

- Create the ``objtemp`` directory if it does not exist
- Compile all ``.f90`` and ``.F90`` source files into object files
- Link them into the final executable ``rw3d``

DFS support is disabled by default when using the Makefile, since the DHI libraries are not
available on Linux and Mac. The preprocessor flag ``USE_DFS`` is not defined, so all
``#ifdef USE_DFS`` blocks in the source code are excluded at compile time.

.. tip::

   *Using MinGW via MSYS2 on Windows*

   To build the project using the provided Makefile on Windows, we recommend using **MinGW**
   along with the **MSYS2** shell. From our experience, this handles required dependencies
   (especially the NetCDF library) in the most straightforward and stable manner.
   This `link <https://code.visualstudio.com/docs/cpp/config-mingw>`_ provides useful information
   to install MinGW and configure Visual Studio Code. Here, we summarize the installation process:

   **Install MSYS2**

   1. Download and install MSYS2 from `https://www.msys2.org <https://www.msys2.org>`_ or
      directly from `here <https://github.com/msys2/msys2-installer/releases/>`_.

   2. Open the **MSYS2 MSYS** terminal and run:

   .. code-block:: bash

       pacman -S --needed base-devel mingw-w64-ucrt-x86_64-toolchain

   3. Add the path of your MinGW-w64 bin folder to the Windows PATH environment variable
      (by default: ``C:\msys64\ucrt64\bin``)

   **Install required packages**

   .. code-block:: bash

       pacman -S mingw-w64-x86_64-gcc-fortran
       pacman -S mingw-w64-x86_64-gcc make
       pacman -S mingw-w64-ucrt-x86_64-lapack
       pacman -S mingw-w64-ucrt-x86_64-netcdf-fortran

   You should now be able to build the code using the ``make`` command.

   Note that building with DFS support via the Makefile on Windows is not currently supported.
   Use Visual Studio instead if DFS file reading is required.


Visual Studio
'''''''''''''

The code was (and still is) mostly developed on Windows using Visual Studio. The IDE streamlines
the software development process by offering intelligent code completion, debugging tools, and
integration with version control systems like Git.

A Visual Studio solution file (``.sln``) is provided in the repository for convenience. It was
developed and tested with Visual Studio 2022 and Intel Fortran Essentials. It may require
adjustments on other configurations, particularly regarding library paths which are machine-specific.

The following instructions (as of 15/01/2025) guide you through building RW3D from scratch.

**Download and install**

#. Download and install `Microsoft Visual Studio <https://visualstudio.microsoft.com/>`_ following
   these `instructions <https://www.intel.com/content/www/us/en/developer/articles/guide/installing-microsoft-visual-studio-2019-for-use-with-intel-compilers.html>`_
#. Download and install `Intel Fortran Essentials <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=windows>`_
   (make sure that oneMKL is selected during installation; this will provide the ``LAPACK`` and ``BLAS`` libraries)

**Build the netCDF-Fortran library**

The ``netCDF-Fortran`` library must be built before compiling RW3D. Some issues have been observed
on Windows. If you encounter difficulties, this
`thread <https://community.intel.com/t5/Intel-Fortran-Compiler/Include-netCDF-in-my-Fortran-projet/m-p/1529236#M168379/>`_
provides some guidance. The build process will produce two libraries needed when linking RW3D:

- ``netcdff.lib`` — the Fortran interface library
- ``netcdf.lib`` — the underlying C library

Note the paths to these files, as they will be needed when configuring the RW3D project properties.

.. tip::

   A common approach on Windows is to use pre-built binaries distributed by
   `Unidata <https://docs.unidata.ucar.edu/netcdf-c/current/>`_ for the C library, and to build
   only the Fortran wrapper against it. This can simplify the process significantly.

**Build the DFS reader libraries**

Follow the instructions in the `DFS reader libraries`_ section above to build ``MzF90.lib`` and
``MzF90c.lib`` before proceeding.

If you do not need to read DFS files, this step can be skipped, provided you remove the
``USE_DFS`` preprocessor flag from the project settings (see below).

**Build RW3D**

#. In Visual Studio: **File** :math:`\to` **New** :math:`\to` **Project**
#. In the New Project window: **Template** :math:`\to` **Intel(R) Visual Fortran** (appears when
   the compiler has been correctly installed) :math:`\to` **Empty Project** (provide a name and location)
#. In the Solution Explorer panel: drag all Fortran source files into the folder **Source files**
#. Right-click on the project name to open **Property Pages**
#. Select the desired configuration: **Debug** (slower but with detailed error messages) or
   **Release** (faster, lighter)
#. Under **Configuration Properties** :math:`\to` **Fortran** :math:`\to` **Preprocessor** :math:`\to`
   **Preprocessor Definitions**: add ``USE_DFS`` to enable DFS support. Remove or leave empty to
   build without DFS support
#. Under **Configuration Properties** :math:`\to` **Fortran** :math:`\to` **Preprocessor** :math:`\to`
   **Preprocess Source File**: select **Yes**
#. Under **Configuration Properties** :math:`\to` **Fortran** :math:`\to` **General**: fill
   **Additional Include Directories** with the path to the ``netCDF-Fortran`` include files
#. Under **Configuration Properties** :math:`\to` **Fortran** :math:`\to` **Libraries**: select
   **Parallel** in **Use Intel Math Kernel Library**
#. Under **Configuration Properties** :math:`\to` **Linker** :math:`\to` **General**: set
   **Link Library Dependencies** to **Yes**
#. Under **Configuration Properties** :math:`\to` **Linker** :math:`\to` **Input**: fill
   **Additional Dependencies** with the following libraries (including their full paths):

   - ``netcdff.lib`` — netCDF Fortran library
   - ``netcdf.lib`` — netCDF C library
   - ``MzF90.lib`` — DFS Fortran wrapper *(only if building with DFS support)*
   - ``MzF90c.lib`` — DFS C wrapper *(only if building with DFS support)*

   .. tip::

      To keep the solution portable across machines, use the ``$(SolutionDir)`` macro to specify
      paths relative to the solution file rather than absolute paths. For example:

      .. code-block:: text

         $(SolutionDir)..\MzFortranWrappers_VS_with_dependencies\x64\Release\MzF90.lib

#. Click **OK** to validate the changes
#. Build the solution: **Build** :math:`\to` **Build Solution**
#. The generated executable will be located in the newly created ``Debug\`` or ``Release\`` folder

.. important::

   When using preprocessor directives in Fortran source files (``.F90``), the ``#`` character
   must always be placed in **column 1** (no leading spaces or indentation). Intel Fortran
   enforces this strictly — indented directives will be ignored by the preprocessor and the
   code will fail to compile.

**Runtime setup**

Before running ``rw3d.exe``, make sure the following ``.dll`` files are present in the
**same folder as the executable**:

- The ``.dll`` files generated when building the DFS libraries (from ``x64\Release\`` or ``x64\Debug\``) *(only if built with DFS support)*
- Any ``.dll`` files required by the ``netCDF`` libraries (typically distributed alongside the pre-built binaries)

Without these files, the executable will fail to launch.


How to run RW3D?
----------------

Once you have located the executable file (``rw3d.exe``), just run it and follow the code's
instructions (i.e., provide a parameter file, as described here :ref:`Inputs`).
