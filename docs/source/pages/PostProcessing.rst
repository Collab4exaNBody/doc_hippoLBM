Post-Processing
===============

HippoLBM provides several operators for simulation output, analysis, and post-processing.
Output operators are typically triggered by frequency counters (``simulation_paraview_freq``, ``simulation_print_log_freq``, ``simulation_analysis_freq``) defined in the ``global`` block.

ParaView Output
---------------

- Operator name: ``write_paraview``
- Description: Writes the current simulation state (density, velocity, obstacle mask) to a ParaView-compatible file. Output files are placed under ``<output_directory>/ParaviewOutput/``. The optional ``distributions`` flag also dumps all :math:`f_i` components.
- Parameters:

  - ``filename``: base filename pattern, formatted with the current timestep (default: ``hippoLBM_%010d``).
  - ``output_directory``: base output directory (default: ``hippoLBMOutputDir``).
  - ``distributions``: if ``true``, the :math:`f_i` distribution functions are also written (default: ``false``).

.. code-block:: yaml

   - write_paraview:
      filename:          "hippoLBM_%010d"
      output_directory:  "MySimDir"
      distributions:     false

   global:
     simulation_paraview_freq: 500

Simulation State
----------------

- Operator name: ``simulation_state``
- Description: Computes global simulation statistics via MPI reduction: sum of density, minimum and maximum velocity norm. The result is stored in the ``simulation_statistics`` slot and consumed by ``log``.
- Parameters: none.

.. code-block:: yaml

   - simulation_state

Log
---

- Operator name: ``log``
- Description: Prints a one-line summary of the simulation state to the console: step number, physical time, total mesh size, sum of density, min/max velocity norm (in physical units), and performance in MLUPS (Million Lattice Updates Per Second). Requires ``simulation_state`` to have run first.
- Parameters:

  - ``print_log_header``: if ``true``, prints a column header before the first log line (default: ``true``).

.. code-block:: yaml

   - simulation_state
   - log

   global:
     simulation_print_log_freq: 100

Plane Velocity Profile
----------------------

- Operator name: ``plane_velocity_profile``
- Description: For each plane perpendicular to a chosen dimension (X, Y, or Z), computes the average, minimum, and maximum velocity norm over fluid nodes and writes the result to a CSV file with columns ``position avg min max``.
- Parameters:

  - ``dimension``: dimension along which to slice: ``"X"``, ``"Y"``, or ``"Z"``.
  - ``dump_file``: CSV filename pattern, formatted with the current timestep (default: ``profile_%010d.csv``).
  - ``output_directory``: base output directory (default: ``hippoLBMOutputDir``).

.. code-block:: yaml

  analysis:
    - plane_velocity_profile:
       dimension: "Z"
       dump_file: "profile_%010d.csv"

  global:
    simulation_analysis_freq: 300

Plot Line Velocity
------------------

- Operator name: ``plot_line_velocity``
- Description: Extracts the velocity profile along an axis-aligned line and writes it to a CSV file with columns ``rx ry rz ux uy uz`` (physical coordinates and velocity components in physical units). The reduction is performed via MPI; only the master rank writes the file, under ``<output_directory>/Profile/``.
- Parameters:

  - ``line`` *(optional)*: line endpoints in physical coordinates ``[[xmin,ymin,zmin],[xmax,ymax,zmax]]``.
  - ``line_lbm`` *(optional)*: line endpoints in LBM grid indices ``[[i0,j0,k0],[i1,j1,k1]]``. Mutually exclusive with ``line``.
  - ``dump_file``: CSV filename pattern, formatted with the current timestep (default: ``line_%010d.csv``).
  - ``output_directory``: base output directory (default: ``hippoLBMOutputDir``).

.. note::

   Exactly one of ``line`` or ``line_lbm`` must be specified. The line must be axis-aligned (two coordinates fixed, one varying).

.. code-block:: yaml

  analysis:
    - plot_line_velocity:
       line: [ [0.05, 0.05, 0.0], [0.05, 0.05, 0.1] ]
       dump_file: "line_%010d.csv"
       output_directory: "MySimDir"

  global:
    simulation_analysis_freq: 300
