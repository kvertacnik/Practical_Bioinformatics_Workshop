# Remote server (LCC)

## Connect
`ssh user_name@server_address` 

Once you connect, you will be working with two computers: your local computer and the remote server. Pay attention to which computer you are currently on when running commands.

___

## Transferring files 

### Copy files from your computer to the server
`scp local_file user_name@server_address:/path/to/server/directory`

### Copy files from the server to your computer
`scp user_name@server_address:/path/to/server/remote_file /path/to/local/directory`<br>

If the destination is the current working directory, `scp user_name@server_address:/path/to/server/file.txt .` can be used instead of the full path.

**_Question:_** How do you modify the `scp` command to copy entire directories?

___

### Modules
LCC has some programs already installed. Although they are installed, module programs will not work until they are activated on your account.

`module list` lists the activated programs onto your account.

`module avail` lists the programs installed on LCC. Use `module avail | grep "program_name"` to search for a specific program (note that most module names on LCC are in lowercase).

`module load module_name` activates the program

`module unload <module name>` deactivates/removes the program from your account

___

## Slurm job submission
LCC uses Slurm to manage multiple users and resource allocation when each user wants to use the server. 

Commands, or "jobs", are submitted via batch scripts. Batch script files must end in `.sh`
and must start with the following lines:

```
#!/bin/bash
#SBATCH --time=             # How long you want to use the resources
#SBATCH --nodes=            # How many processors; typically 1
#SBATCH --ntasks=           # How many cores on the processor
#SBATCH --partition=        # Processor type
#SBATCH --account=          # Get this information from the course instructor

Optional
#SBATCH --mail-type=END     # Notification email when job is done. Check spam/junk folder
#SBATCH --mail-user=        # Your email address
```
The format for --time is day-hour:minute:second, e.g., 00-01:00:00 means 1 hour.

### Partitions
LCC has a variety of processors (partitions). You can see which ones are available by running `sinfo`. For this course, we can use any of the following:
* SKY32M192_L
* CAL48M192_L
* CAC48M192_L

All of these can be reserved for up to 14 days (14-00:00:00). To see which processors are available for use run `sinfo | grep idle`

For these processors, "32" or "48" is the number of cores and "192" is the amount of RAM.

___

An example batch script named grep.sh:
```
#!/bin/bash
#SBATCH --time=00-00:01:00
#SBATCH --nodes=1
#SBATCH --ntasks=1
#SBATCH --partition=CAC48M192_D
#SBATCH --account=my_account

cd examples/

grep -c "ERROR" log_file.txt
```
___

**_Task:_** Make a new file named "batch_header.sh" and enter the required header information assuming you want to use the SKY32M192_L partition with 1 processor, 32 cores, for 1 day. 

Once you submit a job, you can check it's status with `squeue | grep lcc_user_name`

Each job gets a job number and a corresponding slurm-job_number.out file. This file has the information that is normally printed to the screen as the program is running. 

To cancel a job run `scancel job_number`

NB: On LCC, never run a job without submitting a batch script.
