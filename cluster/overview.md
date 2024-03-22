---
sort: 1
---

# What is a compute cluster?

-- UNDER CONSTRUCTION --

A fairly typical research workflow on the cluster is as follows:

1. Copy data to the cluster
2. Create a job script to run your particular research research workflow
3. Submit your job to the cluster
4. Copy data and results off the cluster for post-processing/analysis.  This step is important because there is only a limited amount of storage space for data on the cluster.

## Job scheduler: Slurm

Running programs on the cluster is different from how you might typically run software on your personal computer.  The cluster uses a software package called Slurm 
(**S**imple **l**inux **u**tility for **r**esource **m**anagement) to manage how/when programs are run on the cluster.  Essentially, you will submit a request to run your software.
Your request which will be placed in a job queue and remain there until your job gets to the top of the queueu AND all necessary resources (e.g. # of CPUs, memory, etc.) are available.
At that point your job will begin to execute.

In order to submit a job you will create a text file (refered to here as a job script) which contains two key sections:

1. A set of parameters/resources (e.g., # CPUs) needed to run the job
2. Various commands to load, initialize and, run the software

You then submit your job script to the scheduler using the `sbatch` command (details are below).
