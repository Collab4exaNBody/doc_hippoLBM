HippoLBM Software
=================

.. image:: ../_static/logo_hippoLBM.png
   :align: center

Overview of HippoLBM
^^^^^^^^^^^^^^^^^^^^

``HippoLBM`` is an LBM simulation code based on the ``Onika`` runtime. This software is writen in ``C++`` and aims to provide an LBM tool that can be coupled with other physics such as ``DEM`` using ``exaDEM`` via ``Onika``. ``HippoLBM`` integrates a hybrid ``MPI`` and ``OpenMP`` parallelization on ``CPU`` or ``CUDA`` on ``GPU``. 



``HippoLBM`` is a high-performance Lattice Boltzmann Method (LBM) simulation code developed in ``C++`` and built on the ``Onika`` runtime. It is designed for large-scale fluid dynamics simulations and features a parallelization strategy to maximize computational efficiency. ``HippoLBM`` integrates hybrid ``MPI`` + ``OpenMP`` parallelism for ``CPU`` execution and ``CUDA`` acceleration for ``GPU`` architectures, ensuring scalability across different hardware configurations.

One of ``HippoLBM``’s key strengths is its ability to couple with other physics solvers, enabling multiphysics simulations. With a focus on modularity and extensibility, ``HippoLBM`` provides researchers and engineers with a flexible and efficient tool for tackling complex fluid dynamics problems in high-performance computing environments. 
