Boundary Conditions
===================

In LBM simulations, boundary conditions must be defined within the boundary_conditions operator block. This block allows the specification of different types of boundary conditions applied to the simulation domain. It is possible to include multiple boundary conditions within the same boundary_conditions block. Each condition will be processed accordingly, ensuring accurate enforcement of flow properties at the domain boundaries.

Neumann conditions
^^^^^^^^^^^^^^^^^^

Neumann Z 0
-----------

- Operator Name: ``neumann_z_0``
- Description:  This operator enforces a Neumann boundary condition at z = lz in an LBM simulation. The Neumann boundary condition ensures that the gradient of the distribution function follows a prescribed value (``U{ux,uy,uz}``).

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

Yaml example:

.. code-block:: yaml

   boundary_conditions:
     - neumann_z_0:
        U: [0.001,0,0]


Neumann Z l
-----------

- Operator Name: ``neumann_z_l``
- Description:  This operator enforces a Neumann boundary condition at z = lz in an LBM simulation. The Neumann boundary condition ensures that the gradient of the distribution function follows a prescribed value (``U{ux,uy,uz}``).

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

Yaml example:


.. code-block:: yaml

   boundary_conditions:
     - neumann_z_l:
        U: [0.001,0,0]

