---
sort: 4
---

# Using the F&M cluster

Running software on the cluster is different from how you might typically run software on your personal computer.  In particular, the cluster uses a software package called Slurm 
(**S**imple **l**inux **u**tility for **r**esource **m**anagement) to manage how/when software is run on the cluster.  Essentially, you will submit a request to run your software which will be placed in a job
queue.  Your request will execute once it gets to the top of the job queue and the neceessary resources (e.g., #CPUs) are available.  

A fairly typical research workflow on the cluster is as follows:

1. Copy data to the cluster
2. Create a job script to run your particular research software
3. Submit your job to the cluster
4. Copy data and results off the cluster for post-processing/analysis (Important because there is only a limited amount of storage space for data)


The remainder of this section of documentaion will

- Describe how to create a job script and submit job requests using the `sbatch` command.
- Describe how the two primary mechanisms for accessing software on the cluster.
- Provide several job script examples to cover a wide range of situations.
- Discuss strategies for determining the resources you need to request.
- Describe several commands to manage and view information regarding your jobs.
