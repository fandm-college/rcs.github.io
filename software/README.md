---
sort: 4
---

# Software

Because different scientific software packages sometimes require conficlifting dependencies, software is only available through three mechanisms:

1. Modules
2. Conda environments
3. Containers

## Modules

Environment Modules (or more commonly just modules) is a system for dynamically loading software packages into your environment using a command line tool called `module`. Software packages are installed in a central, read-only location and then a modulefile is built for the software which modifies your environment when you load this module, so that the software becomes visible to you.

This system makes it easy for everyone to find and load pre-built software into their environment to use with their programs, and allows multiple versions of the same software to coexist on the system together.

If your software is available through a module, you just add something like the following to your Slurm job script:

```bash
# Load newest version of a module
module load moduleName

# Load a specific version of a module
module load moduleName/version
```
**Note:**  In your Slurm script you should always clear any loaded modules before loading modules by using `module purge`

### Listing modules

You can list the available modules using `module avail`  The output will look something like:

```bash
------------------------------------------- /usr/share/modulefiles -------------------------------------------
   pmi/pmix-x86_64

---------------------------------------------- /opt/modulefiles ----------------------------------------------
   gcc/8.3.1        heimdall                 mpich/3.3.1-gcc-8.4.1    (D)    openmpi/3.1.4-gcc-8.4.1
   gcc/8.4.1        miniconda                mvapich2/2.3.2-gcc-8.3.1        openmpi/4.0.2-gcc-8.3.1
   gcc/8.5.0 (D)    mpich/3.3.1-gcc-8.3.1    openmpi/3.1.4-gcc-8.3.1         openmpi/4.0.2-gcc-8.4.1 (D)

  Where:
   D:  Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching any of the "keys".
```

In this output there are 7 different modules:

1. pmi
2. gcc
3. heimdall
4. miniconda
5. mpich
6. mvapich
7. openmpi

The (D) besides a module specifies the default version that will be loaded using `module load moduleName`.  For example, `module load gcc` will load GCC version 8.5 while `module load gcc/8.3.1` loads version 8.3.1 of GCC

## Conda environments

Conda is an open-source package management system and environment management system that was orginally designed for Python software management.  In fact most of the scientific software we've loaded through Conda is written in Python  We specifically use the Miniconda version of Conda for software management.

Accessing Conda environments requires specialized Conda initialization:

```bash
module load miniconda
eval "$(conda shell.bash hook)"
````

This loads the miniconda module and then initializes Conda so that the various Conda commands can be run.  Often these two lines will be part of your Slurm job script but you may also need to run them from the command line if you wish to interactively use Conda (e.g. to see which Conda environments are available)

Once Conda is active, you load an environment as:

```bash
conda activate environmentName
```

**Note:** Only a single Conda environemnt can be active at one time.  If you try to activate another one using `conda activate`, the current environment will be deactivated meaning its software will not be available.


### Listing Conda environments

Assuming Conda has been activated as described above, then you can list out all environments using `conda env list`.  It will output something like the following:

```bash
# conda environments:
#
base           *  /opt/apps/miniconda3
cudatoolkit       /opt/apps/miniconda3/envs/cudatoolkit
fetch             /opt/apps/miniconda3/envs/fetch
neuron            /opt/apps/miniconda3/envs/neuron
psrchive          /opt/apps/miniconda3/envs/psrchive
pysigproc         /opt/apps/miniconda3/envs/pysigproc
```

The left column is the environment name.  That is what you will use with `conda activate`.  The right column is the path (think folder) where that environment is.  In most cases the path does not matter, it's the environment name you will need.

## Containers
