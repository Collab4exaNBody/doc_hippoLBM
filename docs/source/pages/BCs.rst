Boundary Conditions
===================

In LBM simulations, boundary conditions must be defined within the ``boundary_conditions`` operator block. This block allows the specification of different types of boundary conditions applied to the simulation domain. It is possible to include multiple boundary conditions within the same boundary_conditions block. Each condition will be processed accordingly, ensuring accurate enforcement of flow properties at the domain boundaries.

Neumann conditions
^^^^^^^^^^^^^^^^^^

Both Neumann boundary conditions described below are applied through a single operator, ``neumann``, which can be applied on one or several boundary planes at once via the ``regions`` parameter (allowed values: ``plan_xy_0``, ``plan_xy_l``). The same prescribed velocity ``U`` is enforced on every region listed.

YAML example:

.. code-block:: yaml

   boundary_conditions:
     - neumann:
        U: [0.0,0,0]
        regions: [plan_xy_0, plan_xy_l]

Neumann Z 0
-----------

- Operator Name: ``neumann`` with ``regions: [plan_xy_0]``
- Description: This operator enforces a Neumann boundary condition at z = 0 in an LBM simulation. The Neumann boundary condition ensures that the gradient of the distribution function follows a prescribed value (``U{ux,uy,uz}``).

- Formula:

.. math::
   \rho = \frac{f_{0} + f_{1} + f_{2} + f_{3} + f_{4} + f_{7} + f_{9} + f_{10} + f_{8} + 2 (f_{6} + f_{13} + f_{12} + f_{17} + f_{16})}{1 - u_z}

.. math::
   n_{xz} = \frac{1}{2} (f_{1} + f_{7} + f_{9} - (f_{2} + f_{10} + f_{8})) - \frac{1}{3} \rho u_x

.. math::
   n_{yz} = \frac{1}{2} (f_{3} + f_{7} + f_{10} - (f_{4} + f_{9} + f_{8})) - \frac{1}{3} \rho u_y

.. math::
   f_{5} = f_{6} + \frac{1}{3} \rho u_z

.. math::
   f_{11} = f_{12} + \frac{1}{6} \rho (u_z + u_x) - n_{xz}

.. math::
   f_{14} = f_{13} + \frac{1}{6} \rho (u_z - u_x) + n_{xz}

.. math::
   f_{15} = f_{16} + \frac{1}{6} \rho (u_z + u_y) - n_{yz}

.. math::
   f_{18} = f_{17} + \frac{1}{6} \rho (u_z - u_y) + n_{yz}

- Parameters:

	- ``U``: Prescribed velocity at the boundary (z = 0), enforcing the Neumann condition.

YAML example:

.. code-block:: yaml

   boundary_conditions:
     - neumann:
        U: [0.001,0,0]
        regions: [plan_xy_0]


Neumann Z l
-----------

- Operator Name: ``neumann`` with ``regions: [plan_xy_l]``
- Description: This operator enforces a Neumann boundary condition at z = lz in an LBM simulation. The Neumann boundary condition ensures that the gradient of the distribution function follows a prescribed value (``U{ux,uy,uz}``).

- Formula:

.. math::
   \rho = \frac{f_{0} + f_{1} + f_{2} + f_{3} + f_{4} + f_{7} + f_{9} + f_{10} + f_{8} + 2 (f_{5} + f_{11} + f_{14} + f_{15} + f_{18})}{1 + u_z}

.. math::
   n_{xz} = \frac{1}{2} (f_{1} + f_{7} + f_{9} - (f_{2} + f_{10} + f_{8})) - \frac{1}{3} \rho u_x

.. math::
   n_{yz} = \frac{1}{2} (f_{3} + f_{7} + f_{10} - (f_{4} + f_{9} + f_{8})) - \frac{1}{3} \rho u_y

.. math::
   f_{6} = f_{5} - \frac{1}{3} \rho u_z

.. math::
   f_{13} = f_{14} + \frac{1}{6} \rho (-u_z + u_x) - n_{xz}

.. math::
   f_{12} = f_{11} + \frac{1}{6} \rho (-u_z - u_x) + n_{xz}

.. math::
   f_{17} = f_{18} + \frac{1}{6} \rho (-u_z + u_y) - n_{yz}

.. math::
   f_{16} = f_{15} + \frac{1}{6} \rho (-u_z - u_y) + n_{yz}

- Parameters:

	- ``U``: Prescribed velocity at the boundary (z = lz), enforcing the Neumann condition.

YAML example:

.. code-block:: yaml

   boundary_conditions:
     - neumann:
        U: [0.001,0,0]
        regions: [plan_xy_l]

Bounce Back
^^^^^^^^^^^

Wall / Surface conditions
-------------------------

The standard bounce-back boundary condition is used to enforce the no-slip condition at solid walls in Lattice Boltzmann Methods (LBM). It is implemented by reflecting the distribution functions at wall nodes back in the opposite direction.

Let:

- :math:`\mathbf{x}` be the position of a lattice node,
- :math:`\mathbf{c}_i = (e_{x,i}, e_{y,i}, e_{z,i})` be the discrete velocity in direction :math:`i`,
- :math:`\bar{i}` be the index of the opposite direction of :math:`i`,
- :math:`f_i(\mathbf{x}, t)` be the distribution function in direction :math:`i` at node :math:`\mathbf{x}` and time :math:`t`.

Then, for a node :math:`\mathbf{x}` marked as a wall, and for each direction :math:`i = 1, \dots, Q - 1`, the bounce-back condition is applied as:

.. math::

   f_i(\mathbf{x}, t + \delta t) = f_{\bar{i}}(\mathbf{x} + \mathbf{c}_i, t)

- Operator Name: ``wall_bounce_back``
- Description: The WallBounceBack class is described as part of the Lattice Boltzmann Method (LBM) implementation, specifically the wall bounce back steps.

YAML example:

.. code-block:: yaml

  pre_stream_bcs:
    - wall_bounce_back

`Cavity`
^^^^^^^^

This boundary condition models a **moving wall**, such as the lid in a lid-driven cavity flow. It is implemented through momentum injection at fluid nodes adjacent to the boundary.

FORMULA: ``TODO``

- Operator Name: ``cavity_z_0`` or ``cavity_z_l``
- Description: This operator enforces a Cavity boundary condition at z = lz in an LBM simulation. The Cavity boundary condition ensures that the gradient of the distribution function follows a prescribed value.
- Parameters:
	- ``U``: Prescribed velocity at the boundary (z = lz or z = 0), enforcing the Cavity condition.

YAML example:

.. code-block:: yaml

  pre_stream_bcs:
    - cavity_z_l:
       U: [0.0, 0.1, 0]

`Lid-Driven Cavity`
^^^^^^^^^^^^^^^^^^^

This boundary condition generalizes the ``Cavity`` condition above: instead of being restricted to the bottom or top Z plane, it can be applied on any of the six domain boundary planes through the ``regions`` parameter. At each fluid node adjacent to the selected plane(s), it enforces a moving wall (lid) by reinitializing the distribution functions to their equilibrium value at the prescribed velocity ``U``.

- Formula:

.. math::
   \rho = \sum_{i=0}^{Q-1} f_i

.. math::
   f_i = w_i \rho \left(1 + 3 (\mathbf{e}_i \cdot \mathbf{U}) + 4.5 (\mathbf{e}_i \cdot \mathbf{U})^2 - 1.5 \, \mathbf{U} \cdot \mathbf{U}\right)

- Operator Name: ``lid_driven_cavity``
- Description: This operator enforces a moving-wall (lid) boundary condition by reinitializing the distribution functions to their equilibrium value at the prescribed velocity ``U``, on one or several boundary planes given by ``regions``. It must be called after the bounce-back step, typically as part of ``post_stream_bcs``.
- Parameters:
	- ``U``: Prescribed velocity (real units) at the moving wall.
	- ``regions``: List of boundary planes on which the condition is applied. Allowed values: ``plan_xy_0``, ``plan_xy_l``, ``plan_xz_0``, ``plan_xz_l``, ``plan_yz_0``, ``plan_yz_l``.

YAML example:

.. code-block:: yaml

  post_stream_bcs:
    - post_bounce_back
    - lid_driven_cavity:  ## needs to be called after bounce back
       U: [0.1, 0.0, 0]
       regions: [plan_xy_l]
