# Example batch submission scripts

One thing to note is that these examples are relatively simple and are meant only as examples(i.e., don't necessarily take the recommendations on values for --ntasks into consideration).  In addition, some pieces of software (notably Miniconda and Gaussian) require some additional steps in the job script in order to use them.  These steps and more general information about the software loaded on the cluster can be found in the [software section](../software/README.md) of the documentation.

## Single job using 1 CPU

This script will submit a single job that runs on a single CPU.  Please note that this is not the best use of the cluster but is provided just as a siimple example.  In this case it is using the email directives, but
will only email when the job completes or if it fails.

```bash
#!/bin/bash

# Set Slurm job directives
#SBATCH --job-name=neuron-test-job
#SBATCH --output=neuron_%j.out

# Turn on mail notification. There are five possible values:
# NONE, BEGIN, END, FAIL, ALL (including all aforementioned)
# For more values, check "man sbatch"
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=auser@fadm.edu

#SBATCH --ntasks=1

# Load the necessary software
module purge
module load miniconda

# Necessary step in order to use miniconda which this setup for neuron requires
eval "$(conda shell.bash hook)"
conda activate neuron
cd neuron

# Run the code.  Start by printing the date (to know when neuron actually started running)
date

nrnivmodl ./
nrniv beginSimulation.hoc

# Print the date again to know when it ended
date
``` 

## Single parallel job using MPI

This script is similar to the one above except the code will be run using multiple CPUs (96 in total) in parallel.  Not all software can be run in parallel like this.  It will depend on the software.  Because this example runs a single job, the number of CPUs MPI uses (mpiexec **-n 96**) cannot exceed the number of tasks (**#SBATCH --ntasks=96**)

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
srun mpiexec -n 96 nrniv -mpi beginSimulation.hoc

date
```

## Multiple jobs each using 1 CPU

The real power of Slurm is the ability to submit and run multiple jobs at once assuming the necessary resources (e.g., number of CPUs) are available.  In this example, we are submitting three different single CPU jobs to run at the same time (hence `--ntasks=3`).

The first thing to notice is the use of *srun* before the program calls.  This is a SLURM command to submit a single run.

The second thing to notice is the use of *>* followed by a filename (e.g. *> sim.out*) when running our programs.  Notice too we did not include the `#SBATCH --output=`.  What is happening here is that each program is redirecting output to it's own file.  This makes it much easier to distinguish the output from the different programs.

Finally each run uses *2>&1 &*.  This does two things.  First, any errors will print to the same file used for output.  Second, the job will run as a background process.  This is important because otherwise the script would wait for the job to finish before submitting the next program to run.  The use of the *wait* command tells the system to wait for all the programs to finish running before finishing the job.

```bash
#!/bin/bash

#SBATCH --job-name=neuron-test-job
#SBATCH --ntasks=3

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

## Multiple similar jobs

This example uses a powerful feature of Slurm called job arrays.  Some explanation here is needed

1. `--array=1-4` specifies the fact that we will be using a job array, specifically one that will include four jobs whose sub-job-id will be the values 1-4.
2. `${SLURM_ARRAY_TASK_ID}` is a Slurm specific variable that will take on the values specified by the `--array` directive.  Here we are using it to refer to 4 different directories, `sim1`, `sim2`, `sim3`, `sim4`
3. The values for `--nodes` and `--ntasks` (and other directives that might apply to physical resources) apply to each individual sub-job.  This is important for example when doing parallel code runs using MPI.  Here each individual simulation is requesting 24 CPUs on a single node.

In this simulation we have decided to differentiate the runs by placing the appropriate pieces in different directories but theare are many other ways you could setup your job.  More details and examples can be found in [Slurm's job array documentation](https://slurm.schedmd.com/job_array.html)

```bash
#!/bin/bash

#SBATCH --job-name=neuron-mpi-multi-job
#SBATCH --nodes=1
#SBATCH --ntasks=24
#SBATCH --output=neuron_mpi_%A_%a.out
#SBATCH --array=1-4

module purge
module load miniconda
module load openmpi

eval "$(conda shell.bash hook)"
conda activate neuron

date

cd sim${SLURM_ARRAY_TASK_ID}
nrnivmodl ./
srun mpiexec -n 24 nrniv -mpi beginSimulation.hoc
cd ..

date                          
```

## GPU jobs

In this example, the `heimdall` program runs on a GPU.  We have specified `#SBATCH --partition=gpus` to tell Slurm we need to use the set of nodes which have GPUs and we also specified `#SBATCH --gres=gpu:1` to indicate a single GPU to use.

```bash
#!/bin/bash                                                                                                                              
#SBATCH --job-name=heimdall-test-job
#SBATCH --ntasks=2                                                                                                     
#SBATCH --partition=gpus                                                                                                                 
#SBATCH --gres=gpu:1                                                                                                          
#SBATCH --output=heimdall_test_%j.out                                                                                                    
                                                                                                                                     
module purge                                                                                                                             
module load heimdall                                                                                                     

date
                                                                                                                                     
srun heimdall -f test.fil                                                                                                                     
cat *.cand > beam01.cand                                                                                                                 
                                                                                                                                        
date         
```

If you wish to use more than one GPU, the batch script is slightly more complicated.  Below is an example that runs two instances of a program called heimdall on two different GPUS

```bash
#SBATCH --job-name=heimdall-test-job
#SBATCH --partition=gpus
#SBATCH --gres=gpu:2
#SBATCH --gpus-per-task=1
#SBATCH --cpus-per-task=2
#SBATCH --ntasks=2
#SBATCH --output=heimdall_test_%j.out

module purge
module load heimdall

date

srun -n 1 heimdall -f test.fil & 
srun -n 1 heimdall -f test.fil &

wait

date
```

As in the above example, we are using the GPU partition but instead of 1 GPU we are requesting 2 (`--gres=gpu:2`).  Most important in this example are the lines

```bash
#SBATCH --gpus-per-task=1
#SBATCH --cpus-per-task=2
#SBATCH --ntasks=2
```

The first line indicates that we want 1 gpu per task.  In most (but not all) cases, a program that uses GPUs will only be able to use a single GPU at a time.

The second line says we need two CPUs per task.  In this example, two is an arbitrary value.  It could be 1 or some other small number.  The important part is that you do specify the number of CPUs per task and not make it large.  By default (that is if left unspecified) the default CPUs per task is all of the CPUs on a node.  If you don't specify CPUs per task the first GPU job will start to execute, and be given all the CPUs meaning the second GPU job can't execute becuase it's waiting on CPUs even though the GPU is available.

Further, If the CPUs per task combined with the number of tasks is greater than the number of CPUs on a node (40 in this case) then the job won't run correctly.  For example if the CPUs per task in this example was 22 instead of just 2, then once again the first GPU job will start to execute, and be given 22  CPUs leaving only 18 available.  The second GPU job can't execute becuase it's waiting on CPUs even though the GPU is available.

The last line says we have a total of two tasks.  Here we use two srun with the *&* to start each individual heimdall run in the background (meaning the second srun command does not wait for the first one to finish before it starts).  The use of *wait* then waits for both heimdall jobs to complete before moving on. 