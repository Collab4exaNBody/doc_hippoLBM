Kernels
=======


Macro Variables
^^^^^^^^^^^^^^^

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
