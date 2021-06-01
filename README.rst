pypro
=====

Python wrapper for the profile_design library.

The pypro package provides access to the Fortran profile_design library through a Cython
interface.

Build dependencies
------------------
Required to build the package from source:

* CMake
* C Compiler (consistent with CPython, e.g. MSVC)
* Fortran Compiler (consistent with the C compiler, e.g. Intel Fortran)  
* NumPy
* Cython

NumPy is a dependency of setup.py and needs to be installed manually. Currently it is not
possible to specify NumPy as a setup dependency in pyproject.toml when running behind a
proxy because of a bug in pip that doesn't pass proxy settings to child processes
(issue `#6018 <https://github.com/pypa/pip/issues/6018>`_).

Installation
------------
From source
^^^^^^^^^^^
#. Clone the repository and it's submodules:

   ``git clone --recurse-submodules git@github.build.ge.com:SteamDigital/pypro.git``
   
#. Build the project using CMake. This will build the shared library, and copy the 
   run-time dependencies to ``pypro/bin`` and the required link library to ``pypro/lib``.

   Example using "NMake Makefiles" as the generator (from the cloned pypro directory):

   | ``mkdir build``
   | ``cd build``
   | ``cmake -G "NMake Makefiles" ..``
   | ``cmake --build .``

   Currently on Windows the default generator is "Visual Studio". If you want to use
   "NMake Makefiles" then CMake must be run from within the Intel compiler command
   prompt so that the environment is set up correctly. Currently the official release
   of "Ninja" doesn't  fully support Fortran and so it isn't recommended.
   
#. Install the package to the current python enviornment using pip (from the cloned
   pypro directory):

   ``pip install .``

From wheel
^^^^^^^^^^
Wheels are pre-compiled binaries that do not require the build dependencies to install.
They are compiled for a specific Python version and target system.

If you require access to the wheels, or different version of a wheel then please contact
kane.chandler@ge.com.

#. Download the appropriate wheel from
   `the pypro Box folder <https://ge.box.com/s/rze7yi82u79m8z8mrlzbumge76tanyin>`_.

#. Install the package to the current python enviornment using pip:

   ``pip install [path to wheel]``
   

Usage
-----

The example below shows how to fit a B-Spline to a set of coordinates defined by arrays
x_points and y_points, ordered from the suction side to the pressure side. This will fit
a C4-continuous, uniformly spaced, open clamped B-Spline to the data whilst fixing the
parametric value of the point closest to the leading edge, defined by (x_le, y_le),
to avoid "wrapping". 

.. code:: python

   import pypro
   x_points: np.ndarray
   y_points: np.ndarray
   x_le: float
   y_le: float
   ncps: int
   x_cp, y_cp = pypro.fitting.get_control_points(x_points, y_points, ncps, x_le, y_le)
