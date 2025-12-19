Installation Guidelines
=======================

`HippoLBM` provides flexible installation methods to meet various user and developer needs. For user usage, the `Spack` (version `1.1.0`) package manager offers a straightforward installation process, ideal for user-only. However, for those who intend to develop or customize `HippoLBM`, `CMake` is recommended, as it supports both `CPU` and `GPU` configurations (`GPU` support is not available with `Spack`).

Choose the method below that best suits your setup and follow the instructions for a smooth installation experience.

Installation With CMake
^^^^^^^^^^^^^^^^^^^^^^^

Minimal Requirements
--------------------

The first step involves the installation of ``yaml-cpp``, which can be achieved using either the ``spack`` package manager or ``cmake``:

.. tabs::

   .. tab:: spack

      .. code-block:: bash

         spack install yaml-cpp@0.6.3
         spack load yaml-cpp@0.6.3

   .. tab:: apt-get install

      .. code-block:: bash

         sudo apt install libyaml-cpp-dev

   .. tab:: cmake 

      .. code-block:: bash

         export CURRENT_HOME=$PWD
         git clone --depth 1 --branch  yaml-cpp-0.6.3 https://github.com/jbeder/yaml-cpp.git
         mkdir ${CURRENT_HOME}/build-yaml-cpp && cd ${CURRENT_HOME}/build-yaml-cpp 
         cmake ../yaml-cpp/ -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=${CURRENT_HOME}/install-yaml-cpp -DYAML_BUILD_SHARED_LIBS=ON -DYAML_CPP_BUILD_TESTS=OFF
         make install -j 4
         cd ${CURRENT_HOME}
         export PATH_TO_YAML=$PWD/install-yaml-cpp

Please ensure to remove `yaml-cpp` and `build-yaml-cpp`. When installing ``Onika``, and ``hippoLBM``, remember to add the following to your cmake command: ``-DCMAKE_PREFIX_PATH=${PATH_TO_YAML}``.

To proceed with the installation, your system must meet the minimum prerequisites (``MPI`` and ``Onika``). The next step involves the installation of ``Onika``:

.. tabs::

   .. tab:: Ubuntu CPU

      .. code-block:: bash

         export CURRENT_HOME=$PWD
         git clone --branch v1.0.4 https://github.com/Collab4exaNBody/onika.git
         mkdir ${CURRENT_HOME}/build-onika && cd ${CURRENT_HOME}/build-onika
         cmake ${CURRENT_HOME}/onika -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=${CURRENT_HOME}/install-onika -DONIKA_BUILD_CUDA=OFF
         make install -j 10
         export onika_DIR=${CURRENT_HOME}/install-onika
         rm -rf ${CURRENT_HOME}/build-onika

   .. tab:: Ubuntu GPU

      Please, select the correct compute capability for your ``GPU`` for ``DCMAKE_CUDA_ARCHITECTURES`` instead of 86 in this example.

      .. code-block:: bash

         export CURRENT_HOME=$PWD
         git clone --branch v1.0.4 https://github.com/Collab4exaNBody/onika.git
         git clone --branch v2.0.4 https://github.com/Collab4exaNBody/exaNBody.git
         mkdir ${CURRENT_HOME}/build-onika && cd ${CURRENT_HOME}/build-onika
         cmake ${CURRENT_HOME}/onika -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=${CURRENT_HOME}/install-onika -DONIKA_BUILD_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES=86
         make install -j 10
         export onika_DIR=${CURRENT_HOME}/install-onika
         rm -rf ${CURRENT_HOME}/build-onika

   .. tab:: TOPAZE GPU

      Please, note that you need copy on topaze onika and exaNBody repository. You can load the ``YAML`` module such as ``module load yaml-cpp/`` and add this option to the ``cmake`` command: ``-DCMAKE_PREFIX_PATH=/ccc/products/yaml-cpp-0.6.3/system/default/``

      .. code-block:: bash

         module load gnu/13.2.0 cuda/12.4 mpi/openmpi/4.1.6 cmake/3.29.6
         cd $CCCSCRATCHDIR
         export CURRENT_HOME=$PWD
         // copy onika v1.0.4
         mkdir ${CURRENT_HOME}/build-onika && cd ${CURRENT_HOME}/build-onika
         cmake ${CURRENT_HOME}/onika -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=${CURRENT_HOME}/install-onika -DONIKA_BUILD_CUDA=ON -DCMAKE_CUDA_ARCHITECTURES=80
         make install -j 10
         export onika_DIR=${CURRENT_HOME}/install-onika
         rm -rf ${CURRENT_HOME}/build-onika

HippoLBM Installation
---------------------

To install ``HippoLBM``, follow these steps:

Set the exaNBody_DIR environment variable to the installation path. Clone the ``HippoLBM`` repository using the command:

.. code-block:: bash
		
   git clone https://github.com/Collab4exaNBody/hippoLBM.git

Create a directory named build-hippoLBM and navigate into it:

.. code-block:: bash
		
   mkdir build-hippoLBM && cd build-hippoLBM

Run CMake to configure the HippoLBM build:

.. tabs::

   .. tab:: cmake Minimal

      .. code-block:: bash
		
         cmake ../hippoLBM -DCMAKE_BUILD_TYPE=Release 
         make -j 4
         source bin/setup-env.sh


Launch examples / ctest
-----------------------

A set of minimal test cases can be run using the following command (non-regression test) in your `build` repository:

.. code-block:: bash

  ctest

or

.. code-block:: bash

  ./hippoLBM ../hippoLBM/example/lbm_poiseuille.msp 


You can also add hippoLBM to your bashrc by adding an alias (please, replace YOURDIR by your build directory): 

.. code-block:: bash

  vi ~/.bashrc
  source ~/YOURDIR/build/bin/hippoLBM
  alias hippoLBM='~/YOURDIR/build/hippoLBM'

Or just on your local environment:

  alias hippoLBM=$PWD/hippoLBM

Installing Spack
----------------

.. code-block:: bash

  git clone --depth=2 --branch=v1.1.0 https://github.com/spack/spack.git
  export SPACK_ROOT=$PWD/spack
  source ${SPACK_ROOT}/share/spack/setup-env.sh

Installing HippoLBM
-------------------

First get the ``spack`` repository in hippoLBM directory and add it to spack. It contains two packages: ``onika`` and ``hippolbm``:

.. code-block:: bash
		
   git clone https://github.com/Collab4exaNBody/spack-repos.git
   spack repo add spack-repos


Second install ``HippoLBM`` (this command will install ``cmake``, ``yaml-cpp``, and ``onika``).

.. code-block:: bash

  spack install hippolbm

