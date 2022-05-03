# Submitting jobs

Typically you will create a text file (refered to here as a job script) to specify the details of your job.  A job script is set of Linux commands paired with a set of resource requirements that can be passed to the Slurm job scheduler. Slurm will then generate a job according to the parameters set in the job script. Any commands that are included with the job script will be run within the job.  A job script will usually consist of four components:

- The first line must be `#!/bin/bash`
- Job resources and directives
- Load/initialize software
- Run the program(s)

Once you have created your script, you can submit your job using `sbatch`.  For example if your job script file is called *neuron.job* then you would submit your job as:

```bash
$ sbatch neuron.job
```
## Slurm batch directives

These directives specify resource requirements and other job information (e.g., job name). These directives must come after `#!/bin/bash` and before any commands are issued in the job script. Each directive contains a flag that requests a resource the job would need to complete execution. An sbatch directive always starts with `#SBATCH` and takes the general form:

```bash
#SBATCH --<flag>=<value>
```

For example, if you wanted to use only 2 nodes to run your job, you would include the following directive:

```bash
#SBATCH --nodes=2
```

| Type               | Description                                         | Flag                       | Example                    |
| :----------------- | :-------------------------------------------------- | :------------------------- | :------------------------- |
| Job Name           | Name your job so you can identify it in the queue   | --job-name=jobname         | --job-name=neuron-job      |
| Output file        | Specify a file that                                 | --output=file              | --output=myjob.out         |
| Sending email      | Receive email at beginning or end of job completion | --mail-type=type           | --mail-type=END, FAIL    |
| Email address      | Email address to receive the email                  | --mail-user=user           | --mail-user=auser@fadm.edu |
| Number of nodes    | The number of nodes needed to run the job           | --nodes=nodes              | --nodes=4                  |
| Number of tasks    | The ***total*** number of processes needed to run the job | --ntasks=processes   | --ntasks=96                |
| Tasks per node     | The number of processes you wish to assign to each node | --ntasks-per-node=processes | --ntasks-per-node=24   |
| Partition          | Specify a partition. Currently only needed if using a GPU | --partition=partition | --partition=gpus         |
| Generic resource | Specify a GRES. Currently only needed if using a GPU | --gres=gres              | --gres=gpu:tesla_v100     |
| Wall time          | The max amount of time your job will run for        | --time=wall time           |


A few comments about these directives.
- The directives for emailing are not currently implemented so do not include them (yet).  We will let you know when these are available.
- Not all job scripts need all of these directives.  We recommend always specifying the job name, output file, and emailing (when it becomes available).  
- If your job can be split up to run in paralell (e.g. using MPI), then you should specify at least the number of tasks and probably also tasks per node.  Each node currently has 40 CPUs so this value should not exced 40.  We also recommend you try to balance the number of tasks across nodes.  For example, if your job requires 96 total tasks, then you might set the tasks per node to 24 which will use 4 nodes.
- If your job uses a GPU, then you must specify both the partition and generic resource directives as given exactly in the examples above.
- For the output directive we recommend using one of the following two forms depending on your specific curcumstances:
  - `output=myfilename_%j.out`  Here the *%j* will be replaced by the job-id Slurm generates automatically.  So if the job id is 439 then the output would go to myfile_439.job.  This is useful to differentiate output files if you run the same job over and over again.
  - `output=myfilename_%A_%a.out`  Sometimes a job will have sub-jobs that get run (for example, running same simulation multiples times but each run uses different parameters).  In this case, *%A* refers to the job-id and *%a* refers to the sub-job-id.  For example if you ran 3 simulations, their sub-job-ids might be 1, 2, and 3.  Each one would have it's own output file, *myfile_439_1.out*, *myfile_439_2.out*, and *myfile_439_2.out*

A full list of commands [can be found in Slurm's documentation for sbatch.](https://slurm.schedmd.com/sbatch.html)

## Resource limits

In order to allow for equitable usage we have set the following limits for usage for various resources.  Note: These limits may change in the future as needs and usage change.  If you have a compute job that exceeds these limits, then email us, dorc@fandm.edu, and we will try to work with you to reserve the appropriate resources.

- /<ClusterName/> research cluster
  - **Max number of CPUs: 360**
  - **Max number of jobs submitted: 30**
- GPUs
  - **Max number of GPUs: 1**
  - **Max number of GPU jobs submitted: 4**

## Example submission scripts

### Single job, Single CPU
```bash
#!/bin/bash

# Set Slurm job directives
#SBATCH --job-name=neuron-test-job
#SBATCH --output=neuron_%j.out

# Turn on mail notification. There are five possible self-explaining values:
# NONE, BEGIN, END, FAIL, ALL (including all aforementioned)
# For more values, check "man sbatch"
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=auser@fadm.edu

# Load the necessary software
module purge
module load miniconda

eval "$(conda shell.bash hook)"
conda activate neuron
cd neuron

# Run the code.  Start by printing the date (to know when the job started)
date

nrnivmodl ./
nrniv beginSimulation.hoc

# Print the date again to know when it ended
date
```

This script will submit a single job that runs on a single CPU.  For such a simple case there really aren't many requirements when it comes to the Slurm directives.  It will email the user when the job completes (or if the job failed).  More information regarding software can be found in the [software section](../software/README.md) of the documentation.

### Single parallel job using MPI

```bash
#!/bin/bash

# Set Slurm job directives
#SBATCH --job-name=neuron-mpi-test-job
#SBATCH --ntasks=96
#SBATCH --tasks-per-node=24
#SBATCH --output=neuron_mpi_%j.out
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=auser@fadm.edu

# Load the necessary software
module purge
module load miniconda
module load openmpi

eval "$(conda shell.bash hook)"
conda activate neuron
cd neuron_mpi

# Run the code
date

nrnivmodl ./
mpiexec -n 96 nrniv -mpi beginSimulation.hoc

date
```

This script is similar to the one above except the code will be run using multiple CPUs (96 in total) in parallel.  Not all software can be run in parallel like this.  It will depend on the software.  Because this example runs a single job, the number of CPUs MPI uses (mpiexec **-n 96**) cannot exceed the number of tasks (**#SBATCH --ntasks=96**)

### Multiple single CPU jobs

```bash
#!/bin/bash

#SBATCH --job-name=neuron-test-job

module purge
module load miniconda

eval "$(conda shell.bash hook)"
conda activate neuron
cd neuron

# Run the jobs
date

# Submit/run 1st job
nrnivmodl ./
srun nrniv beginSimulation.hoc > sim.out 2>&1 &

# Submit/run 2nd job
srun python eval_cands.py candidates.csv > eval.out 2>&1 &

# Submit/run 3rd job
srun ./generateCands -o randomData.txt > gen.out 2>&1 &

wait

date
```

The real power of Slurm is the ability to submit and run multiple jobs at once assuming the necessary resources (e.g., number of CPUs) are available.  In this example, we are submitting three different single CPU jobs to run to run at the same time.

The first thing to notice is the use of *srun* before the program calls.  This is a SLURM command to submit a single run.

The second thing to notice is the use of *>* followed by a filename (e.g. *> sim.out*) when running our programs.  Notice too we did not include the *#SBATCH --output=*.  What is happening here is that each program is redirecting output to it's own file.  This makes it much easier to distinguish the output from the different programs.

Finally each run uses *2>&1 &*.  This does two things.  First, any errors will print to the same file used for output.  Second, the job will run as a background process.  This is important because otherwise the script would wait for the job to finish before submitting the next program to run.  The use of the *wait* command tells the system to wait for all the programs to finish running before finishing the job.
