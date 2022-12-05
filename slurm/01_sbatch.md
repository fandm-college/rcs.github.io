# Submitting jobs

Typically you will create a text file (refered to here as a job script) to specify the details of your job.  A job script is set of Linux commands paired with a set of resource requirements that can be passed to the Slurm job scheduler. Slurm will then run job according to the parameters set in the job script when the needed resources become available (more on that in Resource Recommendation ). Any commands that are included with the job script will be run within the job.  A job script will usually consist of four components:

- The first line must be `#!/bin/bash`
- Job resources and directives
- Load/initialize software
- Run the program(s)

Once you have created your script, you can submit your job using `sbatch`.  For example if your job script file is called *neuron.job* then you would submit your job as:

```bash
$ sbatch neuron.job
```
## Slurm batch directives

These directives specify resource requirements and other job information (e.g., job name). These directives must come after `#!/bin/bash` and before any commands are issued in the job script. Each directive contains a flag that requests a resource the job needs to run. An sbatch directive always starts with `#SBATCH` and takes the general form:

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
| Number of tasks    | The ***total*** number of CPUs needed to run the job | --ntasks=processes   | --ntasks=96                |
| Tasks per node     | The number of processes you wish to assign to each node | --ntasks-per-node=processes | --ntasks-per-node=24   |
| Memory per CPU     | Amount of memory needed on a per CPU basis          | --mem-per-cpu=amount       | --mem-per-cpu=16G         |
| Memory per Node    | Amount of memory needed on a per Node basis         | --mem=amount               | --mem=16G                 |
| Partition          | Specify a partition. Currently only needed if using a GPU | --partition=partition | --partition=gpus         |
| Generic resource   | Specify a GRES. Currently only needed if using a GPU | --gres=gres:#              | --gres=gpu:1     |
| Wall time          | The max amount of time your job will run for        | --time=wall time           |

**A few comments about these directives.**

- The directives for emailing are not currently setup so do not include them (yet).  We will let users know when these are working.
- Not all job scripts need all of these directives.  In most cases you will specify:
  - job name
  - output file (See below for more details on output file)
  - Number of tasks (--ntasks=)
  - Number of nodes (--nodes=) OR Tasks per node (--ntasks-per-node)
  - email directives

- For software that runs on the GPU you will need to specify
  - partition (A **partition** is just a group of related resources like GPUs)
  - generic resource (which will usually be in the form: `gres=gpu:1`  The number after the colon is the number of GPUs needed.  In almost all
    cases the value should be 1

**Output files**

Some software packages usually output messages and errors directly to the terminal as they run.  The `output` directive specifies a file name where such messages will be written to instead of being logged to the terminal.  It **does not** specify the name of output file(s) which your particular program may use to capture output for its calculations.  Those still need to be specified as you normally would when running the code.

For this directive we recommend using one of the following two forms depending on your specific curcumstances:
- `output=myfilename_%j.out`  Here the `%j` will be replaced by the job-id Slurm generates automatically.  So if the job id is 439 then the output would go to `myfilename_439.job`.  This is useful to differentiate output files if you run the same job script over and over again.
- `output=myfilename_%A_%a.out`  Sometimes a job will have sub-jobs that get run (for example, running the same simulation multiples times but each run uses different parameters).  In this case, `%A` refers to the job-id and `%a` refers to the sub-job-id.  For example if you ran 3 simulations, their sub-job-ids might be 1, 2, and 3 producing output files, `myfilename_439_1.out`, `myfilename_439_2.out`, and `myfilename_439_2.out`

A full list of directives and other options [can be found in Slurm's documentation for sbatch.](https://slurm.schedmd.com/sbatch.html)

## Recommendations

It is important to have a basic understanding of how the job scheduler chooses  when to run a certain job.  It is not in first-in first-out manner, and so just because there may be many jobs ahead of your newly submitted job, it doesn't necessarily mean all of the jobs will run before yours gets to run.  Instead, the scheduler chooses jobs based on several factors.  These factors include, but are not limited to

- Availability of necessary resources
- How long a job has been in the queue waiting to run
- How many jobs an individual user has been runing and for how long

### Recommendation 1: When ntasks is 20 or greater

If the number of tasks is 20 or greater, then we strongly recommend also using the --nodes  or --ntasks-per-node directive to use more than one node.  The short reason (without getting into too many technical details) is that
it makes the task of scheduling jobs a little bit easier.  When, only --ntasks is specified, we have seen issues where jobs have been held in the queue indefinitely until we released the job manually.

### How do I determine the value for ntasks (i.e., how many CPUs do I need)?

In terms of choosing an actual value for the number of tasks, this will depend a great deal on your exact circumstances especially as it relates to how your software runs.  Some software is designed to essentially only run using a single CPU.  In such cases you may want to have a job that runs multiple instances of the program, each one with different inputs and so ntasks=12 or less (depending on the exact number) is likely to be sufficient.

This is also a case where specifically setting either --nodes or --ntasks-per-node option may not be useful/desired.  If for example, your are running only 3 or 4 instances of your software then letting the scheduler find a few nodes with one or two idles CPUs to use may be better in terms of getting the job through the queue faster because there are always likely to be at least a few nodes with a small number of CPUs not already dedicated for other jobs.
 
On the other hand, if your software can perform calculations in parallel (using perhaps MPI or OpenMPI), then setting ntasks=64 or perhaps larger will be the way to go.  One thing to note about software that runs in parallel is that there is usually a point where using more CPUs doesn't actually speed things up anymore.  In fact this can often lead to CPUs sitting idle doing resulting again in less than optimal utilization.  Sometimes the software may give guidelines where this point is but usually not.  In most cases we would say that 32 to 64 CPUs will be sufficient and probably no more than 72 CPUs at a maximum.

If your software can use a GPU then the number of tasks will vary.  In many cases only or maybe 1 or 2 CPUs is needed because most of the calculations occur on the GPU itself.  In some other cases though, there may be some kind of splitting between calculations that can be done of the GPU and those that cannot and so a greater number of CPUs (e.g., 8) may be the way to go.  Also, when using the GPU, the value for ntasks may not exceed 40.  We currently only havea single node with GPUs and that node only has 40 CPUs available to use.

**Importantly we aren't asking researchers to try and find the exact "right" value.  What we are asking is that researchers not grossly over-request the number of CPUs for a job so that as many jobs as possible can run.

### Recommendation 2: Memory

By default the total memory assigned to a job is 4.75GB * # of CPUs requested for the job.  For example if your job requests 10 CPUs then it will be assigned roughly 48 GB of memory.  In most cases the default may be fine.  However, if your jobs are failing due to OUT OF MEMORY errors then you should specify either the --mem or --mem-per-cpu option.  Of the two, the --mem option is probably easier to deal with as it specifies the amount of memory on a per node basis.  Each node has a maximum of 190GB of available memory.  Very few jobs will require that much memory.  In addition, if you request that much memory for you job, then no other jobs can use that node for computations even if there are available CPUs.

We recommend requesting memory in small increments.  For example, if your initial job was set to run using 20 CPUs (--ntasks=20) on two nodes (--nodes=2 and so roughly 48GB of memory per node by defualt) then perhaps start with --mem=60G.  This will request 60GB of per node, 12GB per node more than the original job.  You could accomplish the same thing using --mem-per-cpu=6GB but it may be easier to think of the memory usage on a per-node basis instead of a per-cpu basis.

### My job failed, now what?

If your job fails, the first place to look is the output file you specified using the --output directive.  More often than not, you will see some sort of error that will help you determine why the job failed.  If your software produces any outfiles, then those may also provide some clues as to why things did not complete successfully.

## Job limits

Currently there are no limits in place when it comes to running jobs on the cluster.  We feel this provides the maximum amount of flexibility when it comes 
to people utilizing the cluster.  However, if an individual user begins to "hog" the cluster 
(e.g., submitting large amounts of jobs, requesting a high number of CPUs, running the same piece of software hundreds or more times in the same job script, etc.),
then we may set and enforce limits for that indivdual user.  Further, various limits may be introdcuced in the future as needs and usage change.  

If you have one or more jobs that may require a large amount of resources (e.g., many nodes, very long periods of time, etc.) then please contact us and we can
see about arranging a reservation for your job that will temporarily dedicate some of the cluster resources to you and only you (effectively bypassing the job scheduler). 

## Example submission scripts

One thing to note is that these examples are relatively simple and are meant only as examples(i.e., don't necessarily take the recommendations on values for --ntasks into consideration).  In addition, some pieces of software (notably Miniconda and Gaussian) require some additional steps in the job script in order to use them.  These steps and more general information about the software loaded on the cluster can be found in the [software section](../software/README.md) of the documentation.

### Single job, Single CPU
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

This script will submit a single job that runs on a single CPU.  For such a simple case there really aren't many requirements when it comes to the Slurm directives.  It will email the user when the job completes (or if the job failed).  

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
srun mpiexec -n 96 nrniv -mpi beginSimulation.hoc

date
```

This script is similar to the one above except the code will be run using multiple CPUs (96 in total) in parallel.  Not all software can be run in parallel like this.  It will depend on the software.  Because this example runs a single job, the number of CPUs MPI uses (mpiexec **-n 96**) cannot exceed the number of tasks (**#SBATCH --ntasks=96**)

### Multiple single CPU jobs

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

The real power of Slurm is the ability to submit and run multiple jobs at once assuming the necessary resources (e.g., number of CPUs) are available.  In this example, we are submitting three different single CPU jobs to run at the same time (hence *--ntasks=3*).

The first thing to notice is the use of *srun* before the program calls.  This is a SLURM command to submit a single run.

The second thing to notice is the use of *>* followed by a filename (e.g. *> sim.out*) when running our programs.  Notice too we did not include the *#SBATCH --output=*.  What is happening here is that each program is redirecting output to it's own file.  This makes it much easier to distinguish the output from the different programs.

Finally each run uses *2>&1 &*.  This does two things.  First, any errors will print to the same file used for output.  Second, the job will run as a background process.  This is important because otherwise the script would wait for the job to finish before submitting the next program to run.  The use of the *wait* command tells the system to wait for all the programs to finish running before finishing the job.

### Multiple similar jobs

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

This example uses a powerful feature of Slurm called job arrays.  Some explanation here is needed

1. `--array=1-4` specifies the fact that we will be using a job array, specifically one that will include four jobs whose sub-job-id will be the values 1-4.
2. `${SLURM_ARRAY_TASK_ID}` is a Slurm specific variable that will take on the values specified by the `--array` directive.  Here we are using it to refer to 4 different directories, `sim1`, `sim2`, `sim3`, `sim4`
3. The values for `--nodes` and `--ntasks` (and other directives that might apply to physical resources) apply to each individual sub-job.  This is important for example when doing parallel code runs using MPI.  Here each individual simulation is requesting 24 CPUs on a single node.

In this simulation we have decided to differentiate the runs by placing the appropriate pieces in different directories but theare are many other ways you could setup your job.  More details and examples can be found in [Slurm's job array documentation](https://slurm.schedmd.com/job_array.html)

### GPU jobs

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

In this example, the `heimdall` program runs on a GPU.  We have specified `#SBATCH --partition=gpus` to tell Slurm we need to use the set of nodes which have GPUs and we also specified `#SBATCH --gres=gpu:1` to indicate a single GPU to use.

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

As in the above example, we are using the GPU partition but instead of 1 GPU we are requesting 2 (*--gres=gpu:2).  Most important in this example are the lines

#SBATCH --gpus-per-task=1

#SBATCH --cpus-per-task=2

#SBATCH --ntasks=2

The first line indicates that we want 1 gpu per task.  In most (but not all) cases, a program that uses GPUs will only be able to use a single GPU at a time.

The second line says we need two CPUs per task.  In this example, two is an arbitrary value.  It could be 1 or some other small number.  The important part is that you do specify the number of CPUs per task and not make it large.  By default (that is if left unspecified) the default CPUs per task is all of the CPUs on a node.  If you don't specify CPUs per task the first GPU job will start to execute, and be given all the CPUs meaning the second GPU job can't execute becuase it's waiting on CPUs even though the GPU is available.

Further, If the CPUs per task combined with the number of tasks is greater than the number of CPUs on a node (40 in this case) then the job won't run correctly.  For example if the CPUs per task in this example was 22 instead of just 2, then once again the first GPU job will start to execute, and be given 22  CPUs leaving only 18 available.  The second GPU job can't execute becuase it's waiting on CPUs even though the GPU is available.

The last important line says we have a total of two tasks.  Here we use two srun with the *&* to start each individual heimdall run in the background (meaning the second srun command does not wait for the first one to finish before it starts).  The use of *wait* then waits for both heimdall jobs to complete before moving on. 