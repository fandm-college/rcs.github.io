# Submitting jobs

Typically you will create a text file (refered to here as a job script) to specify the details of your job.  The job script typically contains two key sections

1. A set of parameters/resources (e.g., # CPUs) needed to run the job
2. Various commands to load/initialize and run the software

Slurm will then schedule and run the job according to the parameters set in the job script when the needed resources become available. 

## Basic job script template

Below is a basic job script template.  Importantly the first line in the file must be `#!/bin/bash`.  After the first line are a series of lines all beginning with #SBATCH.  These are parameters
that specify information about the job like the name of the job and the computational resources needed.  These directives always begin with `#SBATCH` and are covered in more detail in the next section.  
After these directives comes the commands to load and run the necessary software.  More details specific to software can be found [here](../software/README.md).

```bash
#!/bin/bash

#SBATCH --job-name=sample_chemistry-job
#SBATCH --output=chem.out
#SBATCH --ntasks=16
#SBATCH --ntasks-per-node=4
#SBATCH --mem=12G

# Now that resources and such are specified actually setup and run software
module purge
module load mychemistrysoftware

srun computeChemistry inputFile.txt
```

Once you have created your script, you can submit your job using the `sbatch` command via the terminal.  For example if your job script file is called *chem.job* then you would submit your job as:

```bash
$ sbatch chem.job
```

Note that your job may or may not begin to run immediately.  It will depend on whether or not the requested resources (e.g., CPUs, memory, nodes) are available.

## Slurm batch directives

These directives specify resource requirements and other job information (e.g., job name). These directives must come after `#!/bin/bash` and before any commands are issued in the job script. 
Each directive takes the general form:

```bash
#SBATCH --<flag>=<value>
```

For example, if you wanted to use only 2 nodes to run your job, you would include the following directive:

```bash
#SBATCH --nodes=2
```

Below is a table of some of the more commonly used directives.  Note that you don't need to specify all of these directives in your job script.
A full list of directives and other options [can be found in Slurm's documentation for sbatch.](https://slurm.schedmd.com/sbatch.html)

| Flag                | Description                                         | Example                    |
| :------------------ | :-------------------------------------------------- | :------------------------- |
| --job-name          | Name your job so you can identify it in the queue   | --job-name=neuron-job      |
| --output            | Specify a file that                                 | --output=myjob.out         |
| --mail-type         | Get email when job starts/completes                 | --mail-type=END, FAIL      |
| --mail-user         | Email address to receive the email                  | --mail-user=auser@fadm.edu |
| --nodes             | The number of nodes needed to run the job           | --nodes=4                  |
| --ntasks            | The ***total*** number of CPUs needed to run the job | --ntasks=96               |
| --ntasks-per-node   | The number of processes you wish to assign to each node | --ntasks-per-node=24   |
| --mem-per-cpu       | Amount of memory needed on a per CPU basis          | --mem-per-cpu=16G          |
| --mem               | Amount of memory needed on a per Node basis         | --mem=16G                  |
| --partition         | Specify a partition. Currently only needed if using a GPU | --partition=gpus     |
| --gres              | Specify a GRES. Currently only needed if using a GPU | --gres=gpu:1              |

- The directives for emailing (`mail-type` and `mail-user`)are not currently setup so do not include them (yet).  We will let users know when these are working.
- Not all job scripts need all of these directives.  In almost all cases you should specify:
  - `job-name`
  - `output`
  - `ntasks`
  - Either `nodes` or `ntasks-per-node`

- For software that runs on a GPU you will also need to specify
  - `partition`, specifically `--partition=gpus`
  - `gres` will usually be in the form: `gres=gpu:1`  The number after the colon is the number of GPUs needed.  In almost all cases the value should be 1

## --output directive

Some software packages usually output messages and errors directly to the terminal as they run.  The `output` directive specifies a file name where such messages will be written to instead of being dispalyed to the terminal.  It **does not** specify the name of output file(s) which your particular program may use to capture output.  Those files still need to be specified as you normally would when running the software.

For the `output` directive we recommend using one of the following two forms depending on your specific curcumstances:

- `output=myfilename_%j.out`  Here the `%j` will be replaced by the job-id Slurm generates automatically.  So if the job-id is 439 then the output would go to `myfilename_439.job`.  
  This will make it easier to differentiate output files if you run the same job script over and over again.

- `output=myfilename_%A_%a.out`  Sometimes a job will have sub-jobs that get run (for example, running the same simulation multiples times for the same job script but each run uses different parameters).  
   In this case, `%A` refers to the job-id and `%a` refers to the sub-job-id.  For example if you ran 3 simulations, their sub-job-ids might be 1, 2, and 3 producing output files, `myfilename_439_1.out`, `myfilename_439_2.out`, and `myfilename_439_2.out`