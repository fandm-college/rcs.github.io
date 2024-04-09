---
sort: 1
---

# What is a compute cluster?

A compute cluster is a set of interconnected computers that work together as if they were one very large computer.

A fairly typical research workflow on the cluster is as follows:

1. Copy data to the cluster
2. Create a job script to run your particular research workflow
3. Submit your job script to the cluster to do your computation
4. Copy data and results off the cluster for post-processing/analysis after the computations finish. 
   This step is important because there is only a limited amount of storage space for data on the cluster.

## Slurm job scheduler

Running programs on the cluster is different from how you might typically run software on your personal computer.  The cluster uses a software package called Slurm 
(**S**imple **l**inux **u**tility for **r**esource **m**anagement) to manage how/when programs are run on the cluster.  Essentially, you will submit a request to run your software.
Your request will be placed in a job queue and remain there until 

1. Your job gets to (or near) the top of the queue AND 
2. All necessary resources (e.g. # of CPUs, memory, etc.) that your job needs to run are available. 

In order to submit a job you will create a text file (refered to here as a job script). You then submit your job 
script to the scheduler using the `sbatch` command.  For example, if the job script file is named 
`chemistryrun1.job` then to submit that job you would use the command `sbatch chemistryrun1.job`

```warning
The job script file should just contain text.  Programs such as Microsoft Word 
add additional formatting characters that often can't be seen when looking at the 
file, but cause issues with Slurm and therefore should not be used to write 
the job script files.
```