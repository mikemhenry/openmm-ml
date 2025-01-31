# OpenMM-ML

This is a high level API for using machine learning models in OpenMM simulations.  With just a few lines of code, you
can set up a simulation that uses a standard, pretrained model to represent some or all of the interactions in a system.

The current version is an early release.  The only supported potential functions are ANI-1ccx and ANI-2x.  They are
suitable for small molecules involving a limited set of elements and no charges.  Future releases will add new potential
functions that support a much wider range of molecules.

### Usage

To use this package, create a MLPotential object, specifying the name of the potential function to use.  You can then
call createSystem() to create a System object  for a simulation.  For example,

```python
from openmmml import MLPotential
potential = MLPotential('ani2x')
system = potential.createSystem(topology)
```

Alternatively, you can use createMixedSystem() to create a System where part is modeled with this potential and the rest
is modeled with a conventional force field.  As an example, suppose the Topology contains three chains.  Chain 0 is a
protein, chain 1 is a ligand, and chain 2 is solvent.  The following code creates a System in which the internal energy
of the ligand is computed with ANI2x, while everything else (including interactions between the ligand and the rest of
the System) is computed with Amber14.

```python
forcefield = ForceField('amber14-all.xml', 'amber14/tip3pfb.xml')
mm_system = forcefield.createSystem(topology)
chains = list(topology.chains())
ml_atoms = [atom.index for atom in chains[1].atoms()]
potential = MLPotential('ani2x')
ml_system = potential.createMixedSystem(topology, mm_system, ml_atoms)
```
