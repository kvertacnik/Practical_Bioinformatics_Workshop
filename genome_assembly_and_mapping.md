# Genome assembly and mapping
Goal: (1) Create a _Bactrocera dorsalis_ genome assembly from small-insert, paired-end (150 bp) shotgun Illumina sequencing reads. (2) Map reads from a sequenced individual to the assembly.

## Assembly read data
The sequencing reads are available in the course folder on MCC `/pscratch/jdu282_brazil_bootcamp2023/Bdor_reads`

Instead of copying the files, we are going to make symlinks (symbolic links). Why? To minimize memory use--this way, the course only has one copy of the data instead of everyone in the class downloading the same 10 GB files. 

**_Task:_** Make symlinks to the sequencing reads for assembly. <br>
1. In your home directory on MCC, make a folder named "sequencing_reads".
2. Go into the folder. <br>
3. Run `ln -s /pscratch/jdu282_brazil_bootcamp2023/Bdor_reads/Bdor_ref_Illumina_SRR901643_R1.fastq` <br>
4. Repeat for Bdor_ref_Illumina_SRR901643_R2.fastq. <br>
5. Run `ls` and you should see the two files in light blue text.

These reads were downloaded from the NCBI SRA (short read archive). Learn more about the data [here](https://www.ncbi.nlm.nih.gov/sra/SRX306436[accn])

___

## Read quality assessment
**_Task:_** Make and submit a batch script for fastqc.
1. In your home directory on MCC, make a copy of your batch_header.sh file `cp batch_header.sh fastqc.sh` <br>
2. Move fastqc.sh to your sequencing_reads folder.
3. Go into your sequencing_reads folder.
4. Open fastqc.sh with nano.
5. In the header, change `#SBATCH --ntasks=1`
6. Add the following lines after the header:
```
singularity run --app fastqc0113 /share/singularity/images/ccs/conda/amd-conda6-rocky8.sinf fastqc Bdor_ref_Illumina_SRR901643_R1.fastq 

singularity run --app fastqc0113 /share/singularity/images/ccs/conda/amd-conda6-rocky8.sinf fastqc Bdor_ref_Illumina_SRR901643_R2.fastq 
```
7. Save and close nano.
8. Submit your job `sbatch fastqc.sh` (job should take ~6 minutes)
9. Download the files `Bdor_ref_Illumina_SRR901643_R1_fastqc.html` and `Bdor_ref_Illumina_SRR901643_R2_fastqc.html` to your local computer (hint: use `scp`) and view in a web browser.

[Guide to interpreting FastQC reports](https://dnacore.missouri.edu/PDF/FastQC_Manual.pdf)

___
## Read trimming
**_Task:_** Install the read trimming program `fastp`
1. In your home directory on MCC, make a folder named "programs".
2. Go into the folder.
3. To install fastp, follow the directions to "download the latest prebuilt binary for Linux users" [here](https://github.com/OpenGene/fastp#or-download-the-latest-prebuilt-binary-for-linux-users).

**_Task:_** Make and submit a batch script for read trimming.
1. In your home directory on MCC, make a copy of your batch_header.sh file `cp batch_header.sh fastp.sh` <br>
2. Move fastp.sh to your sequencing_reads folder.
3. Go into your sequencing_reads folder.
4. Open fastp.sh with nano.
5. In the header, change `#SBATCH --ntasks=16`
6. Add the following lines after the header. Change the fastp path to your path information.
```
/path/to/programs/fastp --in1 Bdor_ref_Illumina_SRR901643_R1.fastq --in2 Bdor_ref_Illumina_SRR901643_R2.fastq --out1 Bdor_ref_Illumina_SRR901643_R1_trimmed.fq.gz --out2 Bdor_ref_Illumina_SRR901643_R2_trimmed.fq.gz --detect_adapter_for_pe --overrepresentation_analysis --length_required 100 --compression 9 --thread 16
```
7. Save and close nano.
8. Submit your job `sbatch fastp.sh` (job should take ~20 minutes).

**_Question:_** Look at the fastp [manual](https://github.com/OpenGene/fastp#polyx-tail-trimming). Why did we not use the `--trim_poly_g` setting? Hint, see the NCBI SRA page for our sequencing data. What type of sequencing instrument was used to generate the data?

___
## Compare raw and trimmed fastq files
Rerun FastQC on the trimmed fastp output files.

___

## Assembly
**_Task:_** Make and submit a batch script for genome assembly. <br>
1. In your home directory on MCC, make a copy of your batch_header.sh file `cp batch_header.sh spades.sh` <br>
2. Open spades.sh with nano.
3. Add the following lines after the header. Change the reads paths to your path information.
```
singularity run --app spades3155 /share/singularity/images/ccs/conda/amd-conda9-rocky8.sinf spades.py --pe1-1 /pscratch/jdu282_brazil_bootcamp2023/Bdor_reads/Bdor_ref_Illumina_SRR901643_R1_trimmed.fq.gz --pe1-2 /pscratch/jdu282_brazil_bootcamp2023/Bdor_reads/Bdor_ref_Illumina_SRR901643_R2_trimmed.fq.gz --threads 32 -o Bdor_ref_assembly
```
3. Submit your job by running `sbatch spades.sh`. Assembly will take about three hours. <br>
4. Once assembly is complete, go into the "Bdor_genome_assembly" folder and make a copy of the assembly by running `cp scaffolds.fasta Bdor_ref_genome.fasta`

**_Question_**: We ran `cp scaffolds.fasta Bdor_ref_genome.fasta` to make a copy of the assembly. We will use for downstream analyses. Why did we make a copy instead of using the original `scaffolds.fasta` file?

___

## Assembly quality
### Busco
**_Task:_** Make and submit a batch script for genome assembly. <br>
1. In your home directory on MCC, make a copy of your batch_header.sh file `cp batch_header.sh busco.sh` <br>
2. Open busco.sh with nano.
3. Add the following lines after the header. Change the genome assembly path to your path information.
```
singularity run --app busco546 /share/singularity/images/ccs/conda/amd-conda10-rocky8.sinf busco --in /path/to/your/Bdor_ref_assembly/Bdor_ref_scaffolds.fasta --out Bdor_ref_busco --mode genome --lineage_dataset arthropoda_odb10 --cpu 32 
```
4. Submit your job by running `sbatch busco.sh`. Analysis will take about 15 minutes. <br>
5. See file `short_summary.specific.arthropoda_odb10.Bdor_ref_busco.txt`

[Interpreting BUSCO reports](https://busco.ezlab.org/busco_userguide.html#interpreting-the-results)

###  QUAST

**_Task:_** Install QUAST.
1. Run `module load ccs/conda/python`
2. In your programs folder, run `wget --no-check-certificate https://sourceforge.net/projects/quast/files/latest/download`. If you don't see a file named `quast-5.2.0.tar.gz`, try downloading the file to your local computer and then scp the file to your programs folder. <br>
3. Decompress the file `tar -xzvf quast-5.2.0.tar.gz` <br>
4. Go into the `quast-5.2.0` directory and run `./install.sh`

**_Task:_** Make and submit a batch script for assembly evaluation. <br>
1. In your home directory, make a copy of your `batch_header.sh` file `cp batch_header.sh quast.sh` <br>
2. Add the following commands. Change the paths to your path information.
```
/path/to/your/programs/quast-5.2.0/quast.py /path/to/your/Bdor_ref_assembly/Bdor_ref_scaffolds.fasta --min-contig 1000 --threads 32
```
3. Submit your job by running `sbatch quast.sh`. Analysis will take about 1 minute. <br>
4. See file `report.txt`

N50 is a popular metric of assembly quality. Learn more about N50 [here](https://www.molecularecologist.com/2017/03/29/whats-n50/)

**_Task:_** Compare Illumina and PacBio genome assembly stats
1. Download the Bdor PacBio genome assembly `wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCA/030/710/565/GCA_030710565.1_ASM3071056v1/GCA_030710565.1_ASM3071056v1_genomic.fna.gz`
2. Rename this assembly `mv GCA_030710565.1_ASM3071056v1_genomic.fna.gz Bdor_ref_PacBio_GCA_030710565.1_ASM3071056v1_genomic.fna.gz`
2. Rerun BUSCO and QUAST with this genome assembly.

Additional information about the PacBio assembly is [here](https://www.ncbi.nlm.nih.gov/datasets/genome/GCA_030710565.1/)

___

## Sample mapping
### Get the sequence data
**_Task:_** Make symlinks to the sequencing reads for alignment. <br>
1. Go into your "sequencing_reads" folder. <br>
2. Run `ln -s /pscratch/jdu282_brazil_bootcamp2023/Bdor_reads/mapping/Bdor_B70_SRR22045853_R1.fastq` <br>
3. Repeat for Bdor_B70_SRR22045853_R2.fastq. <br>

Learn more about the data [here](https://www.ncbi.nlm.nih.gov/sra/?term=SRR22045853)

### Read trimming
**_Task:_** Make and submit a batch script for read trimming. <br>
1. Rerun fastp as before. Add the following lines after the header. Change the fastp path to your path information.
```
/path/to/programs/fastp --in1 Bdor_B70_SRR22045853_R1.fastq --in2 Bdor_B70_SRR22045853_R2.fastq --out1 Bdor_B70_SRR22045853_R1_trimmed.fq.gz --out2 Bdor_B70_SRR22045853_R2_trimmed.fq.gz --detect_adapter_for_pe --overrepresentation_analysis --length_required 75 --compression 9 --thread 16 
```
___

## Read mapping
**_Task:_** Install bwa-mem2.
1. In your "programs" directory, run ` wget https://github.com/bwa-mem2/bwa-mem2/releases/download/v2.2.1/bwa-mem2-2.2.1_x64-linux.tar.bz2`
2. Decompress the file `tar -xjvf bwa-mem2-2.2.1_x64-linux.tar.bz2`
3. The program `bwa-mem2` is in the folder `bwa-mem2-2.2.1_x64-linux`

**_Task:_** Index the reference genome assembly.
1. In your home directory on MCC, make a folder named "mapping".
2. In your home directory on MCC, make a copy of your batch_header.sh file `cp batch_header.sh bwa2_index.sh` <br>
3. Move bwa2_index.sh to your mapping folder.
4. Go into your mapping folder.
5. Open bwa2_index.sh with nano.
6. Add the following lines after the header:
```
/path/to/your/programs/bwa-mem2-2.2.1_x64-linux/bwa-mem2 index /path/to/your/Bdor_ref_assembly/Bdor_ref_scaffolds.fasta
```
7. Submit your job by running `sbatch bwa2_index.sh`. Analysis will take about 2 minutes. <br>

**_Task:_** Align the reads from the Bdor sample
1. In your home directory on MCC, make a copy of your batch_header.sh file `cp batch_header.sh bwa2_mapping.sh` <br>
3. Move bwa2_mapping.sh to your mapping folder.
4. Go into your mapping folder.
5. Open bwa2_mapping.sh with nano.
6. Add the following lines after the header:
```
/path/to/your/programs/bwa-mem2-2.2.1_x64-linux/bwa-mem2 mem -t 32 /path/to/your/Bdor_ref_assembly/Bdor_ref_scaffolds.fasta Bdor_B70_SRR22045853_R1_trimmed.fq.gz Bdor_B70_SRR22045853_R2_trimmed.fq.gz > Bdor_B70_SRR22045853_paired.sam

singularity run --app samtools113 /share/singularity/images/ccs/ngstools/samtools-1.13+matplotlib-bcftoools-1.13.sinf samtools view -b --threads 32 Bdor_B70_SRR22045853_paired.sam | samtools sort -o Bdor_B70_SRR22045853.bam --output-fmt BAM --threads 32
```
7. Submit your job by running `sbatch bwa2_mapping.sh`. Analysis will take about 10 minutes. <br>

`Bdor_B70_SRR22045853.bam` is the file with your mapped reads. 
