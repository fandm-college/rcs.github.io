---
sort: 2
title: Managing jobs
---

## Slurm overview

Slurm  stands for **S**imple **l**inux **u**tility for **r**esource **m**anagement.  Slurm allows you to submit, monitor, and manage your jobs on the cluster.  This section will describe how to create a job script and the usage of the `sbatch` command to submit jobs to the cluster.  Information on managing and monitoring jobs [can be found here](commands.md)

## Job script basics

Typically you will create a text file (refered to here as a job script) to specify the details of your job.  A job script is set of Linux commands paired with a set of resource requirements that can be passed to the Slurm job scheduler. Slurm will then generate a job according to the parameters set in the job script. Any commands that are included with the job script will be run within the job.  A job script will usually consist of four components:

- The first line must be `#!/bin/bash`
- Job resources and directives
- Load/initialize software
- Run the program(s)

Once you have created your script, you can submit your job using `sbatch`.  For example if your job script file is called *neuron.job* then you would submit your job as:

```bash
$ sbatch neuron.job
```
## Slurm directives

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
| Sending email      | Receive email at beginning or end of job completion | --mail-type=type           | --mail-type=    |
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
