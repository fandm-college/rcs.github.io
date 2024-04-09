---
sort: 3
---

# Example batch submission scripts

```warning
These scripts are provided as examples only.  

- Any values for resource requests (e.g., --ntasks, --mem) are not meant to be used as recommendations for any of the
  software used in these examples.  See the section [How many resources do I request?](job_scripts#how-many-resources-do-i-request) for guidance.

- These examples are not meant to demonstrate how to actually run any particular piece of software.  It is up to you to know how your software runs
```

```tip
The examples below represent some of the more common situations as it applies to running software on the cluster.  Even though we have presented them as 
distinct examples here, you certainly can (and should) mix and match scenarios in a job script to match your specific workflow.
```

## Single software package, single CPU

This script demonstrates how to run one software package that uses only one CPU. This is essentially the most basic of job scripts.

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

# Change into the directory where my data is 
cd neuron

# Print the date/time to know when the software actually started processing
date

# Actually run the software
nrnivmodl ./
nrniv beginSimulation.hoc

# Print the date/time again to know when it ended
date
``` 

## Single software package, multiple CPUs

This script is similar to the one above except the software will run in parallel using multiple CPUs.  

- We did not specifically request `--ntasks`, the combination of `--nodes=4` and `-tasks-per-node=24` accomplishes the same thing
   - 4 nodes * 24 tasks per node
   - The combination of the directives, `--nodes=4` and `-tasks-per-node=24` means we are asking to evenly distribute 24 tasks across 4 nodes
- `--mem=16G` reserves 16GB of memory **per node** for a total of 64GB. 
- The software is running with `mpiexec -n 96` which break the computation into 96 individual chunks.
  - That value, 96, matches the total number of CPUs requested
  - You could have used a value smaller than 96 (if for example you wanted to do 2 separate runs of the software each using 48 CPUs)
  - You could not have used a value larger than 96 because that would exceed the number of CPUs you requested

```note
This example uses `mpiexec` to break the computations into chunks that can be run in parallel.  In other cases, software might have command-line options 
to run in parallel without the need for something like `mpiexec`.  However, not all software can be run in parallel.  You will need know the specifics of your 
software package(s) to know if you can run your software this way.
```

```bash
#!/bin/bash

#SBATCH --job-name=neuron-mpi-test-job
#SBATCH --nodes=4
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

# Go to the directory where my data files are
cd neuron_mpi

# Run the code
date

nrnivmodl ./
mpiexec -n 96 nrniv -mpi beginSimulation.hoc

date
```

## Multiple software packages in sequence

This example runs three different software applications, in sequence, one right after the other. This example would be similar  
to a workflow where one software package may produce output that another software package needs in order to run.  In this 
example, we set `--ntasks=1` even though we are running three software packages, because only one piece of software will be running 
at a time.  The assumption here is that each piece of software only runs using a single CPU.

```bash
#!/bin/bash

#SBATCH --job-name=astronomy-test-job
#SBATCH --output=pulsar_%j.out
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=auser@fadm.edu
#SBATCH --ntasks=1
#SBATCH --mem=24G

# Load necessary software
module purge
module load miniconda

# Load up first software package
eval "$(conda shell.bash hook)"
conda activate presto

# Go to the directory where our data files are
cd radio_pulse

date

# Run first piece of software
precand m44_9032123.fil

# Run 2nd piece of software
generate_cands -input prepped_data.txt -output_format=csv

# Load and run 3rd piece of software
conda deactivate
module purge
module load astropy
python eval_cands.py candidates.csv

date
```

## Multiple software packages in parallel

The example is very similar to the previous example with one major difference.  Instead of software packages being dependent on one another (as in the 
previous example), this example demonstrates how to run packages that do not depend on each other and therefore could possibly be running at the same time. 
Here we have `--ntasks=3` because three pieces of software will be running at the same time (again under the assumption that each piece of software only needs 1 CPU). 
It is up to you to understand your workflow and software packages to know if your specific job can be run this way or not.

There are several impoartant pieces in this example which are necessary in order to do the computations in parallel.

1. The use of `srun` which is a SLURM command to execute a single task
2. The use of `>`   followed by a filename (e.g. `> reaxff.out`) when running our programs.  This notation redirects any program output that would normally print to the terminal, 
   to instead write to the file specified.  The system will automatically create that file if it does not exist. Notice too we did not include the `#SBATCH --output=` 
   that would normally capture that information because we want each software package to to write to different files making it much easier to distinguish the output 
   from the different programs.
3. The use of `2>&1` which specifies that any error will write to the same file as the one specified in #2 above
4. The final `&` tells the system to run the program as a background process.  This is important because otherwise the script would wait for one program to finish before 
   executing the next programsubmitting the next program to run.  
5. The use of `wait` after all program calls command tells the system to wait for all the programs to finish running before executing any other commands/completing the job.
```bash
#!/bin/bash

#SBATCH --job-name=chemistry-test-job
#SBATCH --mail-type=END,FAIL
#SBATCH --mail-user=auser@fadm.edu
#SBATCH --ntasks=3
#SBATCH --mem=64G

module purge
module load ams2022

# Run the jobs
date

# Submit/run 1st job
srun reaxff bonds.in > reaxff.out 2>&1 &

# Submit/run 2nd job
srun adf molecules1.txt > molecules1.out 2>&1 &

# Submit/run 3rd job
srun ams -prepinput molecules2.txt > amsprep.out 2>&1 &

wait

date
```

## Same software in parallel

This example uses a powerful feature of Slurm called job arrays in order to run the same piece of software multiple times in parallel.  One use case for this 
example is where you may need to do the same computation multiple times but with different data sets. 
In this example we organized our data for each run by placing them in different directories to make the logic of the job-array simpler.
However, there are many other ways we could have organized things and still used job arrays.  More details and examples of ways to use job arrays can be found 
in [Slurm's job array documentation](https://slurm.schedmd.com/job_array.html)

For this example,

- `--array=1-4` specifies the fact that we will be using a job array
   - This will create for sub-jobs
   - Each individual sub-job id will be assigned a value 1,2,3,4.  This equates to the `%a` component in the `--output` directive
- `${SLURM_ARRAY_TASK_ID}` is a Slurm specific variable that will take on the values specified by the `--array` directive.
   - Here we are using it to refer to 4 different data directories, `sim1`, `sim2`, `sim3`, `sim4`
   - The line in the script `cd sim${SLURM_ARRAY_TASK_ID}` will change into each subdirectory (sim1, sim2, sim3, sim4).  
     The system will automatically replace the `${SLURM_ARRAY_TASK_ID}` with the values 1,2,3,4.

Then everything after the batch directives section in the script essentially gets treated as a loop that does the following:

1. Loads and initializes the software packages
2. Switches into the appropriate data
3. Runs the simulation software.  Because of how job arrays work, there's no need for the & as in the previous example in order to run parallel
4. Switches back to a base directory in prepartion for switching to the next data directory.

```note
The values for `--nodes` and `--ntasks` (and other directives that apply to physical resources) apply to **each individual** sub-job.  
This is especially important when doing parallel code runs using for example `mpiexec` because it specifies what resources each individual 
analysis is requesting (4 total nodes with each node needing 24 CPUs, 24GB available for use for this specific example).
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

# Run software on data
cd sim${SLURM_ARRAY_TASK_ID}
srun mpiexec -n 24 nrniv -mpi beginSimulation.hoc 
cd ..

date                          
```

## GPU jobs

Job scripts which use software written to perform computations on a GPU will need to specifiy both of the following `#SBATCH` directives

- `--partition=gpus`
- `--gres`

```warning
If you have a workflow that may use a combination of GPU processing and CPU processing, there is an additional consideration when it comes to `--ntasks`.  If you will need more than 40 CPUs 
for your workflow you will not be able to run the GPU software and CPU software packages in the same package because of how the cluster is setup.  You will 
have to create two different job scripts and submit both of them for processing.  You can submit both of them without waiting for the first one to finish as follows:

1. Submit the job file for that has to run first as usual with `sbatch`.  Make a note of the job id assigned to this job.
2. Submit the second job as follows: `sbatch myscript.job -d after:jobid1` where *jobid1* is the job id from the first job.  The addition of -d after:` indicates to the 
   job scheduler to run this job after the first job runs.
```

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

srun -n 1 heimdall -f test1.fil & 
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

The last line says we have a total of two tasks.  Here we use two srun with the *&* to start each individual heimdall run in the background (meaning the second srun command does not wait for the first one to finish before it starts).  The use of *wait* then waits for both heimdall jobs to complete before moving on. 