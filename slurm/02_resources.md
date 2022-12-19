# How do I determine how many resources to request?

It is important to have a basic understanding of how the amount of CPUs (`--ntasks`) and memory you request for you jobs impacts how/when jobs will run.  Because the cluster is a campus
shared resource, the impact is greater than to just any indivudual user.  Over-requesting resources (especially memory) can easily result in portions of the cluster being unused when they really could be used to run other jobs 
(More about this in the next section).  **To be clear** we aren't asking researchers to find the exact "right" value because there isn't always an exact right value.  What we are asking is that researchers not grossly over-request resources for a job so that as many jobs as possible can be running at one time.

##  Resources amounts per node

Before discussing guidelines for specifying resoucre amounts, you must first know what resources amounts are available on the cluster.
There are 28 nodes (plus 1 additional GPU node) on the cluster.  Each node on the cluster consists of

- 40 CPUs
- 190 GB of memory
- By default (i.e., if not specified in the job script) the default amount of memory for a job is 4.75G * `ntasks` (i.e., the number of CPUs requested for the job)

In addition, the GPU node (specified via the `--partition=gpu` directive) contains 4 GPUs as well as CPU and memory limits just mentioned.

In terms of specifying resource requests in your job scripts, at a bare minimum

- `nodes` cannot exceed 28 (The GPU node should only be used for software requiring a GPU)
- `ntasks` / `nodes` cannot exceed 40 -OR-
- `ntasks-per-node` cannot exceed 40
- `mem` cannot exceed 190G -OR-
- if using `mem-per-cpu` in conjunction with the number of CPUs you request per node cannot exceed 190G

Not let's consider a simple example of two different jobs to see how grossly over-requesting resources can negatively impact cluster usage.  In this example, we'll assume there are only 3 nodes and these two jobs are requested
one right after the other with these resource requests:

- `--ntasks=12`
- `--nodes=3`
- `--mem=120G`

and 

- `--ntasks-per-node=18`
- `--nodes=3`

The first job starts to run, but the second job has to wait even though the nodes have the requested 18 CPUs still available to be used.  The problem here is the memory usage.  The first job requests 120G of memory per node, leaving 70 available for other jobs.  The second job did not specify memory requirements and so uses the default of 4.75G per CPU.  That means the second job requests roughly 85.5G of memory per node to run, but that amount isn't available.

- If the first job has grossly overrequested memory (that is requested 120G when it needs a much smaller amount) then it will block the second job even though the first job may not be using all 120G of memory on the node.
- The second job may also be at fault.  If the second job could use less than the default amount of memory (i.e. less than 4.75 * #CPUs requested) then it could have run despite the amount of requested memory from the first job.  The user in this case could and should request a smaller amount of memory (e.g., `--mem=20G).

## Determining a value for ntasks (# CPUs) and nodes

In terms of choosing a value for the number of tasks, this will depend a great deal on your exact circumstances especially as it relates to how your software runs.  Some software is designed to only run using a single CPU.  In such cases your job may request `--ntasks=1` or perhaps something like `--ntasks=8` if for example you wanted to run the software 8 different times, each individual run using different inputs.  This is also a case where specifically setting either `--nodes` or `--ntasks-per-node` option may not be useful/desired.  If for example, your are running only 3 or 4 instances of your software then letting the scheduler find a few nodes with one or two idle CPUs to use may be better in terms of getting the job through the queue faster because there are always likely to be at least a few nodes with a small number of CPUs not already dedicated for other jobs.
 
On the other hand, if your software can perform calculations in parallel (using perhaps MPI or OpenMPI), then setting `ntasks=32` or perhaps larger will be the way to go.  One thing to note about software that runs in parallel is that there is usually a point where using more CPUs doesn't actually speed things up anymore.  In fact this can often lead to CPUs sitting idle doing resulting again in less than optimal utilization.  Sometimes the software may give guidelines where this tipping point is.  If your not sure then we would recommend that 32 to 64 CPUs as a starting point and probably no more than 72 CPUs at a maximum.

If your software can use a GPU then the number of tasks will vary.  In many cases only or maybe 1 or 2 CPUs is needed because most of the calculations occur on the GPU itself.  In some other cases though, the software may split  calculations such that some can be done on the GPU while others are done on CPUs.  Such software will likely need a greater number of CPUs (e.g., 8).  Also, when using the GPU, the value for `ntasks` may not exceed 40.  We currently only havea single node with GPUs and that node only has 40 CPUs available to use.

## Determining the number of nodes

This value should be considered in conjustion with the value for `ntasks`.  If the value for `ntasks` is small (e.g., 4 or fewer) then  we would recommend **NOT** setting the `--nodes` or `--ntasks-per-node` option.  Instead,
the scheduler will try to find as many nodes as it needs to cover the request and because the total number of `ntasks` is so small, it is very likely that the scheduler will easily find one or more nodes to be able to run the job.

If instead the value for `ntasks` is in the 8 to 20 range then specifying 1 or possibly 2 nodes is probably the way to go.  If your job uses 20 or more CPUs, we strongly recommend using two or more nodes.  To give some flexibility to the job scheduler, you should use `nodes` directive as in this example which request a minimum and a maximum numbmer of nodes to use (2 to 4 nodes in this specific example):

```bash
#SBATCH --nodes=2-4
```

When used in conjuction then with `ntasks`, the scheduler will start the job as soon as 2, 3, or 4 nodes become available with enough total CPUs (and any memory requirements) to run the job.

## Determining a value for memory

As with the number of CPUs this value depends on a few factors.  The first and perhaps most important is the memory needed for a single typical run of your software.  Different software uses different amounts of memory (some more than others).  The best way to determine what your software 'typically' needs is to do 3-4 time-limited runs (using `time` directive) and then use the `seff` command along with your job-id to get a sense of how much memory your jobs might typipcally need.  Here is an example partial job script and output from `seff`:

```bash
#SBATCH --ntasks=16
#SBATCH --nodes=2-4
#SBACTH --time=01:30:00
```

```bash
[auser@rcs-scsn neuron_mpi]$ seff 57957
Job ID: 57957
Cluster: rcs-sc
User/Group: auser/auser
State: COMPLETED (exit code 0)
Nodes: 4
Cores per node: 24
CPU Utilized: 2-13:11:03
CPU Efficiency: 99.24% of 2-13:39:12 core-walltime
Job Wall-clock time: 00:38:32
Memory Utilized: 2.32 GB
Memory Efficiency: 0.91% of 256.00 GB
```

In this example we are requesting the job run for a maximum of 1.5 hours using the default amount of memory (4.75GB per CPU).  The key line to look at from the `seff` command is the *Memory Utilized:* line which outputs the total amount of memory your job used.  By doing 3-4 such timed runs using different inputs/parameters you should get a sense of how much memory your software may use.

There is one very important caveat though.  An hour or 1.5 hours may not provide an accurate picture of memory usage especially if your software takes a very long time to complete a full run.  At the very least you should add an additional 2-4GB to whatever estimate you come up with.  Even so, there may be spikes in memory usage not seen in these timed runs.  If your untimed runs are failing due to OOM (Out of memory) errors, then use the `seff` command to see how much memory it used and increase the requested value in small chunks and re-run.

## Job limits

Currently there are no limits in place when it comes to running jobs on the cluster.  We feel this provides the maximum amount of flexibility when it comes 
to people utilizing the cluster.  However, if an individual user begins to "hog" the cluster 
(e.g., submitting large amounts of jobs, requesting a high number of CPUs, running the same piece of software hundreds or more times in the same job script, etc.),
then we may set and enforce limits for that indivdual user.  Further, various limits may be introdcuced in the future as needs and usage change.  

If you have one or more jobs that may require a large amount of resources (e.g., many nodes, very long periods of time, etc.) then please contact us and we can
see about arranging a reservation for your job that will temporarily dedicate some of the cluster resources to you and only you (effectively bypassing the job scheduler). 