# Software


## The README

<p align="left">
  <img src="assets/command_line/readme.png" width="25%">
</p>

Most programs come with a README file, a text file that documents how to use the program. It is often the first thing people look at when working with a new program.

Besides what the program does and how to use it, the README can have information on system requirements (e.g., other software the program needs), installation instructions, and links to other resources (e.g., website or manual with detailed documentation).

Many basic questions about the program can be addressed by looking in the README (unless you have a poorly written one).

**_Task:_** Look at a README file and get an idea of what kind of information it contains. Download the program fastp [here](https://github.com/OpenGene/fastp). Unzip the file by clicking on it, and then open the resulting folder to find the README. 

<p align="left">
  <img src="assets/command_line/fastp_download.png" width="50%">
</p>

___

## Installation
The coding language a program is written in dictates how it is installed.

A program's **source code** is the human-readable instruction set that programmers write (e.g., Python, R, C, C++). **Binary** is the machine-readable version of source code, where 0’s and 1’s represent machine code that the CPU can run.

Programming languages can be broadly categorized into two types based on how they convert source code into binary: compiled and interpreted.
* An interpreted language (e.g., Python, R) uses an interpreter which reads the source code line by line and executes each line as it is read. The interpreter translates the source code into machine code at runtime. 

* A compiled language (e.g., C, C++) is converted into machine code before runtime. During "compiling", a compiler program converts the source code into an executable file that can be run on the target machine. The compiled code is optimized for the specific hardware and operating system of the machine on which it is intended to run.

### Compiled languages: installing from source code
1. Download the software package. They are often compressed in `.tar.gz` format; use `tar -xzvf package.tar.gz` to decompress.
2. Go into the decompressed source code folder. There should be a file named "configure".
3. In the source code folder, run `./configure --prefix=/local/installation/folder` <br>
   If you do not have root access (like on the cluster), make a folder where you have permission to install programs and specify the path to that folder.
4. In the source code folder, run `make`
5. In the source code folder, run `make install`
6. Go to the folder you specified in step 3. It should now have a `bin` folder that contains files. These are the executable binaries for the program.

### Interpreted and compiled languages (if binary executable is available)
If you're lucky, a pre-compiled binary is available for your specific CPU architecture and operating system. If this is the case, download and decompress the file, place the binary where you install programs, and you're ready to go! 

![Binaries example](assets/command_line/binaries.png)

NB: Keep your installed bioinformatics programs in a single place. On Linux and macOS that is typically `/usr/local/bin`, although I like to make a directory at `/usr/local/bin/programs` to keep my bioinformatics programs separate. On Windows make a directory in `C:\Program Files`. Regardless, make sure the directory is in your PATH.

### Updating your PATH
Environmental variables store information specific to your computer. Software can access these variables to know how things are set up on your computer. The `PATH` variable (Windows, Linux, macOS) specifies the directories where executable programs are located.

Once in your PATH variable, you can call the program by name without having to type the full path.

E.g., instead of typing `/usr/local/bin/programs/bwa-mem2-2.2.1_x64-linux/bwa-mem2`, add the directory bwa-mem2-2.2.1_x64-linux to your path and you will be able to call the program by only typing `bwa-mem2`

To add a program to your path add this line to your `.bash_profile` file
```
export PATH=/where/is/the/program/directory:$PATH 
```
and then refresh your command line shell by running `source ~/.bash_profile`

NB: Do not add the program itself to the path, just the directory the holds the program.

### Package managers
Package managers simplify software management by automating installation, configuration, and upgrades. They don't have every program but it's nice when they have the one you need. 

#### Conda
Anaconda is a Python distribution with thousands of packages. Conda is the package manager for Anaconda. Instead of the complete Anaconda distribution, [Miniconda](https://docs.anaconda.com/free/miniconda/miniconda-install/) is a bare-bones version that comes with Conda, Python, and a few other useful packages.

In addition to package management, Conda is also an environment manager where you can use Conda to create and manage virtual environments (isolated environments with different packages and versions of those packages).

A conda environments is a useful way to set up one or multiple softwares that are installed with python. Each time you want to use the tools in an environment, you need to activate it similarly to how you load a module on the cluster. 

To create a new Conda environment:
1. `conda create --name ENV_NAME python` 
2. `conda activate ENV_NAME`

Once the environment is created, you can call it anytime with step 2. When a conda environment is activated, the shell prompt will change to show that.
<p align="left">
  <img src="assets/command_line/conda_prompt.png" width="40%">
</p>

To install programs in a Conda environment run `conda install PACKAGE_NAME`

To see what programs are installed in a Conda environment run `conda list`

To exit the environment run `conda deactivate`

___

## Getting help with `-h`




___

## Installing programs and setting up the environment for read mapping and SNP calling with GATK
Do all these installations on the cluster.

### fastp
This program does read trimming, among other things. Fastp comes as a pre-built binary, so no installation is required. We just need to download the files to the cluster and make sure they are accessible with `chmod`.

```
wget http://opengene.org/fastp/fastp
chmod a+x ./fastp
```

### BWA2
This is a popular read mapping (alignment) program. It also comes as a prebuilt binary, but this time the download is a tar.bz2 file, so we need to decompress it.

```
wget https://github.com/bwa-mem2/bwa-mem2/releases/download/v2.2.1/bwa-mem2-2.2.1_x64-linux.tar.bz2
tar -xvf bwa-mem2-2.2.1_x64-linux.tar.bz2 
```

### bamUtil
This program modifies read mapping output files (BAM/SAM files). This time we're going to create a conda environment for bamUtil. These steps can be done in a few different ways, but here we'll create an empty environment in a specific location, activate it, then install bamUtils into that environment.

```
module load ccs/conda/python
conda create --prefix /your/cluster/folder/bamUtil_env python=3.9
conda activate /your/cluster/folder/bamUtil_env
conda install -c bioconda bamutil
```

On the cluster, when you submit a job file that requires a Conda environment, include the line `module load ccs/conda/python` before the `conda activate` line. Otherwise, you'll get the error `-bash: conda: command not found`.

### samtools
Samtools is already installed on the cluster, so all we need to do is load the module.
```
module load samtools-1.12-gcc-9.3.0-zo3utt7
```

### GATK
```
wget https://github.com/broadinstitute/gatk/releases/download/4.5.0.0/gatk-4.5.0.0.zip
unzip 4.5.0.0.zip
```

GATK uses a Java wrapper to call commands, so instead of calling a .jar file, as you often do with java tools (e.g. `java --jar file.jar`), we can just call the wrapper as long as java is functioning (thus, we need to load the cluster's Java module).

```
module load ccs/java/jdk-17.0.2
```