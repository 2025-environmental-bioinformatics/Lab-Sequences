
# Job Submission with Slurm

## HPCS

Generally, an HPC looks like this:

![Image from Software Carpentry](images/hpc_system_diagram.png)

We have already used **SSH** to log in to the login nodes (`l1` and `l2`). However, using a batch system (aka **SLURM**) we can actually access the power of the HPC, which is in the **compute nodes**. To do this, we use SLURM.

SLURM is a **Workload Manager** that helps users share computer resources. Poseidon is more than just the two login nodes we’ve been working on so far in class. It is actually a grouping of many computers whose shared use is controlled by SLURM (**Simple Linux Utility for Resource Management**). SLURM is broadly used across HPC systems and:

- allows access to resources over specified periods of time,  
- provides a framework to monitor jobs, and  
- arbitrates multiple users requesting the same resources.  

---

## Poseidon

Let’s take a look at our particular HPC. Poseidon consists of the following:

- **2 login nodes** for user access to the cluster via 10 Gb Ethernet  
- **84 compute nodes**: 192 or 256GB RAM and 36 or 48 cores per node  
- **4 medium memory nodes**: 512GB RAM and 48 cores per node  
- **1 shared memory node**: 3TB RAM and 80 cores  
- **4 Nvidia Volta V100 GPUs** connected via NVLINK, installed in 1 node with 384GB RAM  

These are the *common* resources for every user of the HPC. In addition, there are purchased sets of nodes that are reserved for specific groups.

👉 Type the command:

```bash
sinfo
````

This prints the status of our HPC and the current free / occupied nodes.

---

### Jargon

| Term       | Definition                                                                                                                                                                                                                                           |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Node**   | One system, server, or computer.                                                                                                                                                                                                                     |
| **CPU**    | Central Processing Unit, the part of a computer which executes software programs.                                                                                                                                                                    |
| **Core**   | An individual processor: the part of a computer which actually executes programs. CPUs used to have a single core, so the terms were interchangeable.                                                                                                |
| **Thread** | The ability for a CPU/core/processor to split a process into two or more running tasks (like having two queues to one cash register — pseudo-simultaneous). We do not have threading on our HPC, but jobs can be parallelized across cores or nodes. |
| **RAM**    | Random Access Memory, a form of memory that can be actively read and used by a program.                                                                                                                                                              |

---

## Requesting Resources on Poseidon

Say you have a job you want to run, but you think it’s going to take a while or be too big for your computer. You can run that job on the HPC by using **SLURM to request the resources** (time and memory) that your job will require.

### Information Needed by SLURM

| Option                  | Definition                                                                                                                     |
| ----------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| `--partition=<queue>`   | Which queue do you want your job to run on? (e.g., `compute`)                                                                  |
| `--job-name=<name>`     | The name of your job.                                                                                                          |
| `--ntasks=<N>`          | How many nodes do you want to run your job on? For *most* things in bioinformatics, this will be 1.                            |
| `--ntasks-per-node=<N>` | How many cores do you need on your node? If your tool allows threading, this is how you request it. (Max on `compute`: **36**) |
| `--mem=<size>`          | How much RAM does your job need per node? (Max on `compute`: **192GB**)                                                        |
| `--time=HH:MM:SS`       | How long does your job need to run? (Max on `compute`: **24 hours**)                                                           |

---

### ⛽ The Gas Station Analogy

Yes, you do have to guess what kind of resources your job is going to require. It’s kind of like going to a **cash-only gas station** where you must prepay before pumping:

* If you guess **too little** → you won’t fill your tank.
* If you guess **too much** → you’ll need to go get change.

On an HPC, if you underestimate, your program may return **nothing** because it didn’t finish.

👉 Therefore, it’s usually better to request **too much**.

But—why not max out every request? Because:

* The scheduler will have a hard time placing your job.
* It will count against your fair usage.

---

## Running Jobs on Poseidon

There are two main options for requesting time on the HPC:

1. **Interactive runs** with `srun` (real-time, like logging onto a node)
2. **Batch jobs** with `sbatch` (submitted to a queue, run when resources are free)

* **`srun`** is useful for debugging scripts or running interactive analysis (e.g., a large Jupyter notebook).
* **`sbatch`** is used for bigger jobs, like assemblies or long BLAST searches — “set it and forget it.”

Today, we’ll stick with **`srun`**, but we’ll use **`sbatch`** later in the class.

```bash
srun -p scavenger --time=00:05:00 --ntasks-per-node=1 --mem=1gb --pty bash
```

👉 **What happened to your prompt?**

Now, check your job with:

```bash
squeue
```

You should see your name, job number, and the resources being used (this time, the `scavenger` queue).

Type:

```bash
exit
```

to log off your interactive run and return to the login node.

If you type `squeue` again, you should still see your job active.

Cancel it with:

```bash
scancel <JOB_NUMBER>
```

---

## Resources

1. [WHOI HPC Overview](https://hpc.whoi.edu)
2. [Cluster Cost Comparison](https://whoi-it.whoi.edu/hpc-cluster-cost-comparison/)
3. [SLURM Rosetta Stone](https://slurm.schedmd.com/rosetta.pdf)
4. [SLURM Official Docs](https://slurm.schedmd.com/)
5. [Environment Modules Documentation](https://modules.readthedocs.io/en/latest/module.html)

```



