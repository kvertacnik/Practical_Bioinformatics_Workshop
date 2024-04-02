# Software

Bioinformatics relies on free, open-source programs created by the scientific community. Almost all programs use a command-line interface (CLI), so no buttons to click, drop-down menus for options, or search bars for help.


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
   If you do not have root access, make a folder somewhere you have permission to install programs and specify the path to that folder.
4. In the source code folder, run `make`
5. In the source code folder, run `make install`
6. Go to the folder you specified in step 3. It should have a `bin` folder that contains files. These are the executable binaries you will use to run the program.

NB: Regarding step 3, it is good practice to compile a program in a folder OUTSIDE of the source code folder. 

NB: Finally, pay attention to where you install programs. On Linux and macOS `/usr/local/bin` is popular but I like to make a folder at `/usr/local/programs` to keep my bioinformatics programs separate. On Windows make a folder in `C:\Program Files` or `C:\bin`. Just make sure the folder is in your PATH (see below).

### Interpreted and compiled languages (if binary executable is available)
If you're lucky, a pre-compiled binary is available for your specific CPU architecture and operating system. If this is the case, download and decompress the file, place the binary where you install programs, and you're ready to go! 

![Binaries example](assets/command_line/binaries.png)


### Updating your PATH
Environmental variables store specific information about your computer `PATH` (Windows, Linux, macOS) specifies the directories where executable programs are located.

Once in your PATH variable, you can call the name of the program without having to type the full path.

E.g., instead of typing all this to start bwa-mem2
```
/home/kdu224/programs/bwa-mem2-2.2.1_x64-linux/bwa-mem2
```
can be replaced with this
```
bwa-mem2
```

Let's say you just installed a program on your computer, to add the program to your path add this line to your `.bash_profile` file
```
export PATH=/where/is/the/program/directory:$PATH 
```

NB: add the directory, not the program to the path

### Software package managers
If you're installing and running lots of programs, you may want to consider a package manager which automate software installations, configurations, and upgrades. Basically instead of manually managing each program. Of course, this depends on if the program you want is available from the package manager.


#### Homebrew

https://brew.sh/

#### Conda

conda gives you access to Anaconda, which is a repository of thousands of python programs


As a package manager, you can use conda to install, update, and remove packages and their "dependencies" (the packages they depend upon):

    If Anaconda doesn't include a package that you need, you use conda to download and install it.
    If Anaconda doesn't have the version of a package you need, you use conda to update it.

As an environment manager, you can use conda to manage virtual environments:

    If you're not familiar with virtual environments, they allow you to maintain isolated environments with different packages and versions of those packages.
    conda is an alternative to virtualenv, pipenv, and other related tools.

Miniconda is a Python distribution that only includes Python, conda, their dependencies, and a few other useful packages.

Miniconda is a great choice if you prefer to only install the packages you need, and you're sufficiently familiar with conda. (Here's how to choose between Anaconda and Miniconda.)



##Installing programs for GATK
## Let's do some installs!
The rest of the steps for calling SNPs with GATK require a few other pieces of software, which will let us try several different ways for installing software on a cluster. We're going to go through this together, but you can always find info about how to install these on their websites, often easiest if you just google "[software] installation".

1. First up is **fastp**, which can do read trimming among other things. Fastp comes as a pre-built binary, so no installation is required. We just need to download the files to the cluster and make sure they are accessible with `chmod`.
```
wget http://opengene.org/fastp/fastp
chmod a+x ./fastp
```

2. **BWA2** is a common read mapping software. It also comes as a prebuilt binary, but this time the download is a tar.bz2 file, so we need to uncompress it.
```
https://github.com/bwa-mem2/bwa-mem2/releases
wget https://github.com/bwa-mem2/bwa-mem2/releases/download/v2.2.1/bwa-mem2-2.2.1_x64-linux.tar.bz2
tar -xvf bwa-mem2-2.2.1_x64-linux.tar.bz2 
```

3. **BamUtil** is useful for modifying outputs of read mapping (bam/sam files), and this time we're going to create a conda environment to be able to run bamUtil. A conda environments is a useful way to set up one or multiple softwares that are installed with python. Each time you want to use the tools in an environment, you need to activate it similarly to how you load a module. These steps can be done in a few different ways, but here we'll create an empty environment in a specific location, then activate it, and finally install bamUtils into that environment.
```
module load ccs/conda/python
conda create --prefix /scratch/jdu282/bamUtil_env python=3.9
conda activate /scratch/jdu282/bamUtil_env
conda install -c bioconda bamutil
```
When you have a conda environment activated, the shell prompt will change to show that. It's often useful to check what all is installed in a specific environment and we can do that with the following command:
```
conda list
```
Note, that conda is python-based tool. So you need to have python in your PATH whenever you want to use conda. Thus, in a job file, you'd need to include the `module load ccs/conda/python` part as well as (and before) your `conda activate` part. Otherwise, you'll get an error of `-bash: conda: command not found`.

4. Finally, we need **GATK** itself, which as of version 4 contains the tool picard as well (which used to be installed separately).
```
wget https://github.com/broadinstitute/gatk/releases/download/4.4.0.0/gatk-4.4.0.0.zip
unzip  gatk-4.4.0.0.zip
```
GATK uses a java wrapper to call commands, so instead of calling a .jar file, as you often do with java tools (e.g. `java --jar [file.jar]`), we can just call the wrapper as long as java is functioning (thus, we need to load the java module on MCC). Some of these steps also use samtools, so let's load that module too.
```
module load ccs/java/jdk-17.0.2
module load samtools-1.12-gcc-9.3.0-zo3utt7
```
