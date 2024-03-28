---
sort: 2
---

# Job script basics

```warning
This section of documentation contains a large amount of information.  We have done our best to
 limit it to what is most essential for you to know.  Even so, there is a bit of a learning curve 
to using a compute cluster that necessitates this amount of information.
```

Below is a very basic but typical job script.  There are roughly two sections in any given job script.  
The first/top section contains information the job scheduler needs
in order to know what resources are needed to run the job. The first line in the job script file must be `#!/bin/bash`.  

After that are the job scheduler directives which all begin with `#SBATCH`.  These provide information to the scheduler like the 
name of the job and the computational resources needed.  These directives are covered in more detail in the next section.  

After these directives is the second section of the job script.  This section is where you put commands to load and run your software and may also
contain any other Linux commands you might need in order to perform calculations.

```bash
#!/bin/bash

#SBATCH --job-name=sample_chemistry-job
#SBATCH --output=chem.out
#SBATCH --ntasks=16
#SBATCH --ntasks-per-node=4
#SBATCH --mem=12G
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --mail-user=auser@fandm.edu

# Now that resources and such are specified actually setup and run software
module purge
module load mychemistrysoftware

computeChemistry inputFile.txt
```

```warning
The job script file should just contain text.  Programs such as Microsoft Word 
add additional formatting characters that often can't be seen when looking at the 
file, but cause issues with Slurm and therefore should not be used to write 
the job script files.
```

```note
In the second section of the job script, where you run your software, any line starting with a `#` is treated as a comment and will be ignored by the system. 
So in this example, the line 

`# Now that resources and such are specified actually setup and run software` 

will be ignored by the system.
```

Once you have created your script, you can submit your job using the `sbatch` command.  For example if your job script file is called *chem.job* then you would submit your job as:

```bash
$ sbatch chem.job
```

Your job may or may not begin to run immediately.  It will depend on whether or not the requested resources (e.g., CPUs, memory, nodes) are available
and how many other jobs may be waiting in the job queue already.

## Slurm job directives (#SBATCH)

These directives specify resource requirements and other job information (e.g., job name). In the job script, these directives must come after `#!/bin/bash` and before any commands to run software. 
Each directive takes the general form:

```bash
#SBATCH --<flag>=<value>
```

For example, if you wanted to use only 2 nodes to run your job, you would include the following directive:

```bash
#SBATCH --nodes=2
```

Below is a table of some of the more commonly used directives.  However, you don't need to specify all of these directives in your job script.
A full list of directives and other options can be found in Slurm's documentation for [sbatch](https://slurm.schedmd.com/sbatch.html).

| Flag                  | Description                                         | Example                    |
| :---------- --------- | :-------------------------------------------------- | :------------------------- |
| `--job-name`          | Name your job so you can identify it in the queue   | `--job-name=neuron-job`      |
| `--output`            | Specify a file that                                 | `--output=myjob.out`         |
| `--mail-type`         | Get email when job starts/completes                 | `--mail-type=END,FAIL`      |
| `--mail-user`         | Email address to receive the email                  | `--mail-user=auser@fandm.edu` |
| `--nodes`             | Request a certain number of nodes to run the job    | `--nodes=4`                  |
| `--ntasks`            | The **total** number of CPUs needed to run the job | `--ntasks=96`               |
| `--ntasks-per-node`   | The number of processes you wish to assign to each node | `--ntasks-per-node=24`   |
| `--mem-per-cpu`       | Amount of memory needed on a per CPU basis          | `--mem-per-cpu=16G`          |
| `--mem`               | Amount of memory needed on a per node basis         | `--mem=16G`                  |
| `--partition`         | Specify a partition. Currently only needed if using a GPU | `--partition=gpus`     |
| `--gres`              | Specify a GRES. Currently only needed if using a GPU | `--gres=gpu:1`              |

```tip
We recommend always specifying the following directives

1. `--job-name` (Be sure to make your job name unique to make it easier to troubleshoot any issues)
2. `--output`
3. `--mail-user` and `-mail-type`
4. `--ntasks` or both `--ntasks-per-node` and `--nodes'
  - As a rough guideline we recommend if you will be requesting more than 16 CPUs (i.e. `--ntasks` > 16), 
    then you request at least two nodes and use the `--ntasks-per-node` and `--nodes' option
5. `--mem` or `--mem-per-cpu` (These two directives are mutaully exclusive) 
  - We generally recommend using the `--mem` directive over the `--mem-per-cpu` directive.
```

```note
For software that runs on a GPU you also must specify:
  
1. `--partition=gpus`
2. `--gres` which will usually be in the form: `--gres=gpu:1`  The number after the colon is the number of GPUs needed.  In almost all cases the value should be 1.
```

## The --output directive

Software packages sometimes output messages and errors directly to the terminal as they run.  The `--output` directive specifies a file name where such messages will 
be written to instead of being dispalyed to the terminal.  It **does not** specify the name of output file(s) which your particular program may use to capture other output.  
Those files still need to be specified as you normally would when running the software.

For the `--output` directive we recommend using one of the following two formats depending on your specific curcumstances:

- `--output=myfilename_%j.out`  Here the `%j` will be replaced by the job-id Slurm generates automatically.  So if the job-id is 439 then the output would go to `myfilename_439.job`.  
  This will make it easier to differentiate output files if you run the same job script over and over again.

- `--output=myfilename_%A_%a.out`  Sometimes a job will have sub-jobs that get run (for example, running the same simulation multiples times from the same job script but each run uses different parameters).  
   In this case, `%A` refers to the job-id and `%a` refers to the sub-job-id.  For example if you ran 3 simulations, their sub-job-ids might be 1, 2, and 3 producing output files, `myfilename_439_1.out`, `myfilename_439_2.out`, and `myfilename_439_2.out`

## The --nodes directive

If your job will be requesting resources on more than one node then there are some things to consider.  The `--nodes` directive 
allows you to specify either an exact value (e.g., `--nodes=4` or  a range of values (e.g., `--nodes=2-4`.  In the first case,
the scheduler will start your job when you are at the top of the job queue and that exact number of nodes is available 
(also taking into consideration CPU and memory requests).  In the second case, the scheduler will start your job when 
you are at the top of the job queue and at least the number of nodes is available (again taking into consideration CPU 
and memory requests).

In addition, using one versus the other has implications when you also use the `--ntasks-per-node` directive.  Whether you use
`--nodes` with an exact value or a range, the total number of tasks (that is the number of CPUs) you get will be the number 
of nodes assigned to your job times the number of tasks per node.  For example, if you specify `--ntasks-per-node=8` and `--nodes=4` 
you will get a total of 32 CPUs and your job will run when 4 nodes each with 8 free CPUS is available.

On the other hand, if you if you specify `--ntasks-per-node=8` and `--nodes=2-4` then your job might run with a total of 16, 24, or
32 CPUs depending on how many nodes with 8 available CPUs are free when your job reaches the top of the job queue.  The tradeoff is that 
your job may start sooner but POSSIBLY with fewer CPUs/less memory which may also mean that the computation time may take longer.  It is
up to you to decide how you'd like to request nodes.

## How many resources do I request?

Requesting an appropriate amount of resources is extremely important because it has a direct impact on how many jobs can be running at a time.  When a job starts running on the system, 
the system reserves the resources that are requested in the job script for as long as that job is running, **whether the job actually uses the resources or not**.  For example, 
if your job requests `#SBATCH --ntasks=24` (essentially 24 CPUs) but in the course of processing only uses 8 CPUs, then 16 CPUs did nothing and but could not be used 
for another job becuase they were reserved for your job.  So, over-requesting resources can have a very negative impact on the ability of the scheduler to run jobs, affecting not only you 
but all other users on the cluster as well.

**To be clear** we aren't asking you to find the exact "right" values because there aren't necessarily exact right values.  What we are asking is that you not grossly 
over-request resources for a job so the scheduler can run as many jobs as possible at one time.

```warning
The steps that follow for determining how many resources to request are a rough guidleline. 
There is no guarantee that any particular job won't exceed resource limits because the amount of resources a particular job 
actually needs is dependent on many factors including (but not limited to)

1. The size of the input
2. Your processing workflow for a particular input
  - How many different software packages get used?
  - What is the typical output for any individual software package?
  - Is a software package CPU intensive?  Memory intensive? Both?
3. What's in the data?
  - Is the software going to "find" lots of possibly interesting things related to the research question?
  - Is the data pretty "boring" in terms of the scientific question being examined?
    
From our perspective, it is better to have a job fail due to insufficient resources and re-run that job requesting more resources than it is 
to over-request (grossly over-request) resources which never get used AND probably prevent other jobs from running.
```

Before discussing the guidelines for determining resoucre amounts, you should first know what resources amounts are available on the cluster 
because these are physical upper limits.  Requesting amounts in excess of this will cuase your job to fail immediately.

Currently the cluster has

- 29 nodes
  - 1 of these nodes is the GPU node and should only be used for software that requires a GPU
- Each node (including the GPU node) has
  - 40 CPUs
  - 192 GB memory
- GPU node has 4 GPUs

Again any single job should never use all of any of the resources.  But just for clarity the computational upper limits are as follows:

- `#SBATCH --nodes` cannot exceed 28 (effectively 28 since the GPU node should ony be used for GPU based software)
- `#SBACTH --ntasks`
  - cannot not exceed 1120 if you do not also specify `#SBATCH --nodes`
  - if you do specify `#SBATCH --nodes` then the number of tasks divided by the number of nodes cannot exceed 40
- `#SBATCH --ntasks-per-node` cannot exceed 40
- Memory
  - `#SBATCH --mem` cannot exceed 192G -OR-
  - `#SBATCH --mem-per-cpu` times ntasks cannot exceed 192G
  - If no memory directive is specified, then the default is to use 4.75G * ntasks

We currently do not set any resource limits in the job scheduler, however we do recommend that you follow these suggestions:

- No more than 128 CPUs total
  - No more than 16 CPUs per node
- Use 6GB of memory per node as a starting point `#SBATCH --mem=6G` or the equivalent if specifying `#SBATCH --mem-per-task`

In addition, If you are going to request a certain number of nodes (using `#SBATCH --nodes`, which we recommend doing) then we suggest 
requesting them in the general form `#SBATCH --nodes=min-max` where min is the minimum number of nodes you'd like to use and max is the maximum 
number.  For example, `#SBATCH --nodes=3-6` will request anywhere from 3 to 6 nodes which have enough free CPUs and memory to run your job. 
Requesting nodes this way gives the scheduler more flexibility when it comes to actually starting your job.

### Step 1: Understand your workflow and software

The biggest benefit of a cluster...

Below are some questions to help determine if your situation allows running parallel computations.  If the answer to any of these questions is YES, then your job 
script should request more than just one CPU, either with `#SBATCH --ntasks` or `#SBATCH --ntasks-per-node`.  Guidance on the number of 
CPUs to request will be provided in the next few sections.

- Does my software run in parallel? **Note:**Just because you've never run a piece of software in parallel, does not mean it can't be done.
- Am I running multiple pieces of software that can be run independent of each other? For example, I need to run *program1* and *program2*, but neither 
  program depends on the other so they can run at the same time.
- Am I running the same piece of software multiple times with different inputs and/or parameters?  Often times these different runs can be 
  executing at the same time.

```tip
Just because you may not be able to run software in parallel, does not mean you should not use the cluster.  If you have a computation or series of computations 
which may take a long time or require a lot of memory, running on the cluster may still make sense.  It just means that you should specify `#SBATCH --ntasks=1` and you do not need to 
use the `#SBATCH --nodes` directive.
```

### Step 2: Run some sample jobs

Below is a sample set of resource requests to use as a baseline for testing purposes.  If you already know your software/workflow cannot be run in parallel, 
then remove the line `#SBATCH --nodes=2-4` and change the first line to `#SBATCH --ntasks=1`  It is still worth doing some sample runs to get a sense of how much memory 
you may need.

```note
This snippet only includes the reewust for things like CPUs and memory, you should also include other directives for things like 
job name, output, etc., as well as all the commands you need to actually run the software.
```

```bash
#SBATCH --ntasks-per-node=6
#SBATCH --nodes=2-4
#SBATCH --mem=16G
#SBATCH --time=01:30:00
```

This will script request a minimum of 2 nodes and a maximum of 4 nodes, and anywhere from 12 to 24 CPUs depending on how many nodes are assigned to your job, and 16GB of memory per node.  The final line sets a run 
time limit of 1.5 hours.  This sample script is just to get a rough idea of your computational requirtements, so if your computation would normally take hours or more, you don't want 
to wait that long just for a test run.

```tip 
We strongly suggest you do at least 3 test runs with different inputs in order to get a sense of the resource requirements.
```

Assuming the job runs without error, then when it does complete you should use the the `seff` command to look at resource utilization. 
This command requires the job ID that Slurm assigns.  For example, if your job id was 57957 you would run `seff 57957` which will produce output similar to:

```bash
Job ID: 57957
Cluster: rcs-sc
User/Group: auser/auser
State: COMPLETED (exit code 0)
Nodes: 4
Cores per node: 6
CPU Utilized: 2-13:11:03
CPU Efficiency: 99.24% of 2-13:39:12 core-walltime
Job Wall-clock time: 00:38:32
Memory Utilized: .57 GB
Memory Efficiency: 0.91% of 64.00 GB
```

There are three lines you should focus on:

1. CPU Efficiency
  - CPU Utilization is very good here, 99% utilization.
  - Becuase of such a high rate of utilization, it is likely (though not certain) you could request more CPUs for your job.
2. Memory Utilized and Memory Efficiency
  - In this sample output, memory utilization was terrible, less than 1% of memory requested was utilized.
  - Therefore 16GB per node is way too much.  Suggestion based on the memory utilized of roughly 1/2GB would be to decrease the memory requested to `#SBATCH --mem=2GB` 
    which is still probably more than you need, but allows for some wiggle room in terms of memory usage while still leaving a good amount 
    of memory available for other jobs.

### Step 3: Additional considerations

```warning
In terms of using multiple CPUs in parallel, more is not always better.  There is eventually a point where 
having more CPUs does not produce a computational benefit especially in relation to how long it takes software to run.
```

1. Check the documentation for your software for recommendations on the number of CPUs and/or amount of memory
2. If you will be running software in parallel on multiple nodes, we recommend trying to distribute the number 
   of CPUs equally across nodes using `--ntasks-per-node`
3. Once you do some actual computations, use `seff` after the jobs complete to assess your resource usage