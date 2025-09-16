
# Using `screen` on HPC Systems

When working on a High-Performance Computing (HPC) cluster, you often run interactive sessions that you don’t want to be interrupted if your SSH connection drops. The **`screen`** utility helps you manage persistent terminal sessions that continue running in the background, even after disconnecting.

---

## Why Use `screen`?

- Keeps your processes running if you lose connection.  
- Allows multiple terminal sessions within a single SSH login.  
- Lets you detach and reattach to sessions anytime.  
- Useful for monitoring or debugging long-running jobs.

---

## Basic Commands

Start a new screen session:
```bash
screen -S newsession #use any name you want; prefereably something indicative of the process you are running
````

Detach from the session (leave it running in background):

```bash
Ctrl + A, then press D
```

List all active screen sessions:

```bash
screen -ls
#alternatively
screen -r
```

Reattach to a session:

```bash
screen -r newsession
```

Kill a session (if no longer needed):

```bash
screen -X -S mysession quit
```

---

## Example Workflow

1. SSH into the HPC login node:

   ```bash
   ssh user@poseidon.whoi.edu
   ```

2. Start a screen session:

   ```bash
   screen -S alignment
   ```

3. Run your long-running command, e.g.:

   ```bash
   bowtie2 -x genome_index -U reads.fq -S output.sam
   ```

4. Detach safely (`Ctrl + A, D`) and log out.

5. Later, log back in and reattach:

   ```bash
   screen -r alignment
   ```

---

## Tips

* Use **descriptive names** with `-S` so you can easily identify sessions.
* If you forget to name your session, you’ll see a numeric ID in `screen -ls`.
* `screen` is lightweight, but for more advanced session management you might also explore [`tmux`](https://github.com/tmux/tmux).

---

✅ With `screen`, you never have to worry about losing progress on long HPC jobs again!

```

Would you like me to also include a **section comparing `screen` vs `tmux`** so students know why one might be preferable?
```
