---
sort: 3
---

# Using the F&M cluster

Running software on the cluster is different from how you might typically run software on your personal computer.  In particular, the cluster uses a software package called Slurm 
(**S**imple **l**inux **u**tility for **r**esource **m**anagement) to manage how/when software is run on the cluster.  Essentially, you will submit a request to run your software which will be placed in a job
queue.  Your request will execute once it gets to the top of the job queue and the neceessary resources (e.g., #CPUs) are available.  The remainder of this section will

1. Describe how to create a job script and submit job requests using the `sbatch` command.
2. Describe how the two primary mechanisms for accessing software on the cluster.
3. Provide several job script examples to cover a wide range of situations.
4. Discuss strategies for determining the resources you need to request.
5. Describe several commands to manage and view information regarding your jobs.
