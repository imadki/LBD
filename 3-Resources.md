
<h1 align="center">Reservation commands</h1>

## Simlab

<img src="data/simlab.png" alt="Example Image" >

## Some definition

- A node is one computer unit of an HPC cluster each containing memory and one or more CPUs. There are generally two classifications of nodes; login and compute. 
	- login node: this is where users first login to stage their job scripts and do file and directory manipulations with text file editors and Unix commands.
	- compute node; A cluster can contain a few compute nodes or thousands of compute nodes. These are often referred to as just nodes since jobs are only scheduled on the compute nodes.

- Core
	- Slurm refers to cores as CPUs.
	- there are 40/44 cores per node in Simlab
	- up to 384GB memory per node in Simlab

- Task
	- a task can be considered a command such as blast, bwa, script.py
 	- default: 1 cpu per task
 
<img src="data/tasks.png" alt="Task" >

## I. Reserve resources


### 1. `srun` command
- Obtain a terminal on a CPU or GPU compute node within which you can execute your code,
- Directly execute your code on the CPUs or GPUs.

**Example 1: Connecting to a compute node**

```shell
srun --ntasks=1 --pty bash
```
- Running this command will redirect you directly to a compute node
- An interactive terminal is obtained with the --pty option.
- This command will allocate one task in the default partition (defq).

***run `python3 script.py`***

**Example 2: Running Python script**

- Run the Python file `script.py`
```python
import socket

def get_host_info():
    # Get the hostname
    hostname = socket.gethostname()
    return hostname

if __name__ == "__main__":
    host = get_host_info()
    print(f"Hostname: {host}")
```
- Load Python module if it's not loaded, then run this command
```shell
srun --ntasks=1 python3 script.py
```
- This command will allocate one task in the default partition (defq).
- The resources will be deallocated once the task is finished

- Output:
```shell
Hostname: node03
```

### 2. `salloc` command
- This command will allow you to reserve CPU or GPU resources to run multiple executions.
```shell
salloc --ntasks=1
```
- Ouput
```shell
salloc: Granted job allocation 5858476
```
- Display the submitted jobs
```shell
squeue -u $USER
```
or 
```shell
ssqueue
```
- Output:
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858476      defq     bash ikissami  R       0:35      1 node03
```
- You can either run a command in the current terminal or access to the requested resources from another terminal (In this example node03)
```shell
ssh node03
```

### 3. `sbatch` command

- This command run jobs in the backend (recommended).
- Create file `job.slurm`
```shell
#!/bin/bash

#SBATCH --ntasks=1

# unload any modules to start with a clean environment
module purge

# load software modules
module load Python/3.8.2-GCCcore-9.3.0

# run commands
python3 script.py
```
- Run the job file
```shell
sbatch job.slurm
```
- Output:
```shell
Submitted batch job 5858478
```
- Display the information of the job 5858478
```shell
squeue -j 5858478
```
***This command can display nothing if the job is very fast.***
***But you can check the job output, by running this command:***
```shell
cat slurm-5858478.out
```
***The file slurm-jobid.out is generated automatically.***

## II. Check the available resources using `sinfo` and `squeue` commands
- It's recommended to check the available resources before submitting a job to be sure that your job won't be waiting/pending.

### 1. `sinfo` command
- Display all partitions information
```shell
sinfo
```
- Output in Simlab
```shell
PARTITION AVAIL  TIMELIMIT  NODES  STATE NODELIST
defq*        up    1:00:00      2   resv node[01-02]
defq*        up    1:00:00      3    mix node[03-04,14]
defq*        up    1:00:00      1  alloc node15
defq*        up    1:00:00      1   idle node05
gpu          up 2-00:00:00      2    mix node[06,14]
gpu          up 2-00:00:00      9  alloc node[07-10,12-13,15-17]
gpu          up 2-00:00:00      1   idle node11
shortq       up    4:00:00      2   resv node[01-02]
shortq       up    4:00:00      3    mix node[03-04,14]
shortq       up    4:00:00      1  alloc node15
shortq       up    4:00:00      1   idle node05
longq        up 30-00:00:0      2   resv node[01-02]
longq        up 30-00:00:0      3    mix node[03-04,14]
longq        up 30-00:00:0      1  alloc node15
longq        up 30-00:00:0      1   idle node05
visu         up 1-00:00:00      1    mix visu01
special      up      30:00      2   resv node[01-02]
special      up      30:00      4    mix node[03-04,06,14]
special      up      30:00      9  alloc node[07-10,12-13,15-17]
special      up      30:00      2   idle node[05,11]
```

<table>
  <tr>
    <th>Partition</th>
    <th>Max. Cpu Time</th>
    <th>Nodes available for the partition</th>
  </tr>
  <tr>
    <td>defq</td>
    <td>1 hour</td>
    <td>7 (node[01-05], node14, node15)</td>
  </tr>
  <tr>
    <td>shortq</td>
    <td>4 hours</td>
    <td>7 (node[01-05], node14, node15)</td>
 </tr>
  <tr>
    <td>longq</td>
    <td>30 days</td>
    <td>7 (node[01-05], node14, node15)</td>
  </tr>
  <tr>
    <td>special</td>
    <td>30 minutes</td>
    <td>17 (all nodes)</td>
  </tr>
  <tr>
    <td>visu</td>
    <td>24 hours</td>
    <td>1 (visu01)</td>
  </tr>
  <tr>
    <td>gpu</td>
    <td>48 hours</td>
    <td>12 (node[06-17])</td>
  </tr>
</table>


### 2. `squeue` command

- Display information for all jobs
```shell
squeue
```
- Output:
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858549       gpu     bash ikissami PD       0:00      1 (Resources)
           5857887     longq     Toth zakaria.  R 8-21:16:45      1 node15
           5858432     longq jupyter- haitham.  R   22:25:01      1 node14
           5858338     longq    ST_fn    safae  R 1-19:00:29      1 node14
           5858335     longq   300_fn    safae  R 1-20:13:32      1 node14
           5858334     longq 2_str_10    safae  R 1-20:18:16      1 node14
           5858548       gpu     bash ikissami  R       3:09      1 node13
           5858546       gpu     bash ikissami  R       3:37      1 node16
 [...]
 ```

- Display only your jobs information
```shell
squeue -u $USER
```
- Output
```shell
 JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858549       gpu     bash ikissami PD       0:00      1 (Resources)
           5858548       gpu     bash ikissami  R       4:04      1 node13
           5858546       gpu     bash ikissami  R       4:32      1 node16
```

- Display a specific job information
```shell
squeue -j 5858548
```
- Ouput:
```shell
 JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858548       gpu     bash ikissami  R       4:26      1 node13
```
- Display the pending jobs
```shell
squeue -u $USER -t pending
```
- Output:
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858549       gpu     bash ikissami PD       0:00      1 (Resources)
```

## III.  Slurm Parameters: nodes, partitions, tasks, memory, time

### 1. `--nodes`
  - *number of nodes to use where a node is one computer unit of many in an HPC cluster (optional)*
    - `--nodes=1` \# request 1 node (optional since default=1)
    - *used for multi-node jobs*
      - `--nodes=2`
    - *if the number of cpus per node is not specified then defaults to 1 cpu*
    - ***defaults=1 node*** if `--nodes` not used 

```shell
srun --nodes=2 --pty bash
```
***This command will try to allocate two nodes => two cpus (one cpu per node).***
***Oups!!! It does not work***
```shell
srun: error: Unable to allocate resources: Node count specification invalid
```
***The number of nodes that could be allocated in the `defq` partition is limited to 1.***

### 2. `--partition`
  - *specify a partition (queue)*

### - In Simlab

<table>
  <tr>
    <th>Partition</th>
    <th>Max. Cpu Time</th>
    <th>Nodes available for the partition</th>
    <th>Max nodes per job</th>
    <th>Min-Max cores per job</th>
  </tr>
  <tr>
    <td>defq</td>
    <td>1 hour</td>
    <td>7 (node[01-05], node14, node15)</td>
    <td>1</td>
    <td>1-44</td>
  </tr>
  <tr>
    <td>shortq</td>
    <td>4 hours</td>
    <td>7 (node[01-05], node14, node15)</td>
    <td>2</td>
    <td>1-88</td>
  </tr>
  <tr>
    <td>longq</td>
    <td>30 days</td>
    <td>7 (node[01-05], node14, node15)</td>
    <td>1</td>
    <td>1-44</td>
  </tr>
  <tr>
    <td>special</td>
    <td>30 minutes</td>
    <td>17 (all nodes)</td>
    <td>17</td>
    <td>1-740</td>
  </tr>
  <tr>
    <td>visu</td>
    <td>24 hours</td>
    <td>1 (visu01)</td>
    <td>1</td>
    <td>1-44</td>
  </tr>
  <tr>
    <td>gpu</td>
    <td>48 hours</td>
    <td>12 (node[06-17])</td>
    <td>2</td>
    <td>1-88</td>
  </tr>
</table>

***You can reserve a job using `gpu` partition with max of two nodes (88 cores)***

***You can reserve a job using `special` partition with max of 17 nodes (740 cores)***

**Example 1:** Reserving two node using `shorq` partition 
- You are limited to 2 nodes per job 
- Time limit fixed to 4 hours
```shell
srun --partition=shortq --nodes=2 --pty bash
```

- Display jobs information
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858482    shortq     bash ikissami  R       0:06      2 node[03-04]
```
- This command will reserve 2 nodes, with 1 cpu in each node and 2*8.7GB of memory. To check this, run this command:
```shell
scontrol show jobid 5858482
```
- Output:
```shell
JobId=5858482 JobName=bash
   UserId=ikissami(1063) GroupId=ikissami(1074) MCS_label=N/A
   Priority=105683 Nice=0 Account=novec-account QOS=normal
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
   RunTime=00:00:12 TimeLimit=04:00:00 TimeMin=N/A
   SubmitTime=2023-12-08T22:18:20 EligibleTime=2023-12-08T22:18:20
   StartTime=2023-12-08T22:18:20 EndTime=2023-12-09T02:18:21 Deadline=N/A
   PreemptTime=None SuspendTime=None SecsPreSuspend=0
   LastSchedEval=2023-12-08T22:18:20
   Partition=shortq AllocNode:Sid=master01:192503
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=node[03-04]
   BatchHost=node03
   NumNodes=2 NumCPUs=2 NumTasks=2 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   TRES=cpu=2,mem=17514M,node=2,billing=2
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=1 MinMemoryCPU=8757M MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   Gres=(null) Reservation=(null)
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=bash
   WorkDir=/home/ikissami/TRAINING
   Power=
```

**Example 2:** Allocating resources in the `gpu` partition
- The number of nodes is limited to 2 per job
- The time limit is equal to 48 hours
- You can allocate only cpus 
- You can allocate max. of 2 gpus per job
- Runing this command: `srun --partition=gpu --nodes=2 --pty bash`
	- **does not mean automatically that you are using gpus!!**
- To use gpus you need to add `--grep=gpu:1` (one gpu per node)

```shell
srun --partition=gpu --nodes=2 --gres=gpu:1 --pty bash
```

 ***This command will reserve 2 gpus in two different nodes.***

```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858659       gpu     bash ikissami  R       0:03      2 node[16-17]
```
   
### 3. `--ntasks`
  - *a task can be considered a command such as blastn, .exe, script.py, etc.*
    - `--ntasks=1` \# total tasks across all nodes per job

**Example:** Multi tasks using OpenMPI
- Display available version:
```sh
$ module avail OpenMPI
----------------- /cm/shared/modulefiles ------------------
OpenMPI/3.1.4-GCC-8.3.0  OpenMPI/4.0.3-GCC-9.3.0  OpenMPI/4.0.5-GCC-10.2.0 
```
- Load OpenMPI (version 4.0.3 compiled with GCC 9.3.0):
```sh
$ module load OpenMPI/4.0.3-GCC-9.3.0
```
- Install mpi4py using pip
	- First load both OpenMPI and Python
 ```sh
module load  OpenMPI/4.0.3-GCC-9.3.0 Python/3.8.2-GCCcore-9.3.0 
```
```sh
pip3 install mpi4py
```
  - Create file `mpiscript.py`
```python
from mpi4py import MPI
COMM = MPI.COMM_WORLD
SIZE = COMM.Get_size()
RANK = COMM.Get_rank()

if RANK==0: print("the number of cpus is:", SIZE)
```
- Run the script using 44 cores in one node (load python/3.8.2-GCCcore-9.3.0 and OpenMPI/4.0.3-GCC-9.3.0)
```shell
srun --partition=shortq --nodes=1 --ntasks=44 --pty bash
```
***This command will allocate one node with 44 tasks***
- Then run
```shell
 mpirun python3 mpiscript.py
```
- Display job information  after getting the jobid using `ssqueue` or `squeue -u $USER`
```shell
scontrol show jobid 5858486
```

```shell
JobId=5858486 JobName=bash
   UserId=ikissami(1063) GroupId=ikissami(1074) MCS_label=N/A
   Priority=105584 Nice=0 Account=novec-account QOS=normal
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
   RunTime=00:00:11 TimeLimit=04:00:00 TimeMin=N/A
   SubmitTime=2023-12-08T22:20:58 EligibleTime=2023-12-08T22:20:58
   StartTime=2023-12-08T22:20:58 EndTime=2023-12-09T02:20:58 Deadline=N/A
   PreemptTime=None SuspendTime=None SecsPreSuspend=0
   LastSchedEval=2023-12-08T22:20:58
   Partition=shortq AllocNode:Sid=master01:192503
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=node05
   BatchHost=node05
   NumNodes=1 NumCPUs=44 NumTasks=44 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   TRES=cpu=44,mem=385308M,node=1,billing=44
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=1 MinMemoryCPU=8757M MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   Gres=(null) Reservation=(null)
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=bash
   WorkDir=/home/ikissami/TRAINING
   Power=
```

***If there is no node with 44 tasks available the job will be pending***

- You can ask for 44 tasks without specifying the number of nodes. 
	- This command can reserve more than one node if it's needed
```shell
srun --partition=shortq --ntasks=44 --pty bash
```
***You can specify the number of tasks per node using the option `--ntasks-per-node` unstead of `ntasks`.***

### 4. `--time`
  - *max runtime for job (required); format: days-hours:minutes:seconds (days- is optional)*
    - `--time=24:00:00`   *# max runtime 24 hours (same as `--time=1-00:00:00`)*
    - `--time=7-00:00:00` *# max runtime 7 days*
    - In Simlab, the time limite depends on the partition
   
- Display the time limite for all jobs
```shell
squeue -l
```
- Output:
```shell
Sat Dec  9 15:06:00 2023
             JOBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)
           5858461       gpu Delis.sl noureddi  RUNNING   18:54:26 2-00:00:00      1 node14
           5858543    shortq     bash issam.ai  RUNNING    1:02:28   4:00:00      1 node03
           5858550       gpu 3Dcamber sboughou  RUNNING       9:15 2-00:00:00      1 node17
           5857887     longq     Toth zakaria.  RUNNING 8-21:37:30 25-06:00:00      1 node15
           5858432     longq jupyter- haitham.  RUNNING   22:45:46 30-00:00:00      1 node14
           5858338     longq    ST_fn    safae  RUNNING 1-19:21:14 30-00:00:00      1 node14
           5858335     longq   300_fn    safae  RUNNING 1-20:34:17 30-00:00:00      1 node14
           5858334     longq 2_str_10    safae  RUNNING 1-20:39:01 30-00:00:00      1 node14
           5858542     longq       AI ilyas.bo  RUNNING    1:27:18 4-04:00:00      1 node05
```

### 5. `--mem`
  - *total memory for each node (required)*
    - `--mem=40G` *# request 40GB total memory per node*. If `--nodes=2`, means that the memory allocated will be 40G in each node.
**Example**:
```shell
srun --partition=gpu  --nodes=2 --mem=40G --pty bash
```
***This command will allocate two nodes with 40GB in each node in the partition `gpu`.***
- Check that using `scontrol`
```shell
squeue -u $USER
```
```shell
 JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858555       gpu     bash ikissami  R       0:03      2 node[13-14]
```
```shell
scontrol show job 5858555
```
```shell
JobId=5858555 JobName=bash
   UserId=ikissami(1063) GroupId=ikissami(1074) MCS_label=N/A
   Priority=105684 Nice=0 Account=novec-account QOS=normal
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
   RunTime=00:02:31 TimeLimit=2-00:00:00 TimeMin=N/A
   SubmitTime=2023-12-09T15:59:13 EligibleTime=2023-12-09T15:59:13
   StartTime=2023-12-09T15:59:13 EndTime=2023-12-11T15:59:13 Deadline=N/A
   PreemptTime=None SuspendTime=None SecsPreSuspend=0
   LastSchedEval=2023-12-09T15:59:13
   Partition=gpu AllocNode:Sid=master01:139308
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=node[13-14]
   BatchHost=node13
   NumNodes=2 NumCPUs=10 NumTasks=2 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   TRES=cpu=10,mem=80G,node=2,billing=10
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=5 MinMemoryNode=40G MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   Gres=(null) Reservation=(null)
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=bash
   WorkDir=/home/ikissami
   Power=
```
 ### - In Simlab
 - In simlab 1 cpu = 8.7GB 
 
| HOSTNAMES | MEMORY     | MEMORY PER CPU |
|------------|------------|------------|
| node01, node02     | 125 GB  | 8.7GB |
| node03-node17     | 376 GB  |  8.7GB |

 
***By default, the allocated CPU memory is proportional to the number of reserved cores. For example, if you request 1/4 of the cores of a node, you will have access to 1/4 of its memory.***

**Example 1** (In simlab)
- Allocate 2 cpus with 4GB
```shell
 srun --partition=gpu  --ntasks 2 --mem=4GB --pty bash
```
```shell
squeue -u $USER 
```
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858557       gpu     bash ikissami  R       0:02      1 node06
```
```shell
scontrol show jobid 5858557
```
```shell
JobId=5858557 JobName=bash
   UserId=ikissami(1063) GroupId=ikissami(1074) MCS_label=N/A
   Priority=102906 Nice=0 Account=novec-account QOS=normal
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
   RunTime=00:00:14 TimeLimit=2-00:00:00 TimeMin=N/A
   SubmitTime=2023-12-09T16:22:50 EligibleTime=2023-12-09T16:22:50
   StartTime=2023-12-09T16:22:50 EndTime=2023-12-11T16:22:50 Deadline=N/A
   PreemptTime=None SuspendTime=None SecsPreSuspend=0
   LastSchedEval=2023-12-09T16:22:50
   Partition=gpu AllocNode:Sid=master01:139308
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=node06
   BatchHost=node06
   NumNodes=1 NumCPUs=2 NumTasks=2 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   TRES=cpu=2,mem=4G,node=1,billing=2
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=1 MinMemoryNode=4G MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   Gres=(null) Reservation=(null)
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=bash
   WorkDir=/home/ikissami
   Power=
```
**Example 2** (In simlab)

- Allocate 1 cpus with 20GB
```shell
 srun --partition=gpu  --ntasks 1 --mem=20GB --pty bash
```
```shell
squeue -u $USER 
```
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858558       gpu     bash ikissami  R       0:03      1 node06
```
```shell
scontrol show jobid 5858558
```
```shell
JobId=5858558 JobName=bash
   UserId=ikissami(1063) GroupId=ikissami(1074) MCS_label=N/A
   Priority=102843 Nice=0 Account=novec-account QOS=normal
   JobState=RUNNING Reason=None Dependency=(null)
   Requeue=1 Restarts=0 BatchFlag=0 Reboot=0 ExitCode=0:0
   RunTime=00:00:24 TimeLimit=2-00:00:00 TimeMin=N/A
   SubmitTime=2023-12-09T16:24:22 EligibleTime=2023-12-09T16:24:22
   StartTime=2023-12-09T16:24:22 EndTime=2023-12-11T16:24:22 Deadline=N/A
   PreemptTime=None SuspendTime=None SecsPreSuspend=0
   LastSchedEval=2023-12-09T16:24:22
   Partition=gpu AllocNode:Sid=master01:139308
   ReqNodeList=(null) ExcNodeList=(null)
   NodeList=node06
   BatchHost=node06
   NumNodes=1 NumCPUs=3 NumTasks=1 CPUs/Task=1 ReqB:S:C:T=0:0:*:*
   TRES=cpu=3,mem=20G,node=1,billing=3
   Socks/Node=* NtasksPerN:B:S:C=0:0:*:* CoreSpec=*
   MinCPUsNode=3 MinMemoryNode=20G MinTmpDiskNode=0
   Features=(null) DelayBoot=00:00:00
   Gres=(null) Reservation=(null)
   OverSubscribe=OK Contiguous=0 Licenses=(null) Network=(null)
   Command=bash
   WorkDir=/home/ikissami
   Power=
```
***The number of allocated cpus is equal to 3, even if you asked only one. This is because to satisfy 20GB you need at least 3 cpus (3x8.7=26,1GB).***
***So the rule is when you want to allocate a specific memory, check first if there are enough cpus***

### 6. `--job-name`
 - *set the job name, keep it short and concise without spaces (optional but highly recommended)*
   - `--job-name=myjob`
```shell
srun --partition=gpu --job-name=myjob --pty bash
```
```shell
ssqueue 
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858559       gpu    myjob ikissami  R       0:03      1 node06
```

### 7. `--output` and `--error`
- When you run a job using `sbatch`, a default file will be generated called `slurm-jobid.out`. This file will contain both the output of your execution and errors if there are any.
- So to change the name of this default file, you can:
    - *use just `--output` to save stdout and stderr to the same output file:* `--output=output.%j.log`
	- *save all stdout to a specified file (optional but highly recommended for debugging)*
	    - `--output=stdout.%x.%j` *# saves stdout to a file named stdout.jobname.JobID*
	- *save all stderr to a specified file (optional but highly recommended for debugging)*
	    - `--error=stderr.%x.%j` *# saves stderr to a file named stderr.jobname.JobID*

**Example 1**
- Create `job3.slurm`
```shell
#!/bin/bash

#SBATCH --ntasks=1
#SBATCH --output=output.%j.log
sleep 30
echo "this is test file"
```
- Run the job
```shell
sbatch job3.slurm
```
- Run `squeue -u $SUSER`
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858562      defq job3.slu ikissami R       0:00      1 node03
```
- Display the content of the file
```shell
cat output.5858562.log
```
- Output:
```shell
this is test file
```

**Example 2:**

- Create `job4.slurm`
```shell
#!/bin/bash

#SBATCH --ntasks=1
#SBATCH --output=stdout.%x.%j
#SBATCH --error=stderr.%x.%j
sleep 30
echo "this is test file"
module load bizzare
```
- Run the job
```shell
sbatch job4.slurm
```
- Run `squeue -u $SUSER`
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           5858563      defq job4.slu ikissami  R       0:29      1 node03
```
- Display the content of the output file
```shell
cat stdout.job4.slurm.5858563
```
- Output:
```shell
this is test file
```
- Display the content of the error file
```shell
cat stderr.job4.slurm.5858563
```
- Output:
```shell
ERROR: Unable to locate a modulefile for 'bizzare'
```

### 8. `--array`

- Array jobs are good to use when you have multiple samples each of which can utilize an entire compute node running software that
supports multiple threads but does not support MPI
	- `--array=0-23` # job array indexes 0-23
	- `--array=1-24` # job array indexes 1-24
	- `--array=1,3,5,7` # job array indexes 1,3,5,7
	- `--array=1-7:2` # job array indexes 1 to 7 with a step size of 2 do not use `--nodes` with `--array`
 
- Use the index value to select which commands to run either from a text file of commands or as part of the input file name or parameter value
	- `$SLURM_ARRAY_TASK_ID` is the array index value

- stdout and stderr files can be saved per index value
	- `--output=stdout.%x.%A_%a`
	- `--error=stderr.%x.%A_%a`
- maximum array size is 1000 and max total pending and running jobs per user is 1000
	- Limit the number of simultaneously running tasks
	- can help prevent reaching file and disk quotas due to many intermediate and temporary files
	- as one job completes another array index is run on an available node
	- `--array=1-40%5` # job array with indexes 1-40; max of 5 running array jobs

**Example:**

```shell
#!/bin/bash
#SBATCH --jobname=pi_estimation 
#SBATCH --partition=gpu 
#SBATCH -n 6
#SBATCH --array=1-6

# Load python module                                                                                                                                                                                       module load Python/3.8.2-GCCcore-9.3.0

# Define the array of num_samples                                                                                                                                                                           
num_samples_array=(1000 10000 100000 1000000 10000000 100000000)

# Get the num_samples for this task
num_samples=${num_samples_array[$SLURM_ARRAY_TASK_ID-1]}

# Run the python script
python -c "
import random                                                                                                                                                                                              def estimate_pi(num_samples):                                                                                                                                                                                  num_inside_circle = 0
    for _ in range(num_samples): 
        x = random.uniform(-1, 1) 
        y = random.uniform(-1, 1) 
        distance = x**2 + y**2
                                                                                                                                                                                                            
        if distance <= 1: 
            num_inside_circle += 1
            
    return 4 * num_inside_circle / num_samples 
print(estimate_pi($num_samples))
"
```
- After `squeue -u $USER`
```shell
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
         5859016_1       gpu pi_estim ikissami  R       0:00      1 node14
         5859016_2       gpu pi_estim ikissami  R       0:00      1 node14
         5859016_3       gpu pi_estim ikissami  R       0:00      1 node13
         5859016_4       gpu pi_estim ikissami  R       0:00      1 node13
         5859016_5       gpu pi_estim ikissami  R       0:00      1 node13
         5859016_6       gpu pi_estim ikissami  R       0:00      1 node16
```

### 9. `--constraint`
- The `--constraint` option in Slurm is used to specify hardware or feature constraints for the nodes on which the job should run
- In Simlab for example, you can specify the type of the GPU (V100, P40) when asking resources:
	
**Example:**
```shell
srun --partition=gpu --nodes=1 --constraint=V100 --gres=gpu:1 --pty bash
```

## Remark 

Sometimes even if the CPUs and GPUs seems to be available but the job is pending because there are some future reservations.

- Check the reservations
```shell
scontrol show reservation 
```

```shell
 Nodes=node[06-13,16-17] NodeCnt=10 CoreCnt=440 Features=(null) PartitionName=(null) Flags=SPEC_NODES
   TRES=cpu=440
   Users=(null) Accounts=aait-account,acer-account,agbs-account,ai-account,ak-account,bps-account,cc-cs-account,chatgpt-account,ciam-account,cmsupport-account,crsa-account,cs-cs-account,d4r-account,eiea-account,emines-account,gsmi-account,gti-account,hpcadmin-account,hpcgadmin-account,iap-account,ilo-account,issb_p-account,it-dept-account,iwri-account,limset-account,mascir-account,mmekias-account,msda-account,msn-account,novec-account,ocpgrp-account,ocpsolutions-account,pclab-account,root,sboughou-account,sccs-dna-account,schrodinger-account,sci-account,simlab-account,susmat-account,susmatrc-account,techcell-account,tto-account,tuto-account,usmba-limas-account,vanguard-account,ventures-account Licenses=(null) State=INACTIVE BurstBuffer=(null) Watts=n/a
```

- Or
```shell
scontrol show reservation | tr ' ' '\n' | egrep "ReservationName=|StartTime=|EndTime=|Nodes=" | paste - - - -
```

```shell
ReservationName=hpc_training	StartTime=2023-12-14T13:00:00	EndTime=2023-12-14T18:00:00	Nodes=node[06-13,16-17]
```
***You need to set the time limit before the start of the reservation***

**Example**

- Check jobs status
```shell
squeue -l
```
```shell
Tue Dec 12 20:22:11 2023
             JOBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)
           5858849       gpu       D1 abdelmou  PENDING       0:00 2-00:00:00      1 (Resources)
           5858850       gpu       D2 abdelmou  PENDING       0:00 2-00:00:00      1 (Priority)
           5858851       gpu       RF abdelmou  PENDING       0:00 2-00:00:00      1 (Priority)
           5858858       gpu jupyter-    swiam  PENDING       0:00 2-00:00:00      1 (Priority)
           5858867       gpu       AI ilyas.bo  PENDING       0:00 2-00:00:00      1 (Priority)
           5858868       gpu       AI ilyas.bo  PENDING       0:00 2-00:00:00      1 (Priority)
           5858869       gpu       AI ilyas.bo  PENDING       0:00 2-00:00:00      1 (Priority)
           5858870       gpu       AI ilyas.bo  PENDING       0:00 2-00:00:00      1 (Priority)
           5858871       gpu       AI ilyas.bo  PENDING       0:00 2-00:00:00      1 (Priority)
           5858872       gpu       AI ilyas.bo  PENDING       0:00 2-00:00:00      1 (Priority)
```
- Check the reservations:
```shell
scontrol show reservation | tr ' ' '\n' | egrep "ReservationName=|StartTime=|EndTime=|Nodes=" | paste - - - -
```
```shell
ReservationName=hpc_training	StartTime=2023-12-14T13:00:00	EndTime=2023-12-14T18:00:00	Nodes=node[06-13,16-17]
```
- Now lets fix the time limit
```shell
 srun --partition=gpu --nodes=1 --time=01:00:00 --gres=gpu:1 --pty bash
```
- Check jobs status
```shell
squeue -l -u $USER
```

```shell
Tue Dec 12 20:25:44 2023
             JOBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)
           5858896       gpu     bash ikissami  RUNNING       0:41   1:00:00      1 node16
```

***The new job is running***


