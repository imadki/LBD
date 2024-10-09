## I. Check the available resources using `sinfo` and `squeue` commands
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


- Display the state for each node 
```shell
sinfo -o "%n %G %C %t"
```
- Output in Simlab
```shell
HOSTNAMES GRES CPUS(A/I/O/T) STATE
node01 (null) 0/40/0/40 resv
node02 (null) 0/40/0/40 resv
node03 (null) 30/14/0/44 mix
node04 (null) 30/14/0/44 mix
node14 gpu:1 43/1/0/44 mix
node15 gpu:1 44/0/0/44 alloc
node05 (null) 0/44/0/44 idle
node06 gpu:1 6/38/0/44 mix
node07 gpu:1 44/0/0/44 alloc
node08 gpu:1 44/0/0/44 alloc
node12 gpu:1 44/0/0/44 alloc
node13 gpu:1 44/0/0/44 alloc
node16 gpu:1 44/0/0/44 alloc
node09 gpu:1 0/44/0/44 idle
node10 gpu:1 0/44/0/44 idle
node11 gpu:1 0/44/0/44 idle
node17 gpu:1 0/44/0/44 idle
visu01 gpu:1 1/43/0/44 mix
```
- Display the available CPUs per partition in Simlab
```shell
sinfo -o "%n %G %C %t %P" --noheader | grep -v -e "resv" -e "drain" -e "maint" | awk '{split($3,cpus,"/"); partition[$5]+=cpus[2]} END {for (p in partition) print p, partition[p]}'
```
- Output in Simlab:
```shell
special  438
gpu      366
shortq   94
defq*    94
visu     44
longq    94
```

***You can run `cpusinfo` command to get the same output***

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
### 3. Useful commands (CPUs & GPUs availability)
- Display the available GPUs 
    - In simlab there is only one GPU per node
```shell
sinfo -o "%n %G %C %t"
```
```shell
HOSTNAMES GRES CPUS(A/I/O/T) STATE
node01 (null) 0/40/0/40 resv
node02 (null) 0/40/0/40 resv
node03 (null) 30/14/0/44 mix
node04 (null) 30/14/0/44 mix
node14 gpu:1 22/22/0/44 mix
node15 gpu:1 44/0/0/44 alloc
node05 (null) 0/44/0/44 idle
node06 gpu:1 1/43/0/44 mix
node07 gpu:1 1/43/0/44 mix
node13 gpu:1 1/43/0/44 mix
node16 gpu:1 6/38/0/44 mix
node10 gpu:1 44/0/0/44 alloc
node12 gpu:1 44/0/0/44 alloc
node08 gpu:1 0/44/0/44 idle
node09 gpu:1 0/44/0/44 idle
node11 gpu:1 0/44/0/44 idle
node17 gpu:1 0/44/0/44 idle
visu01 gpu:1 0/44/0/44 idle
```
***This command does not give exact information about the available GPUs***

***When the state is mixed for a node containing gpu, it does not mean that the gpu is not available***

**Example:**
- Allocate 1 task in the gpu partition
```shell
srun --partition=gpu --nodelist=node11 --pty bash
```
- Run `sinfo -o "%n %G %C %t" | grep node11`
```shell
node11 gpu:1 1/43/0/44 mix
```
- Now I can run `srun --partition=gpu --nodelist=node11 --gres=gpu:1 --pty bash` to allocate the gpu in the `node11`
- Run `sinfo -o "%n %G %C %t" | grep node11`
```shell
node11 gpu:1 2/43/0/44 mix
```

- In Simlab Add this command to `~/.bashrc` file, then tap `source ~/.bashrc`
```shell
gpuspernode=1
cpuspernode=44
alias gpusinfo='(squeue -t RUNNING -o "%N %b %C" | awk "NR>1 {split(\$2, gpuArray, \":\"); nodes[\$1]+=\$2; gpus[\$1]+=gpuArray[2]; cpus[\$1]+=\$3} END {for (node in nodes) print node, '$gpuspernode'-gpu\
s[node], '$cpuspernode'-cpus[node]}" && sinfo -p gpu --states=idle --noheader -o "%n %G %c" | grep -v -e "maint" -e "drain" -e "resv" | awk "{gsub(/[^0-9]/, \"\", \$2); print \$1, \$2, \$3}") | grep -F "\
$(sinfo -o "%n %G" | grep "gpu" | awk "{print \$1}")" | column -t'
```

- Display the information about GPU availability
```shell
gpusinfo
```

- Output in Simlab
```shell
node07  0  43
node16  1  39
node10  0  0
node12  0  0
node13  0  43
node14  0  22
node06  0  43
node15  1  0
node08  1  44
node09  1  44
node11  1  44
node17  1  44
```
***This means that node08, node09, node11, node16 and node17 are available to be allocated***


## II. Slurm Parameters: `--time`
  - *max runtime for job (required); format: days-hours:minutes:seconds (days- is optional)*
    - `--time=24:00:00`   *# max runtime 24 hours (same as `--time=1-00:00:00`)*
    - `--time=7-00:00:00` *# max runtime 7 days*
    - In Simlab, the time limite depends on the partition
    - In Toubkal, the time limite depends on the qos
   
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
**Example:**
- If you have access to `low-gpu` qos in Toubkal, you're limited to 1200 minutes for all jobs

| QOS              | Time Limit     | Max. allowed usage of resources per user     | Max. allowed usage of resources for all jobs |
|-------------------|---------------|---------------|-----------|
| low-gpu           | 12:00:00      | node=4        |gres/gpu=1200| 
- Runing multiple batch files asking for 1 gpu 
```shell
#!/bin/bash

#SBATCH --partition=gpu # partition name
#SBATCH --account=manapy-1wabcjwe938-low-gpu 
#SBATCH --nodes=1  # number of nodes to reserve
#SBATCH --gres=gpu:1 # use 1 gpus (On toubkal each node has 4 gpus)

sleep 100 
```
- The `low-gpu`  qos allows me to run job during 12 hours, with max of 4 nodes. For the example, let's submit this job 3 times.
```shell
sbatch batch_gpu.slurm
sbatch batch_gpu.slurm
sbatch batch_gpu.slurm
```
```shell
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
           2100331       gpu batch_gp imad.kis PD       0:00      1 (QOSGrpGRESRunMinutes)
           2100330       gpu batch_gp imad.kis PD       0:00      1 (QOSGrpGRESRunMinutes)
           2100329       gpu batch_gp imad.kis  R       0:00      1 slurm-a100-gpu-h22a2-u10-sv
```
***Oups!!! 2 jobs are pending with reason: QOSGrpGRESRunMinutes***
***This is due to Max. allowed usage of resources for all jobs, which is fixed to `gres/gpu=1200`=> 1200 minutes for all jobs***
***When you don't specifiy the time limit using `--time`, the job takes the max. time lime allowed by the qos***
-	In our case 12 hours. To check that run `squeue -l -u $USER`
```shell
OBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)
           2100429       gpu batch_gp imad.kis  PENDING       0:00  12:00:00      1 (QOSGrpGRESRunMinutes)
           2100428       gpu batch_gp imad.kis  PENDING       0:00  12:00:00      1 (QOSGrpGRESRunMinutes)
           2100427       gpu batch_gp imad.kis  RUNNING       0:01  12:00:00      1 slurm-a100-gpu-h22a2-u10-sv
```
***Here the max. time limites for all the jobs is equal to 12x3=36 hours=2160 minutes > 1200 minutes)***

**Solution:**

- Cancel all the jobs and add `--time` option to limite the time
	- `scancel JOBID`
	- `scancel -u <username>`
	- `scancel -t PENDING -u <username>`

```shell
#!/bin/bash

#SBATCH --partition=gpu # partition name
#SBATCH --account=manapy-1wabcjwe938-low-gpu 
#SBATCH --nodes=1  # number of nodes to reserve
#SBATCH --gres=gpu:1 # use 1 gpus (On toubkal each node has 4 gpus)
#SBATCH --time=04:00:00 # max runtime 4 hours
sleep 100 
```
- Run the jobs
```shell
sbatch batch_gpu.slurm
sbatch batch_gpu.slurm
sbatch batch_gpu.slurm
```
```shell
Sun Dec 10 16:48:53 2023
             JOBID PARTITION     NAME     USER    STATE       TIME TIME_LIMI  NODES NODELIST(REASON)
           2100457       gpu batch_gp imad.kis  RUNNING       0:02   4:00:00      1 slurm-a100-gpu-h22a2-u10-sv
           2100458       gpu batch_gp imad.kis  RUNNING       0:02   4:00:00      1 slurm-a100-gpu-h22a2-u10-sv
           2100459       gpu batch_gp imad.kis  RUNNING       0:02   4:00:00      1 slurm-a100-gpu-h22a2-u10-sv
```
- Or, update the time limit for the current jobs using `scontrol` commad.
```shell
scontrol update jobid=2100457 Timelimit=04:00:00
scontrol update jobid=2100458 Timelimit=04:00:00
scontrol update jobid=2100459 Timelimit=04:00:00
```
