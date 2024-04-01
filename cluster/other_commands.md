---
sort: 5
---

# Additional Slurm commands

## Vieiwng the job queue

The `squeue` command is a command that can be used to display information about the 
jobs in queue. By default, it will print out (left to right)

1. *__job ID__*
2. *__partition__*
3. *__username__*
4. *__job status__* - Most common states are **R** for running, and *PD** for pending
5. *__number of nodes__*
6. *__name of nodes__* - the actual nodes the job is running on
For example:

```bash
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
60149     nodes nil-neur nsengupt  R 1-19:38:33     15 n[07-21]
60187       osg bl_3jsGg    osg01 PD       0:00      1 (Priority)
60186       osg bl_lUjLu    osg01 PD       0:00      1 (Priority)
60184       osg bl_rqGUg    osg01 PD       0:00      1 (Priority)
60185       osg bl_Sixws    osg01 PD       0:00      1 (Priority)
60179       osg bl_8Afn2    osg01 PD       0:00      1 (Priority)
60178       osg bl_WWNqJ    osg01 PD       0:00      1 (Priority)
60177       osg bl_DqhRR    osg01 PD       0:00      1 (Priority)
60176       osg bl_5dXSj    osg01 PD       0:00      1 (Resources)
60175       osg bl_fthxT    osg01  R    6:23:21      1 n36
60174       osg bl_0OkIh    osg01  R    6:25:19      1 n32
60173       osg bl_9pmce    osg01  R    8:31:26      1 n35
60172       osg bl_effaH    osg01  R   12:24:54      1 n29
60167       osg bl_cdVsx    osg01  R   12:25:00      1 n30
60166       osg bl_87MDq    osg01  R   13:10:32      1 n33
60165       osg bl_4DTvm    osg01  R   13:32:17      1 n34
60164       osg bl_Lvpox    osg01  R   13:49:47      1 n31
```

In this example there are 17 jobs in the queue, 9 of them are running and 8 are waiting to run.  There are many different job states including 
`ST`, stopped, `OOM`, out of memory, and `F`, failed, but most of the time you see running and pending.

If you wish to see the state of only your jobs, you can use the `--user` flag as:

```bash
$ squeue --user=osg01
```

You can also output additional information with the `--long` flag. This 
flag will print out the non-abbreviated default information with the 
addition of a *__timelimit__* field:

```bash
$ squeue --user=username --long
```

Finally, for pending jobs, you can use the squeue command to display your the **estimated** start time for jobs by adding 
the `--start` flag.

```bash
$ squeue --user=username --start
```

```warning
Using `--start` only provides an estimate and that time could be greatly impacted by other  
fatcors such as jobs with a higher priority or a lack of computational resources when your job reaches 
the top of the job queue.
```

For more information on `squeue`, [visit the Slurm page for squeue](https://slurm.schedmd.com/squeue.html)

## Managing your jobs

Sometimes you may need to stop a job entirely either while it’s running or before it starts. 
This can be done with the `scancel` command. The general form of the command is:

```bash
$ scancel job-id
```

To cancel multiple jobs, you can use a comma-separated list of job IDs:

```bash
$ scancel job-id1, job-id2, jobid3
```

For more information, [visit the Slurm page for scancel](https://slurm.schedmd.com/scancel.html)

##  Reporting on running jobs

The `sstat` command allows users to see detailed information for currently running jobs. This includes information  such as
*__CPU usage__*, *__task information__*, *__node information__*, *__resident set size(RSS)__* (i.e. memory usage), 
and and much more. The basic form for the `sstat` command is:

```bash
$ sstat --jobs=job-id
```

By default, it will display a large amount of information, more information than you likely want/need to see. 
You can limit the information you see by using the `--format` option. 
This option takes a comma-separated list of stats to report:

```bash
$ sstat --jobs=job-id --format=stat1,stat2,stat3,...
```

For an example, to print out a job's id, average cpu time, and number of tasks the command would be:

```bash
sstat --jobs=job-id --format=jobid,avecpu,ntasks
```

A full list of statistics that can be used can be printed to the screen with the command `sstat --helpformat` or 
you can find it (and more) by [visiting the slurm page for sstat](https://slurm.schedmd.com/sstat.html).

## Reporting on completed jobs

### seff

The `seff` command provides information on how effeciently a job used CPU and memory resources.
This command can be especially helpful in deciding how many resources you need to request for a job. 
Below is an example command and associated output:

```bash
[auser@rcs-scsn neuron_mpi]$ seff 57957
Job ID: 57957
Cluster: rcs-sc
User/Group: auser/auser
State: COMPLETED (exit code 0)
Nodes: 4
Cores per node: 24
CPU Utilized: 2-13:11:03
CPU Efficiency: 95.24% of 2-13:39:12 core-walltime
Job Wall-clock time: 00:38:32
Memory Utilized: 2.32 GB
Memory Efficiency: 0.91% of 256.00 GB
```

Based on this output, it used 96 CPUs (Nodes * Cores per node) and that the CPU utilization was very good, 95.24%  
This basically means that pretty much all the CPUs were in use for the entire time the job ran.  Memory utilization 
was not so good though.  The job requested 256GB of memory but only used 2.32GB (.91% utilization).

### sacct

The `sacct` command providers a much greater amount of information for completed jobs.  It is very similar to the 
`sstat` command in terms of what it shows and the options you can use with the command.  Below are two very basic examples. 
The first will print information for one specific job, where the job-id is 12345.  The second prints job information for all jobs 
run by *auser*.

```bash
$ sacct --jobs=12345
$ sacct --user=auser
```

As with `sstat`, the information that is displayed can be adjusted using the `--format` option.

```tip
By default, `sacct` will retrieve jobs that were run during the current day. 
You can use the `--starttime` and `--endtime` options to adjust time frame 
for searching.  For example, this will print information on all of auser's jobs 
that started on or after March 15th, 2019, up to the current time.

`$ sacct --user=auser --starttime=2019-04-15`
```

As another example of using `sacct`, suppose you want to see information about your jobs that were run on March 12, 2018. 
For each job you want to see the job name, the number of nodes used in the job, the number of cpus, and the elapsed time. 
The command would look like this:

```bash
$ sacct --user=username --starttime=2018-03-12 --format=jobname,nnodes,ncpus,elapsed
```

More details regarding `sacct` by [visiting the slurm page for sacct](https://slurm.schedmd.com/sacct.html).