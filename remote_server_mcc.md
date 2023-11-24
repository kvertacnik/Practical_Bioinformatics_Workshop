# Remote cluster (MCC)

## Connect
`ssh user_name@mcc-dtn.ccs.uky.edu` 

Once you connect, you will be working with two computers: your local computer and the remote server. When running commands, pay attention to which computer you are working with.

To disconnect from MCC, run `exit`

___

## Transferring files 

### Copy files from your computer to the cluster
`scp local_file user_name@server_address:/path/to/cluster/directory`

### Copy files from the cluster to your computer
`scp user_name@server_address:/path/to/cluster/remote_file /path/to/local/directory`<br>

If the destination is the current working directory, `scp user_name@server_address:/path/to/cluster/file.txt .` can be used instead of the full path.

**_Question:_** How do you modify the `scp` command to copy a directory?

___

## Modules
Some programs are already installed on LCC, however, they will not work until they are activated.

`module list` lists the activated programs onto your account.

`module avail` lists the programs installed on LCC. Use `module avail | grep "program_name"` to search for a specific program (note that most module names on are in lowercase).

`module load module_name` activates the program

`module unload module name` deactivates/removes the program from your account

___

## Singularities
These are like modules. The full list of available programs is [here](https://ukyrcd.atlassian.net/wiki/spaces/UKYHPCDocs/pages/2920537/Software+list+for+singularity+containers+for+conda+packages+in+the+MCC+cluster)

Instead of loading them, however, add the singularity information (in the "Notes" column) to your batch script. 

___

## Slurm job submission & batch scripts
LCC uses Slurm to manage user demand and system resources. 

Commands, or "jobs", are submitted with batch scripts. Batch script files must end in `.sh` and must start with the following lines:

```
#!/bin/bash
#SBATCH --time=             # How long you want to use the resources
#SBATCH --nodes=            # How many processors; typically 1
#SBATCH --ntasks=           # How many cores on the processor
#SBATCH --partition=        # Processor type
#SBATCH --account=          # CPU hours are monitored and charged

Optional
#SBATCH --mail-type=END     # Notification email when job is done
#SBATCH --mail-user=        # Your email address
```
The format for --time= is day-hour:minute:second, e.g., 00-01:00:00 means 1 hour.

If you choose to have notification emails sent to you, note that they often end up in spam/junk folders.

**_Task:_** Make a new file named "batch_header.sh" and add the following lines. We will use this file as a template for making batch files.
```
#!/bin/bash
#SBATCH --partition=normal
#SBATCH --time 01-00:00:00
#SBATCH --nodes=1
#SBATCH --ntasks=32
#SBATCH --account=coa_jdu282_brazil_bootcamp2023
#SBATCH --mail-type END
#SBATCH --mail-user <your email address>
```
___

Once you submit a job, you can check it's status with `squeue | grep lcc_user_name`. If all processors are in use, your job will wait until resources are available. 

Each job gets a job number and a corresponding `slurm-job_number.out` file. This file has the information that is normally printed to the screen as the program is running. 

To cancel a submitted job use `scancel job_number` (get the job number from `squeue | grep mcc_user_name`)

NB: Never run a job without submitting a batch script.
