# Genome assembly
For this exercise, we are going to create a _Bactrocera dorsalis_ genome assembly from small-insert, paired-end Illumina sequencing reads.

## Read data
The sequence data is available in the course folder. We're going to make symlinks (symbolic links) to the files.

**_Task:_** In your home directory on LCC, make a folder named "sequence_reads". Go into the folder. Run `ln -s /path/to/course/folder/SRR901643_1.fastq /path/to/your/sequence_reads/folder` for SRR901643_1.fastq and SRR901643_2.fastq. Hint: use `pwd` to get the path information.

This data was downloaded from the NCBI SRA (short read archive). You can learn more about the data [here](https://www.ncbi.nlm.nih.gov/sra/SRX306436[accn])

___

## Read trimming
**_Task:_** Install the read trimming program `fastp`. <br>
In your home directory on LCC, make a folder named "programs". Go into the "programs" directory. Follow the directions [here](https://github.com/OpenGene/fastp#or-download-the-latest-prebuilt-binary-for-linux-users).<br>

**_Task:_** Make and submit a batch script for read trimming. <br>
In your home directory, make a copy of your `batch_header.sh` file `cp batch_header.sh fastp.sh` <br>
Open `fastp.sh` in `nano` and change the number of cores to 16 (fastp only uses up to 16 threads) `#SBATCH --ntasks=16` <br>
Add the following commands. Change the paths to YOUR path information.
```
cd /path/to/your/sequence_reads/directory

/path/to/fastp --in1 SRR901643_1.fastq --in2 SRR901643_2.fastq --out1 SRR901643_1_trimmed.fq.gz --out2 SRR901643_1_trimmed.fq.gz --detect_adapter_for_pe --overrepresentation_analysis --length_required 75 --compression 9 --thread 16
```

Now submit your job! `sbatch fastp.sh`. This should take about 20 minutes. Check your job status with `squeue | grep LCC_username`

**_Question:_** Look at the fastp readme. Why did we not use the --trim_poly_g setting? Hint, see the NCBI SRA page for the read data. What sequencing instrument was used?

___

## Assembly
**_Task:_** Install the SPAdes assembler. <br>
First, in your "programs" folder, download the program `wget --no-check-certificate https://github.com/ablab/spades/releases/download/v3.15.5/SPAdes-3.15.5-Linux.tar.gz`. <br>
Second, decompress the file `tar -xzvf SPAdes-3.15.5-Linux.tar.gz`. The program `spades.py` is located in the bin directory within the SPAdes-3.15.5-Linux directory. <br>

**_Task:_** Make and submit a batch script for genome assembly. <br>
In your home directory, make a copy of your `batch_header.sh` file `cp batch_header.sh spades.sh` <br>
Add the following commands. Change the paths to YOUR path information.
```
/path/to/spades.py --pe1-1 /path/to/SRR901643_1_trimmed.fq.gz -pe1-2 /path/to/SRR901643_2_trimmed.fq.gz --threads --memory -o bdor_genome_assembly
```
Use the maximum amount of processor cores and memory. Assembly will take about three hours on LCC.

___

## Assembly quality
**_Task:_** Install QUAST. <br>
First, in your "programs"  folder, run `wget --no-check-certificate https://sourceforge.net/projects/quast/files/latest/download`. If you don't see a file named `quast-5.2.0.tar.gz`, try downloading the file to your local computer and scp the file to LCC. <br>
Second, decompress the program file `tar -xzvf quast-5.2.0.tar.gz` <br>
Third, go into the directory quast-5.2.0 and run `./install.sh`

**_Task:_** Make and submit a batch script for assembly evaluation. <br>
In your home directory, make a copy of your `batch_header.sh` file `cp batch_header.sh quast.sh` <br>
Add the following commands. Change the paths to YOUR path information.
```
python /path/to/programs/quast-5.2.0/quast.py /path/to/bdor_genome_assembly/scaffolds.fasta
```
Evaluation will take about 1 minute.

**_Task:_** Compare genome assembly stats <br>
Rerun QUAST with the PacBio assembly file Bdor_GCA_030710565.1_ASM3071056v1_genomic.fna in the course folder. Additional information about the assembly is [here](https://www.ncbi.nlm.nih.gov/datasets/genome/GCA_030710565.1/)

