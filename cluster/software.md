---
sort: 3
---

# Research software on the cluster

Because different software packages sometimes require conflicting dependencies, we will handle installing software.  You will then
access software in your job scripts using one of the following mechanisms:

1. Modules
2. Conda environments

Here we will briefly describe each of these mechanisms.

## Modules

Environment Modules (or more commonly just modules) is a system for dynamically loading software packages into your environment using a command line tool called `module`. Software packages are installed in a central, read-only location and then a modulefile is built for the software which modifies your environment when you load this module, so that the software becomes visible to you.


### Using modules in a job script
If your software is available via a module, you just add something like the following to your job script:

```bash
# Load newest version of a module
module load moduleName

# Load a specific version of a module
module load moduleName/version
```

```note
We recommend that in your job script you clear any loaded modules before loading other ones.  This can be done as `module purge`

For example:

module load heimdall
## RUN HEIMDALL

module purge

module load miniconda
## DO SOMETHING WITH MINICONDA
```

```note
Some modules require some additional lines to be added to the job script in order to use them

- Miniconda add the following line: `eval "$(conda shell.bash hook)"`
- Gaussian 16 add the following line (including the period at the beginning): `. $g16root/g16/bsd/g16.profile`
```

### Showing available modules

You can see what modules are available on the cluster using `module avail`  The output will look something like:

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

The (D) besides a module specifies the default version that will be loaded using `module load moduleName`.
For example, `module load gcc` will load GCC version 8.5 while `module load gcc/8.3.1` loads version 8.3.1 of GCC

## Conda environments

Conda is an open-source package and environment management system that was orginally designed for Python software management.  Most, but not all, of the scientific software 
loaded through Conda is written in Python.  

### Using Conda in a job script

The cluster uses Miniconda for software management which must be loaded using the `module` command (as described above) before it can used.
Once loaded, you can activate an environment as:

```bash
conda activate environmentName
```

replacing `environmentName` with the actual name of the environment (e.g. `conda activate neuron`)
Once the environment is activated, in your job script you run your code as you normally would.

You can deactivate the current Conda enviroment using the command `conda deactivate` (you do not need to use the environment name).

```note
Only a single Conda environemnt can be active at one time.  

If you try to activate another one using `conda activate`, the current environment will be deactivated meaning its software will not be available to run.
```

### Showing available Conda environments

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

The left column is the environment name.  That is the environment name you will use with the `conda activate` command. 
The right column is the path (i.e., folder) where that environment is located, but the actual location of the 
Conda environment is not important.  What matters most for your job scripts is the environment name.