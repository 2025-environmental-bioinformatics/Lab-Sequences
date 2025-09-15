Let's get ourselves some sequence data to play with. Data from many projects are available at the Sequence Read Archive (SRA) - this the NCBI repository for raw high-throughput sequencing data. 

## Download sequences from SRA

Luckily, the SRA has its own command line toolkit `sra-tools` for retrieving data. This lets us directly download files to Poseidon, which is handy as some of these files can be big. Plus, with a command line tool, we can use bash scripting to automate retrieval of lots of sequences at once. (More on this later.)

### Install sra-tools

#### 1. Using `conda`
First, we need to install `sra-tools` in a new conda environment. Let's create one called *sra_get* (or whatever you like!), activate it, and install `sra-tools`.

```
conda create --name sra_get
conda activate sra_get
conda install -c bioconda sra-tools
```

#### 2. Using `mamba` to speed up downloads
If this is taking a long time, a great alternative is `mamba`. `mamba` is a reimplementation of `conda` (so it does the exact same thing as `conda`, it just uses a different route to do so). Using a magic combination of _parallelization_, `C++`, and much faster solving using an improved algorithm (`libsolv`), `mamba` resolves all of our package installation woes. Most packages should be quick to install with `mamba`. 

```
mamba create --name sra_get
mamba activate sra_get
mamba install -c bioconda sra-tools
```

just like with `conda`; `mamba` works _exactly_ like `conda` does!

![Speedup gif from https://blog.hpc.qmul.ac.uk/mamba.html](https://github.com/2023-environmental-bioinformatics/Lab-Sequence_Quality/blob/main/libmamba_classic_comparison.gif)

### Request Poseidon resources for downloading

Since we're not downloading a lot right now, we could go ahead and do it on Poseidon as-is. However, it's good HPC manners to request time and resources first. Let's do that interatively with an `srun` request (make sure you've navigated to the folder you cloned from GitHub for this).

```srun -p compute --time=1:00:00 --ntasks-per-node=1 --mem=8gb --pty bash```

Notice how your prompt changed once resources were assigned? `pn045` (or whatever) is the node that's been assigned to you for the next hour.

Now, activate your `sra_get` conda environment. (If you had already activated it, you would have to do so again after your `srun` request to be able to access it now.)

#### Download fastq files based on the run number 

Now, we are ready to start the download using the `fasterq-dump` command. (This is the streamlined update of the original `fastq-dump` command, courtesy of those clever wags at NCBI.)

Use help to see your options:
```fasterq-dump -h```

The flags `-O` and `-split-3` are commonly used. `-O` lets you set the output directory (default is your current working directory).  `--split-3` is specific to **paired-end reads**, and will create two fastq files, one for the forward reads and the other for the reverse read. `--split-3` will check that each forward read has a reverse read mate, and will not download unpaired reads. `--split-reads` will split forward and reverse without running this check. In theory, all the reads **should** be paired in the raw data, but if they aren't then `--split-3` ensures downstream processing will go more smoothly.

Now, let's all download the file with the accession number SRR8281009. (This is a transcriptome run from _Cymothoa exigua_, everybody's favorite fish tongue-replacing parasitic isopod.)

```fasterq-dump --split-3 --verbose -O sra/ SRR8281009```

Check out your new `sra` folder. How big is it? The `du` ("Disk Usage") command is handy for this. `-s` makes it give a summary size over all the files in the directory, and `-h` displays it in handy human-friendly M, G, etc (rather than thousands of bytes.)

`du -sh sra`

Now, navigate into your `sra` folder. Hopefully you have 2 files ending in `_1.fastq` and `_2.fastq`. These are your forward and reverse reads from the single run you downloaded.

