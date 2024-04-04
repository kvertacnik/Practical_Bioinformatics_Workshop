# Remote cluster (MCC)

![ssh](assets/command_line/ssh.png)

## Connect
`ssh` (secure shell) establishes a connection from your computer to the cluster. You must have an account on the cluster in order to log in.

On your computer run `ssh user_name@mcc-dtn.ccs.uky.edu` 

The first time you connect to the cluster, you will get a message like this; type `yes` to continue.
![Host authentication message](assets/command_line/host_authentication.png)

After successfully logging in, the command line prompt will change to show that. It should look something like this `[user_name@mcc-login001 ~]$`

To disconnect from MCC, run `exit`

NB: Once you connect, you will be working with two computers: your local computer and the remote cluster. When running commands, pay attention to where your working directory is located (i.e., which computer you are working on.)

___

## Make your working directory on MCC
1. After logging on to MCC, go to our course directory <br>
 `cd /pscratch/jdu282_brazil_bootcamp2023/`

2. View the contents of the class directory with `ls`

3. Go into the "students" folder and make a new directory <br>
 `cd students` <br>
 `mkdir your_name`

4. Run `ls` again. You should see a folder with your name. How do we know it’s a folder? The name is color-coded in blue.

___

## Transferring files 

### Copy files from your computer to the cluster
Your current working directory and local_file needs to be on your local computer.

If local_file is in your current working directory: <br>
`scp local_file user_name@server_address:/path/to/cluster/directory`

If local_file is in another directory: <br>
`scp /path/to/local_file user_name@server_address:/path/to/cluster/directory`

### Copy files from the cluster to your computer
Again, run the command from your local computer.

`scp user_name@server_address:/path/to/cluster/remote_file /path/to/local/directory`<br>

If the destination is the current working directory, the full path to the current working directory can be replaced with `.` <br>
`scp user_name@server_address:/path/to/cluster/file.txt .`

**_Question:_** How do you modify the `scp` command to copy a directory?

**_Task:_** Copy the `hello.txt` file you made previously from your computer to your folder on the cluster. Then delete the file from the cluster.

___

## Modules
Some programs are already installed on MCC, however, they need to be activated in order to work.

`module list` lists the activated programs on your account.

`module avail` lists the programs installed on the cluster. Use `module avail | grep "program_name"` to search for a specific program (note that most module names are in lowercase).

`module load module_name` activates the program for your current session

`module unload module name` deactivates/removes the program from your account

___

## Singularities
These are like modules except that instead of loading them, the program name is called when you submit a job to the cluster.

The full list of singularity programs is [here](https://ukyrcd.atlassian.net/wiki/spaces/UKYHPCDocs/pages/72417975/Software+list+for+singularity+containers+for+conda+packages+in+the+MCC+cluster)

___

## Slurm job submission & batch scripts
MCC uses Slurm (Simple Linux Utility for Resource Management) to manage user demand and system resources. When you submit a job, what you're doing is requesting the use of computer processors (nodes). 

Commands, or "jobs", are submitted with batch scripts. Batch script files must end in `.sh` and must start with the following lines:

```
#!/bin/bash
#SBATCH --partition=        # Processor type
#SBATCH --time=             # How long you want to use the resources
#SBATCH --nodes=            # How many processors; typically 1
#SBATCH --ntasks=           # How many cores on the processor
#SBATCH --account=          # CPU hours are monitored and charged

Optional
#SBATCH --mail-type=ALL     # Notify when job starts/ends/fails
#SBATCH --mail-user=        # Your email address
```

To see what types of processors are available run `sinfo`
<p align="left">
  <img src="assets/command_line/sinfo.png" width="75%">
</p>

The format for --time= is day-hour:minute:second, e.g., 00-01:00:00 means 1 hour.

NB: Notification emails often end up in spam/junk folders.

**_Task:_** In your folder on MCC, make a new file named "batch_header.sh" and add the following lines. We will use this file as a template for job submission.
```
#!/bin/bash
#SBATCH --partition=normal
#SBATCH --time 01-00:00:00
#SBATCH --nodes=1
#SBATCH --ntasks=32
#SBATCH --account=coa_jdu282_brazil_bootcamp2023
#SBATCH --mail-type ALL
#SBATCH --mail-user <your email address>
```

To submit a job run `sbatch your_batch_script.sh`

Once you submit a job, you can check its status with `squeue | grep mcc_user_name`. If all processors are in use, your job will wait until resources are available; `TIME 0:00` means your job has not started.

![squeue example](assets/command_line/squeue.png)

Each job gets a job number and a corresponding `slurm-job_number.out` file. This file has the information that is normally printed to the screen as the program is running. 

To cancel a submitted job use `scancel job_number` (get the job number from `squeue | grep mcc_user_name`)

See [here](https://ukyrcd.atlassian.net/wiki/spaces/UKYHPCDocs/pages/72418017/Submitting+jobs+on+MCC+for+first-time+users) for additional information.

NB: All data analysis should be submitted as a job. **Do not run jobs on the login node.**

___

## Getting data for read mapping and SNP calling with GATK
SNP calling from whole-genome (shotgun) sequence data requires a reference genome plus sequencing reads from multiple individuals to map to the reference and call SNPs from. 

We're going to use Bactrocera dorsalis (oriental fruit fly) whole-genome sequence data from [this paper](https://onlinelibrary.wiley.com/doi/full/10.1111/eva.13507).

### **_Task:_** Download the _Bactrocera dorsalis_ reference genome from NCBI
We will download the latest Bactrocera dorsalis (oriental fruit fly) reference genome from NCBI (National Center for Biotechnology Information (USA)).

1. Search for "Bactrocera dorsalis" on https://www.ncbi.nlm.nih.gov/

2. Click on "Genomes" and then click on the genome with the green check (which is the latest reference genome).

<p align="left">
  <img src="assets/gatk/Bdor_NCBI_1.png" width="60%">
</p>

3. Ignore the big download button and instead navigate to the FTP server. You should be at https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/023/373/825/GCF_023373825.1_ASM2337382v1/

![Bdor NCBI genome page](assets/gatk/Bdor_NCBI_2.png)

4. Right-click and copy the link for `GCF_023373825.1_ASM2337382v1_genomic.fna.gz`

5. In your folder on MCC type `wget` and then paste the link:
`wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/023/373/825/GCF_023373825.1_ASM2337382v1/GCF_023373825.1_ASM2337382v1_genomic.fna.gz`

6. After downloading, your folder should look something like this:
<p align="left">
  <img src="assets/gatk/Bdor_MCC_download.png" width="60%">
</p>

___

### **_Task:_** Get sequencing read files (and submit your first cluster job!)
Our data is from this [BioProject](https://www.ncbi.nlm.nih.gov/bioproject/PRJNA893460/). We need to download shotgun reads for multiple individuals, which is a repetitive task (and the perfect opportunity to use a for loop).

A list of the SRA accessions is at `/pscratch/jdu282_brazil_bootcamp2023/data/Bdor_WGS_SRA_list.txt`

You were all assigned a set of four samples, to get a file with the accession values run <br>
`sed -n '<lower_number>,<upper_number>' /pscratch/jdu282_brazil_bootcamp2023/data/Bdor_WGS_SRA_list.txt > SRA_accessions.txt`

To download from NCBI, we're going to need to use the program SRAtoolkit which is available [here](https://github.com/ncbi/sra-tools/wiki/01.-Downloading-SRA-Toolkit). I  already got a copy for us to use.

Make a copy of your batch script template `cp batch_header.sh SRA_download.sh`

Open SRA_download.sh and add the following after the header:
```
for f in `cat SRA_accessions.txt`; do /scratch/kdu224/iceland_workshop/programs/sratoolkit.3.1.0-ubuntu64/bin/prefetch $f; /scratch/kdu224/iceland_workshop/programs/sratoolkit.3.1.0-ubuntu64/bin/fasterq-dump --outdir fastq --skip-technical --threads 32 $f/$f.sra; rm -rf $f; done
```

Then submit your job `sbatch SRA_download.sh`. Assuming it starts right away, this should take about 10 minutes and you should have a new folder named "fastq".

___

### **_Task:_** Subsample sequencing read files
The sequence files have ~20-30 million reads. To speed up analysis time, let's subsample them down to 1 million reads. This way, we'll have enough time for the entire genotyping pipeline, but it will obviously affect the final SNP dataset that we obtain at the end.

Make and submit a batch script named SRA_subsample.sh with these commands. Also change `#SBATCH --ntasks=32` to `#SBATCH --ntasks=1`

```
mv SRA_accessions.txt fastq
cd fastq

for f in `cat SRA_accessions.txt`; do head -n 4000000 "$f"_1.fastq > "$f"_1Mreads_R1.fastq; head -n 4000000 "$f"_2.fastq > "$f"_1Mreads_R2.fastq; done

rm *_1.fastq
rm *_2.fastq
```

**_Question:_** Can you follow what's going on in this job? Why did we move SRA_accessions.txt? Why are we getting 4000000 lines per file? And what does the `>` do?


### Symlinks to frequently used files