Numerical Scheme
================

HippoLBM solves the incompressible Navier-Stokes equations via the Lattice Boltzmann Method (LBM).
The algorithm advances the distribution functions :math:`f_i(\mathbf{x}, t)` in two alternating steps — **collision** and **streaming** — repeated at each time iteration.

Initialization
--------------

Before the compute loop, the ``initialize`` block sets up the full simulation state:

.. list-table::
   :header-rows: 1
   :widths: 35 65

   * - Operator
     - Role
   * - ``domain``
     - MPI decomposition and grid allocation (see :doc:`Structures`)
   * - ``lbm_parameters``
     - Convert physical parameters (``nuth``, ``tau``, ``Fext``) to LBM units
   * - ``build_traversal``
     - Build the ``LBMGridRegion`` traversal regions (Real, Inside, Extend…)
   * - ``define_grid_3dq19``
     - Allocate the ``LBMFields<19>`` arrays (f, ρ, flux, obstacles)
   * - ``set_distribution``
     - Uniform initialization of all :math:`f_i` components
   * - ``init_obstacles`` / ``update_obstacles``
     - Mark solid nodes from registered walls, balls, or quadrics (see :doc:`Obstacles`)
   * - ``macro_variables``
     - Compute initial :math:`\rho` and :math:`\mathbf{u}` (see :doc:`Kernels`)

Parameters (``lbm_parameters``)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Physical parameters are converted to LBM units by the ``lbm_parameters`` operator:

.. code-block:: yaml

   - lbm_parameters:
      Fext:    [0.0, 0.0, 0.0]   # body force (physical units)
      nuth:    1e-4               # kinematic viscosity [m²/s]
      avg_rho: 1000.0             # reference density [kg/m³]
      tau:     0.7                # relaxation time (optional; exclusive with dt)

The relaxation time :math:`\tau` controls numerical viscosity: :math:`\nu = c_s^2\,(\tau - \tfrac{1}{2})\,\Delta t`.
If ``tau`` is omitted it is derived from ``nuth`` and the grid spacing ``dx``; alternatively ``dt`` can be specified directly, in which case ``tau`` is back-computed from it (``tau`` and ``dt`` are mutually exclusive).

.. note::

   Specifying ``dt`` directly is possible but not recommended. Fixing ``tau`` is the preferred approach, as it gives direct control over numerical stability and viscosity, independently of the grid resolution.

Compute Loop
------------

At each time step the following sequence is executed (defined in ``data/config/config_compute_loop.msp``):

.. list-table::
   :header-rows: 1
   :widths: 35 65

   * - Operator
     - Role
   * - ``macro_variables``
     - Compute :math:`\rho` and :math:`\mathbf{u}` from the current :math:`f_i` (see :doc:`Kernels`)
   * - ``collision`` (``bgk`` or ``mrt``)
     - Relax :math:`f_i` toward local equilibrium → post-collision :math:`f_i^*` (see :doc:`Kernels`)
   * - ``pre_stream_bcs``
     - Boundary conditions applied to :math:`f_i^*` **before** streaming (e.g. ``pre_bounce_back``, see :doc:`BCs`)
   * - ``streaming``
     - Propagate :math:`f_i^*` along each discrete velocity: :math:`f_i(\mathbf{x}+\mathbf{e}_i, t+1) = f_i^*(\mathbf{x},t)` (see :doc:`Kernels`)
   * - ``post_stream_bcs``
     - Boundary conditions applied **after** streaming (e.g. ``post_bounce_back``, ``lid_driven_cavity``, ``neumann``, see :doc:`BCs`)
   * - ``boundary_conditions``
     - Additional boundary conditions (e.g. ``wall_bounce_back``, see :doc:`BCs`)
   * - ``end_iteration``
     - I/O triggers: ParaView output, log, analysis
   * - ``next_time_step``
     - Increment the simulation time counter (:math:`t \leftarrow t + \Delta t`)

``pre_stream_bcs``, ``post_stream_bcs``, and ``boundary_conditions`` default to ``nop`` and are overridden in each simulation's YAML file.

.. tip::

   You can visualize the full operator execution graph by adding ``--debug-graph`` after your ``.msp`` file:

   .. code-block:: bash

      hippoLBM my_simulation.msp --debug-graph

Collision Models
^^^^^^^^^^^^^^^^

Two collision models are available (see :doc:`Kernels` for the full formulas):

- ``bgk``: Bhatnagar-Gross-Krook, single relaxation time :math:`\tau` — simple and efficient, default model.
- ``mrt``: Multiple Relaxation Time, one relaxation rate per moment — more stable at high Re, at the cost of a heavier kernel.

The active model is set via the ``collision`` key:

.. code-block:: yaml

   collision: bgk   # or mrt
