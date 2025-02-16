Performance
===========

In this section, we will highlight the current performance of the hipoLBM code on simple benchmarks. These results are bound to improve over time as we continue to optimize our products.

LBM Poiseuille
^^^^^^^^^^^^^^

Input File:

.. code-block:: bash

  do_domain:
    - domain:
       bounds: [[0,0,0],[0.1,0.1,0.1]]
       resolution: 200
       periodic: [true, true, false]

  set_lbm_parameters:
    - lbm_parameters:
       Fext: [9.512485e-05,0.000000e+00,0.000000e+00]
       nuth: 1e-3

  boundary_conditions:
    - neumann_z_l:
       U: [0.0,0,0]
    - neumann_z_0:
       U: [0.0,0,0]

  global:
     simulation_paraview_freq: -1
     simulation_end_iteration: 1000

LBMDEM3d (old version of the code):

LBMDEM3D 1 mpi x 1 openmp: 969.773610
LBMDEM3D 128 mpi x 1 openmp : 48.270874s

With SOA data storage:

TOPAZE/Milan:

Data: 02-15-25

- 1x1 1376.0s
- 128 threads: 72.35s 
- 128 mpi: 46.44s
- 16x8 mpixthreads: 33.52s

TOPAZE/a100:

Data: 02-15-25

- 1 mpi x 32 openmp x 1 a100: 13.54s
- 4 mpi x 32 openmp x 1 a100: 25.59s


With AOS data storage:


TOPAZE/Milan:

Data: 02-15-25

- 1 mpi x 1 openmp : 1214s
- 128 mpi x 1 openmp : 46.88s
- 16 mpi x 8 openmp : 41.07s
- 1 mpi x 128 openmp : 50.50s

TOPAZE/a100:

Data: 02-15-25

- 1 mpi x 32 openmp x 1 a100: 26.89s
