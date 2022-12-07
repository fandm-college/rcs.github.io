# How do I determine how many resources to request?

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

We recommend requesting additional memory in small increments.  For example, if your initial job was set to run using 20 CPUs (--ntasks=20) on two nodes (--nodes=2 and so roughly 48GB of memory per node by defualt) then perhaps start with --mem=60G.  This will request 60GB of per node, 12GB per node more than the original job.  You could accomplish the same thing using --mem-per-cpu=6GB but it may be easier to think of the memory usage on a per-node basis instead of a per-cpu basis.

### My job failed, now what?

If your job fails, the first place to look is the output file you specified using the --output directive.  More often than not, you will see some sort of error that will help you determine why the job failed.  If your software produces any outfiles, then those may also provide some clues as to why things did not complete successfully.

## Job limits

Currently there are no limits in place when it comes to running jobs on the cluster.  We feel this provides the maximum amount of flexibility when it comes 
to people utilizing the cluster.  However, if an individual user begins to "hog" the cluster 
(e.g., submitting large amounts of jobs, requesting a high number of CPUs, running the same piece of software hundreds or more times in the same job script, etc.),
then we may set and enforce limits for that indivdual user.  Further, various limits may be introdcuced in the future as needs and usage change.  

If you have one or more jobs that may require a large amount of resources (e.g., many nodes, very long periods of time, etc.) then please contact us and we can
see about arranging a reservation for your job that will temporarily dedicate some of the cluster resources to you and only you (effectively bypassing the job scheduler). 