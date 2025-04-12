Kernels
=======


Macro Variables
^^^^^^^^^^^^^^^

Formula for `Density` (array `m0`)
--------------------------

The density :math:`m0[idx]` is computed as the sum of the distribution function values over all directions (from 0 to :math:`Q-1`):

.. math::

   m0[idx] = \rho = \sum_{i=0}^{Q-1} s_i

Where:

- :math:`s_i = pf(idx, i)` is the distribution function for lattice direction :math:`i`.

Formula for `Velocity` ( array of 3D Vector `m1`)
-------------------------------------------------

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

Yaml example:

.. code-block:: yaml

  - macro_variables

Collision BGK
^^^^^^^^^^^^^

- Operator name: ``collision_bgk``
- Description: This operator implements the Bhatnagar-Gross-Krook (BGK) collision model for the Lattice Boltzmann Method (LBM). This model assumes a single relaxation time approach  to approximate the collision process, driving the distribution functions toward equilibrium.
- Parameters: No parameters but you need to define ``lbm_parameters``.
- Formula:

.. math::

   f_i(\mathbf{x} + \mathbf{e}_i, t + 1) = f_i(\mathbf{x}, t) - \frac{1}{\tau} \left( f_i(\mathbf{x}, t) - f_i^{\text{eq}}(\mathbf{x}, t) \right)

With:

- :math:`f_i(\mathbf{x}, t)` is the distribution function in the i-th direction at position `x` and time `t`,
- :math:`f_i^{\text{eq}}(\mathbf{x}, t)` is the equilibrium distribution function for the i-th direction at position `x` and time `t`,
- :math:`\tau` is the relaxation time parameter (LBMParameters).

Yaml example:

.. code-block:: yaml

  - collision_bgk

Streaming
^^^^^^^^^

The streaming step is divided into two parts (step1 and step2), and synchronization is required between these two steps to correctly update the ghost halos."

- Operator name: ``streaming``
- Description: TO DO
- Parameters:

  - ``asynchrone``: The asynchrone option controls the execution style: when true, it allows asynchronous operations with overlapping computation and communication, improving parallel performance. When false, it runs synchronously, ensuring sequential execution of operations and data updates.


Yaml example:

.. code-block:: yaml

  - streaming:
     asynchrone: false

.. note::

  ``asynchrone`` option is disabled.
