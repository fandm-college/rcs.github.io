# Research Computing Overview

This guide provides information and basic how-to guides related to Research Computing resources. 

If you have any questions, please contact dorc@fandm.edu.

## Acknowledging Research Computing

Use of Franklin and Marshall Research Computing resources must be acknowledged in any and all publications.

 - For research using the cluster:
   - "This work utilized the \<ClusterName\> Cluster at Franklin and Marshall College. \<ClusterName\> was funded through NSF grant \<GrantNo\>."

- For research using resources other than the cluster:
  - "The authors acknowledge Research Computing at Franklin and Marshall College for providing computational resources and support that have contributed to the research results reported in this publication."

## Available resources

| **Compute system**   | **Hardware specs**  | **Description**         |
|:-------------|:------|:------------------------| 
| rcs-scsn.fandm.edu    | {::nomarkdown}<ul><li>36 Compute nodes</li><ul><li> 40 CPUs per node</li><li>128GB memory per node</ul><li>1 GPU node</li><ul><li>4 Tesla V100 GPUs w/ 32GB memory</li><li>40 CPUs</li><li>128GB memory</li></ul></ul>{:/} | F&M research cluster best for large number and or long running jobs. |
| rcs-cuda.fandm.edu    | {::nomarkdown}<ul><li>8 CPUs</li><li>64GB memory</li><li> 1 Titan RTX GPU w/ 24GB memory</li></ul>{:/} | Best for single or small number  |
| rcs-cuda1.fandm.edu    | {::nomarkdown}<ul><li>16 CPUs</li><li>192GB memory</li><li> 1 Titan RTX GPU w/ 24GB memory</li></ul>{:/} | Best for single or small numberof CPU or GPU jobs especially memory intensive jobs |
| rcs-sci.fandm.edu    | {::nomarkdown}<ul><li>24 CPUs</li><li>128GB memory</li><li> 1 A100 GPU w/ 40GB memory</li></ul>{:/} | Best for single or small number of CPU or GPU jobs jobs especially memory intensive jobs |
| rcs-test.fandm.edu    | {::nomarkdown}<ul><li>8 CPUs</li><li>32GB memory</li><li> 1 Titan V GPU w/ 12GB memory</li></ul>{:/}| Best for testing code/simulations before running on other resources |

\<Something about storage\>


## Courses using Research Computing resources

Instructors who wish to lead a class using any of the Research Computing resources must contact us at dorc@fandm.edu before the class begins.  This is to ensure that adequate resources and support are available.  At a minimum, we will need the following information:  

- A list (preferably a text file) of the netIDs of everyone who needs access
- Software needed 
- Typical computational work load (number of jobs or sessions, length, number of CPUs, etc)  
