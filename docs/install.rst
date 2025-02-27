.. meta::
  :description: hipBLAS documentation and API reference library
  :keywords: hipBLAS, rocBLAS, BLAS, ROCm, API, Linear Algebra, documentation

.. _install:

***********************
Building and Installing
***********************

Prerequisites
=============

* If using the rocBLAS backend on an AMD machine:

  * A ROCm enabled platform. Find more information on the :doc:`System requirements (Linux) <rocm-install-on-linux:reference/system-requirements>` page, or the :doc:`System requirements (Windows) <rocm-install-on-windows:reference/system-requirements>` page.
  * A compatible version of rocBLAS
  * A compatible version of rocSOLVER for full functionality

* If using the cuBLAS backend on a Nvidia machine:

  * A HIP enabled platform, more information :doc:`HIP installation <hip:how_to_guides/install>` page.
  * A working CUDA toolkit, including cuBLAS, see `CUDA toolkit <https://developer.nvidia.com/accelerated-computing-toolkit/>`_.

Installing pre-built packages
=============================

Download pre-built packages either from `ROCm's native package manager <https://rocm.docs.amd.com/projects/install-on-linux/en/latest/tutorial/quick-start.html#native-package-manager>`_ or by clicking the `GitHub releases tab <https://github.com/ROCm/hipBLAS/releases>`_ and manually downloading, which could be newer.  Release notes are available for each release on the releases tab.

.. code-block::bash
   sudo apt update && sudo apt install hipblas

hipBLAS build
========================

Build library dependencies + library
------------------------------------
The root of this repository has a helper bash script ``install.sh`` to build and install hipBLAS with a single command.  It does take a lot of options and hard-codes configuration that can be specified through invoking ``cmake`` directly, but it's a great way to get started quickly and can serve as an example of how to build/install.
A few commands in the script need sudo access so that it may prompt you for a password.

Typical uses of ``install.sh`` to build (library dependencies + library) are
in the table below.

.. tabularcolumns::
   |\X{1}{4}|\X{3}{4}|

+-------------------------------------------+--------------------------+
|  Command                                  | Description              |
+===========================================+==========================+
| ``./install.sh -h``                       | Help information.        |
+-------------------------------------------+--------------------------+
| ``./install.sh -d``                       | Build library            |
|                                           | dependencies and library |
|                                           | in your local directory. |
|                                           | The -d flag only needs   |
|                                           | to be used once. For     |
|                                           | subsequent invocations   |
|                                           | of install.sh it is not  |
|                                           | necessary to rebuild the |
|                                           | dependencies.            |
+-------------------------------------------+--------------------------+
| ``./install.sh``                          | Build library in your    |
|                                           | local directory. It is   |
|                                           | assumed dependencies     |
|                                           | have been built.         |
+-------------------------------------------+--------------------------+
| ``./install.sh -i``                       | Build library, then      |
|                                           | build and install        |
|                                           | hipBLAS package in       |
|                                           | `/opt/rocm/hipblas`. You |
|                                           | will be prompted for     |
|                                           | sudo access. This will   |
|                                           | install for all users.   |
|                                           | If you want to keep      |
|                                           | hipBLAS in your local    |
|                                           | directory, you do not    |
|                                           | need the -i flag.        |
+-------------------------------------------+--------------------------+


Build library dependencies + client dependencies + library + client
-------------------------------------------------------------------

The client contains executables in the table below.

=============== ====================================================
executable name description
=============== ====================================================
hipblas-test    runs Google Tests to test the library
hipblas-bench   executable to benchmark or test individual functions
example-sscal   example C code calling hipblas_sscal function
=============== ====================================================

Common uses of ``install.sh`` to build (dependencies + library + client) are
in the table below.

.. tabularcolumns::
   |\X{1}{4}|\X{3}{4}|

+-------------------------------------------+--------------------------+
| Command                                   | Description              |
+===========================================+==========================+
| ``./install.sh -h``                       | Help information.        |
+-------------------------------------------+--------------------------+
| ``./install.sh -dc``                      | Build library            |
|                                           | dependencies, client     |
|                                           | dependencies, library,   |
|                                           | and client in your local |
|                                           | directory. The -d flag   |
|                                           | only needs to be used    |
|                                           | once. For subsequent     |
|                                           | invocations of           |
|                                           | install.sh it is not     |
|                                           | necessary to rebuild the |
|                                           | dependencies.            |
+-------------------------------------------+--------------------------+
| ``./install.sh -c``                       | Build library and client |
|                                           | in your local directory. |
|                                           | It is assumed the        |
|                                           | dependencies have been   |
|                                           | built.                   |
+-------------------------------------------+--------------------------+
| ``./install.sh -idc``                     | Build library            |
|                                           | dependencies, client     |
|                                           | dependencies, library,   |
|                                           | client, then build and   |
|                                           | install the hipBLAS      |
|                                           | package. You will be     |
|                                           | prompted for sudo        |
|                                           | access. It is expected   |
|                                           | that if you want to      |
|                                           | install for all users    |
|                                           | you use the -i flag. If  |
|                                           | you want to keep hipBLAS |
|                                           | in your local directory, |
|                                           | you do not need the -i   |
|                                           | flag.                    |
+-------------------------------------------+--------------------------+
| ``./install.sh -ic``                      | Build and install        |
|                                           | hipBLAS package, and     |
|                                           | build the client. You    |
|                                           | will be prompted for     |
|                                           | sudo access. This will   |
|                                           | install for all users.   |
|                                           | If you want to keep      |
|                                           | hipBLAS in your local    |
|                                           | directory, you do not    |
|                                           | need the -i flag.        |
+-------------------------------------------+--------------------------+

Dependencies For Building Library
==================================

Dependencies are listed in the script ``install.sh``. Use ``install.sh`` with ``-d`` option to install dependencies.
CMake has a minimum version requirement listed in the file ``install.sh``. See ``--cmake_install`` flag in ``install.sh`` to upgrade automatically.

However, for the test and benchmark clients' host reference functions you must manually download and install AMD's ILP64 version of the AOCL libraries, version 4.1 or 4.0, from https://www.amd.com/en/developer/aocl.html.
The `aocl-linux-*` packages include AOCL-BLAS and AOCL-LAPACK.
If you download and install the full AOCL packages into their default locations then this reference LAPACK and BLAS should be found by the clients ``CMakeLists.txt``.
Note, if you only use the ``install.sh -d`` dependency script and change the default CMake option ``LINK_BLIS=ON``, you may experience `hipblas-test` stress test failures due to 32-bit integer overflow
on the host unless you exclude the stress tests via command line argument ``--gtest_filter=-*stress*``.


Manual build (all supported platforms)
=======================================

This section has useful information on how to configure ``cmake`` and manually build.


Build Library Using Individual Commands
---------------------------------------

.. code-block:: bash

   mkdir -p [HIPBLAS_BUILD_DIR]/release
   cd [HIPBLAS_BUILD_DIR]/release
   # Default install location is in /opt/rocm, define -DCMAKE_INSTALL_PREFIX=<path> to specify other
   # Default build config is 'Release', define -DCMAKE_BUILD_TYPE=<config> to specify other
   CXX=/opt/rocm/bin/hipcc ccmake [HIPBLAS_SOURCE]
   make -j$(nproc)
   sudo make install # sudo required if installing into system directory such as /opt/rocm


Build Library + Tests + Benchmarks + Samples Using Individual Commands
-----------------------------------------------------------------------

The repository contains source for clients that serve as samples, tests and benchmarks. Clients source can be found in the clients subdir.

Dependencies (only necessary for hipBLAS clients)
-------------------------------------------------

The hipBLAS samples have no external dependencies, but our unit test and benchmarking applications do. These clients introduce the following dependencies:

- `lapack <https://github.com/Reference-LAPACK/lapack-release>`_,  lapack itself brings a dependency on a fortran compiler
- `googletest <https://github.com/google/googletest>`_

Unfortunately, googletest and lapack are not as easy to install. Many distros do not provide a googletest package with pre-compiled libraries, and the lapack packages do not have the necessary cmake config files for cmake to configure linking the cblas library. hipBLAS provide a cmake script that builds the above dependencies from source. This is an optional step; users can provide their own builds of these dependencies and help cmake find them by setting the CMAKE_PREFIX_PATH definition. The following is a sequence of steps to build dependencies and install them to the cmake default /usr/local.

(optional, one time only)
-------------------------

.. code-block:: bash

   mkdir -p [HIPBLAS_BUILD_DIR]/release/deps
   cd [HIPBLAS_BUILD_DIR]/release/deps
   ccmake -DBUILD_BOOST=OFF [HIPBLAS_SOURCE]/deps   # assuming boost is installed through package manager as above
   make -j$(nproc) install

Once dependencies are available on the system, it is possible to configure the clients to build. This requires a few extra cmake flags to the library cmake configure script. If the dependencies are not installed into system defaults (like /usr/local ), you should pass the CMAKE_PREFIX_PATH to cmake to help find them.

.. code-block:: bash

   -DCMAKE_PREFIX_PATH="<semicolon separated paths>"
   # Default install location is in /opt/rocm, use -DCMAKE_INSTALL_PREFIX=<path> to specify other
   CXX=/opt/rocm/bin/hipcc ccmake -DBUILD_CLIENTS_TESTS=ON -DBUILD_CLIENTS_BENCHMARKS=ON [HIPBLAS_SOURCE]
   make -j$(nproc)
   sudo make install   # sudo required if installing into system directory such as /opt/rocm
