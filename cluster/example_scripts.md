---
sort: 3
---

# Example batch submission scripts

```warning
These scripts are provided as examples only.  Any values for resource requests (e.g., --ntasks, --mem) are not meant to be used as recommendations for any of the
software used in these examples.

See the section How do I determine how 
```

## Software running on 1 CPU

This script will submit a  job that runs on a single CPU.  This is not the best use of the cluster but is provided just as the most basic job script example.  
This job requests 1 CPU, and a total of 16GB of memory.  It will run on a single node.

```bash
#!/bin/bash

#SBATCH --job-name=neuron-test-job
#SBATCH --output=neuron_%j.out
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=auser@fadm.edu
#SBATCH --ntasks=1
#SBATCH --mem=16G

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

## Software running in parallel (i.e., multiple CPUs)

This script is similar to the one above except the code will be run using multiple CPUs (96 in total) in parallel.  Because of the directives, `--ntasks=96` and `-tasks-per-node=24`,
this job will execute on 4 nodes (96/24).  The memory request is specified as `--mem=16G` which means the job will reserve 16GB of memory **per node** which means in total it will reserve 64GB.
Finally, the actual research software uses `mpiexec -n 96` to break the computation into 96 individual chunks.  That value, 96, matches the number of CPUs requested via **#SBATCH --ntasks=96**.
You could have used a value smaller than 96 after the `-n` but not a larger value.  Doing so would cause your job to fail immediately because it is trying to use more resources
than what was requested.

```note
This example uses `mpiexec` to break the computations into chunks that can be run in parallel.  In other cases, the software might have command-line options
to run in parallel without the need for something like mpiexec.  However, not all software can be run in parallel.  You will need know the specifics of your
software package(s) to know if you can run your software this way
```

```bash
#!/bin/bash

#SBATCH --job-name=neuron-mpi-test-job
#SBATCH --ntasks=96
#SBATCH --tasks-per-node=24
#SBATCH --mem=16G
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

## Multiple software calls


## Multiple, independent, software calls in parallel

In this example, we are executing three different software packages which can be run at the same time (i.e. None of the packages rely on output from the others).
In order to achieve this there are some additional commands needed:

1. The use of `srun` which is a SLURM command to execute a single task
2. The use of `>`   followed by a filename (e.g. `> sim.out`) when running our programs.  This notation redirects any program output that would normally print to the terminal,
   to instead write to the file specified.  The system will automatically create that file if it does not exist. Notice too we did not include the `#SBATCH --output=` 
   that would normally capture that information because we want each software package to to write to different files making it much easier to distinguish the output 
   from the different programs.
3. The use of `2>&1` which specifies that any error will write to the same file as the one specified in #2 above
4. The final `&` tells the system to run the program as a background process.  This is important because otherwise the script would wait for one program to finish before 
   executing the next programsubmitting the next program to run.  
5. The use of `wait` after all program calls command tells the system to wait for all the programs to finish running before executing any other commands/completing the job.

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

## Multiple, similar software calls

This example uses a powerful feature of Slurm called job arrays in order to run the same piece of software multiple times, but with different data sets.
In this example we have decided to organize our data by placing  them in different directories making this example easier to run using job-array.
However, there are many other ways we could have organized things and still used job arrays.  More details and examples of ways to use job arrays can be found 
in [Slurm's job array documentation](https://slurm.schedmd.com/job_array.html)

For this example,

1. `--array=1-4` specifies the fact that we will be using a job array, specifically one that will include four jobs whose sub-job-id will be the values 1-4.
2. `${SLURM_ARRAY_TASK_ID}` is a Slurm specific variable that will take on the values specified by the `--array` directive.  Here we are using it to refer to 4 different directories, `sim1`, `sim2`, `sim3`, `sim4`
   The line in the script `cd sim${SLURM_ARRAY_TASK_ID}` will change into each subdirectory (sim1, sim2, sim3, sim4).  The system will automatically replace the `${SLURM_ARRAY_TASK_ID}` with the values 1,2,3,4.


```note
The values for `--nodes` and `--ntasks` (and other directives that might apply to physical resources) apply to each individual sub-job.  
This is especially important when doing parallel code runs using for example `mpiexec` because it specifies what resources each individual 
simulation is requesting (4 total nodes with each node needing 24 CPUs, 24GB available for use for this specific example).
```

```bash
#!/bin/bash

#SBATCH --job-name=neuron-mpi-multi-job
#SBATCH --nodes=1
#SBATCH --ntasks=24
#SBATCH --mem=24G
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

Job scripts which use software written to perform computations on a GPU will need to specifiy both of the following `#SBATCH` directives

- `--partition=gpus`
- `--gres`

### Using 1 GPU

In this example, we perform a single execution of the GPU-based software package, `heimdall`.
It specifies `#SBATCH --partition=gpus` to tell Slurm we need to use the set of nodes which have GPUs and also specified `#SBATCH --gres=gpu:1` to request
one GPU for computation.

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
                                                                                                                                     
heimdall -f test.fil                                                                                                                     
cat *.cand > beam01.cand                                                                                                                 
                                                                                                                                        
date         
```

### Using multiple GPUs

Sometimes software can use more than 1 GPU at a time split computations up.  Other times you may want to have multiples of the same software, each running on it's own GPU
(similar to the running multiple, independent, software example above).  This example demonstrates the latter scenario, where two independent and parallel calls to `heimdall` 
are being executed.  For this example, in addition to requesting a total of 2 GPUs(`--gres=gpu:2`), we've also specified explicitly that each task (each call to heimdall) 
should use 1 GPU(`--gpus-per-task=1`) and 1 CPU(`--ntasks=2`).

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

srun -n 1 heimdall -f test.1fil & 
srun -n 1 heimdall -f test2.fil &

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