Kernels
=======

Macro Variables
^^^^^^^^^^^^^^^

Formula for `Density` (array named `m0`)
----------------------------------------

The density :math:`m0[idx]` is computed as the sum of the distribution function values over all directions (from 0 to :math:`Q-1`):

.. math::

   m0[idx] = \rho = \sum_{i=0}^{Q-1} s_i

Where:

- :math:`s_i = pf(idx, i)` is the distribution function for lattice direction :math:`i`.

Formula for `Velocity` (array of 3D Vector named `m1`)
------------------------------------------------------

The velocity components :math:`u_x`, :math:`u_y`, and :math:`u_z` are calculated as the weighted sum of the distribution functions and the corresponding lattice velocity components :math:`e_{x,i}`, :math:`e_{y,i}`, and :math:`e_{z,i}`:

.. math::

   u_x = \sum_{i=0}^{Q-1} s_i \cdot e_{x,i}, \quad u_y = \sum_{i=0}^{Q-1} s_i \cdot e_{y,i}, \quad u_z = \sum_{i=0}^{Q-1} s_i \cdot e_{z,i}

Where:

- :math:`s_i = pf(idx, i)` is the distribution function for lattice direction :math:`i`.
- :math:`e_{x,i}, e_{y,i}, e_{z,i}` are the components of the lattice velocities for direction :math:`i`.

After computing the velocity components, they are normalized by the density :math:`\rho` (if :math:`\rho \neq 0`):

.. math::

   u_x = \frac{u_x}{\rho}, \quad u_y = \frac{u_y}{\rho}, \quad u_z = \frac{u_z}{\rho}

Finally, the velocity vector :math:`m1[idx]` is stored as:

.. math::

   m1[idx] = \text{Vec3d}(u_x, u_y, u_z)

Where `Vec3d` represents the 3D velocity vector.


- Operator name: ``macro_variables``
- Description: A functor for computing macroscopic variables (densities and flux) for lattice Boltzmann method.
- Parameters: No parameters but you need to define ``lbm_parameters``.

YAML example:

.. code-block:: yaml

  - macro_variables

Collision BGK
^^^^^^^^^^^^^

- Operator name: ``bgk``
- Description: This operator implements the Bhatnagar-Gross-Krook (BGK) collision model for the Lattice Boltzmann Method (LBM). This model assumes a single relaxation time approach to approximate the collision process, driving the distribution functions toward equilibrium.
- Parameters: No parameters but you need to define ``lbm_parameters``.
- Formula:

.. math::

   f_i(\mathbf{x} + \mathbf{e}_i, t + 1) = f_i(\mathbf{x}, t) - \frac{1}{\tau} \left( f_i(\mathbf{x}, t) - f_i^{\text{eq}}(\mathbf{x}, t) \right)

With:

- :math:`f_i(\mathbf{x}, t)` is the distribution function in the i-th direction at position `x` and time `t`,
- :math:`f_i^{\text{eq}}(\mathbf{x}, t)` is the equilibrium distribution function for the i-th direction at position `x` and time `t`,
- :math:`\tau` is the relaxation time parameter (LBMParameters).

YAML example:

.. code-block:: yaml

  - bgk

Collision MRT
^^^^^^^^^^^^^

- Operator name: ``mrt``
- Description: This operator implements the Multiple-Relaxation-Time (MRT) collision model for the Lattice Boltzmann Method (LBM). This model assumes multiple relaxation times to approximate the collision process, driving the distribution functions toward equilibrium.
- Parameters: No parameters but you need to define ``lbm_parameters``.

YAML example:

.. code-block:: yaml

  - mrt

Streaming
^^^^^^^^^

The streaming step is divided into two parts (step1 and step2), and synchronization is required between these two steps to correctly update the ghost halos.

- Operator name: ``streaming``
- Description: TO DO
- Parameters:

  - ``asynchrone``: The asynchrone option controls the execution style: when true, it allows asynchronous operations with overlapping computation and communication, improving parallel performance. When false, it runs synchronously, ensuring sequential execution of operations and data updates.


YAML example:

.. code-block:: yaml

  - streaming:
     asynchrone: false

.. note::

  ``asynchrone`` option is disabled.

Utils
^^^^^

Initialization
--------------

``set_distribution``
~~~~~~~~~~~~~~~~~~~~

- Operator name: ``set_distribution``
- Description: Initializes all distribution function components :math:`f_i` uniformly to a given ``value``. Can be applied to the whole grid, restricted to an axis-aligned bounding box (``bounds``), or restricted to a quadric-defined region (``quadrics`` + optional ``transform``).
- Parameters:

  - ``value``: Uniform value assigned to every :math:`f_i` component (default: ``1.0``).
  - ``do_update``: If ``true``, operates on ``Real`` cells only and triggers a ghost-cell update; otherwise applies to all cells including ghost layers (default: ``false``).
  - ``bounds`` *(optional)*: Restricts initialization to an AABB ``[[xmin,ymin,zmin],[xmax,ymax,zmax]]``.
  - ``quadrics`` *(optional)*: Named quadric shape (``sphere``, ``cyly``, …) restricting the initialized region.
  - ``transform`` *(optional)*: Sequence of ``scale`` / ``translate`` transforms applied to the quadric.

.. warning::

   ``value`` is applied **uniformly to all** :math:`f_i` components — this is not a thermodynamically consistent initialization. Use ``set_dp_pressure`` for a physically meaningful pressure perturbation.

.. code-block:: yaml

   set_distributions:
     - set_distribution:
        value: 1.0              # initialize entire grid

     - set_distribution:
        value: 1.2
        quadrics: sphere
        transform:
          - scale:     [0.1, 0.1, 0.1]
          - translate: [0.3, 0.3, 1.5]

``set_dp_pressure``
~~~~~~~~~~~~~~~~~~~

- Operator name: ``set_dp_pressure``
- Description: Initializes a pressure perturbation :math:`\Delta p` (in Pa) relative to the fluid's reference state (:math:`\rho_\text{LBM} = 1`, i.e. ``avg_rho``). Unlike ``set_distribution``, :math:`\Delta p = 0` always leaves the fluid unperturbed regardless of ``dtLB``. The physical pressure is converted to an LBM density via the isothermal equation of state (:math:`p = c_s^2 \rho`, :math:`c_s^2 = 1/3`):

  .. math::

     \Delta p_\text{LBM} = \Delta p \cdot \frac{\Delta t^2}{\rho_\text{ref} \cdot \Delta x^2}, \qquad \rho_\text{LBM} = 1 + 3\,\Delta p_\text{LBM}

- Parameters:

  - ``delta_p`` *(required)*: Pressure difference in Pa relative to the reference state.
  - ``bounds`` *(optional)*: Restricts initialization to an AABB.
  - ``quadrics`` *(optional)*: Named quadric shape restricting the initialized region.
  - ``transform`` *(optional)*: Sequence of ``scale`` / ``translate`` transforms applied to the quadric.
  - ``verbosity``: If ``true``, prints the converted LBM density to the log (default: ``false``).

.. code-block:: yaml

   set_pressures:
     - set_dp_pressure:
        delta_p: 1.0e6          # 1 MPa perturbation
        quadrics: sphere
        transform:
          - scale:     [0.15, 0.15, 0.15]
          - translate: [1.0,  1.0,  1.0 ]
        verbosity: true

   set_distributions:
     - set_distribution:
        value: 1.0
     - set_pressures

The figure below shows the result of a 1 MPa spherical pressure perturbation expanding in a fully periodic water cube:

.. list-table::
   :widths: 60 40

   * - .. image:: ../_static/dp_pressure_sphere.png
          :width: 100%
     - .. image:: ../_static/dp_pressure_sphere.gif
          :width: 100%

Checkers
^^^^^^^^

Checkers are analysis operators, declared in the ``checker:`` block, that inspect the simulation state without modifying it (useful for regression testing or post-processing).

Check Macro Quantities
----------------------

- Operator name: ``check_macro_quantities``
- Description: This operator checks macroscopic quantities (sum of densities, minimum and maximum velocity norm) against user-defined bounds, and aborts the simulation if any check fails. It is mainly used for regression testing (CI).
- Parameters:

  - ``density``: Expected sum of all densities (optional, requires ``tol``).
  - ``tol``: Tolerance, relative to ``density`` (optional).
  - ``vmin``: Minimal velocity norm allowed (default: 0).
  - ``vmax``: Maximal velocity norm allowed (optional).

YAML example:

.. code-block:: yaml

  checker:
    - check_macro_quantities:
       density: 1e6
       tol: 1e-6
       vmin: 0
       vmax: 0.1
