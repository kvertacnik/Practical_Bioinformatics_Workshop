# Genome manipulation
Let's download a high-quality reference genome for _B. dorsalis_ and do some basic editing of the assembly, namely, fasta sequence extraction and renaming.

## Download the reference genome

Download [this assembly](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_023373825.1/) to your personal folder on the cluster:
1. `wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/023/373/825/GCF_023373825.1_ASM2337382v1/GCF_023373825.1_ASM2337382v1_genomic.fna.gz`


One of the tools we will use for manipulating this genome file requires an uncompressed file, so let's decompress the genome file:
1. `gunzip -c GCF_023373825.1_ASM2337382v1_genomic.fna.gz > GCF_023373825.1_ASM2337382v1_genomic.fna`


Take a look at this genome. Can you predict what each of these commands will do?
```
ls -lh GCF_023373825.1_ASM2337382v1_genomic.fna
wc -l GCF_023373825.1_ASM2337382v1_genomic.fna
grep ">" GCF_023373825.1_ASM2337382v1_genomic.fna
grep ">" GCF_023373825.1_ASM2337382v1_genomic.fna | wc -l
zcat GCF_023373825.1_ASM2337382v1_genomic.fna.gz | grep ">" | wc -l
```
(the last two are a bit repetitive, but using `zcat` with piping is a nice way to access information that's in a compressed file)

**_Question:_** What does `grep ">"` tell us about the genome file?

<details>
<summary>Answer</summary>

Fasta sequences start with a header line. The header line starts with `>`, so when we search for `>`, we're looking for each fasta header.

</details>

<br>

___

## seqtk installation
Now, let's download and install the program [seqtk](https://github.com/lh3/seqtk). The README has directions on how to install the program. Do this in your "programs" folder within your personal folder on the cluster.
1. Make sure you are in your `programs` folder.
2. Download the program `git clone https://github.com/lh3/seqtk.git`
3. Go into the seqtk directory `cd seqtk`
4. Run `ls -l` to see the contents of the directory.
5. Run `make`
6. Run `ls -l` again. You should see something like this:
    ```
    [jdu282@mcc-login001 seqtk]$ ls -l
    total 258
    -rw-r--r-- 1 jdu282 users  18784 Nov 26 14:04 khash.h
    -rw-r--r-- 1 jdu282 users   8634 Nov 26 14:04 kseq.h
    -rw-r--r-- 1 jdu282 users   1086 Nov 26 14:04 LICENSE
    -rw-r--r-- 1 jdu282 users    284 Nov 26 14:04 Makefile
    -rw-r--r-- 1 jdu282 users    809 Nov 26 14:04 NEWS.md
    -rw-r--r-- 1 jdu282 users   1780 Nov 26 14:04 README.md
    -rwxr-xr-x 1 jdu282 users 309312 Nov 26 14:04 seqtk
    -rw-r--r-- 1 jdu282 users  65772 Nov 26 14:04 seqtk.c
    ```
The `make` command created a new file named seqtk that has executable permissions (`-rwxr-xr-x`). This is the program binary.

### Did my installation work???
The next step is to test the binary and see if it works (i.e., see if the program was installed correctly). For any software installation, my first course of action is to try to execute the software and see whether it produces something sensible or some random error. My go-to commands are the following, using seqtk an example:
1. Just trying it out without any options `./seqtk` 
2. If something is installed correctly, "--help" will usually list all the options  `./seqtk --help`
3. "-h" is an abbreviation of "--help". Some softwares allow both abbreviated and long versions of options, some only one or the other `./seqtk -h`

Which one of these will work depends on the software; as you can see, seqtk doesn't like either help command, but does like being called without options (1), indicating the installation was successful. 

Even if you've never used the software you're installing, you should be able to tell whether the output of those commands is something that makes sense. Here, we know this software deals with sequence/genome manipulation and you see options specific to that task, as compared to some error message unrelated to sequencing. 

What if you try the above command without `./`?  
1. `seqtk`

You'll see something like this:
```
[jdu282@mcc-login001 seqtk]$ seqtk
-bash: seqtk: command not found
```

Even though you're in the same directory as seqtk, the command line does not automatically know to look here for the executable file (program). `./` tells the command line to look in the current directory for the executable file.


**_Question:_** Why do you get a similar "command not found" error if you do this?
```
cd ..
./seqtk
```

<details>
<summary>Answer</summary>

Since we moved up one directory, seqtk is no longer in the current directory despite what we are indicating with `./`, so the command line returns an error. 

</details>

<br>

### There are several ways to call a program
#### First, you could use the full path every time you call the software.
We need to get the path (with `pwd`) of where we downloaded seqtk:
```
[jdu282@mcc-login001 seqtk]$ pwd
/pscratch/kdu224_iceland_bootcamps2024/students/julian/programs/seqtk/
```
And then since the executable is called "seqtk", we would use this path to call the program:
```
/pscratch/kdu224_iceland_bootcamps2024/students/julian/programs/seqtk/seqtk
```
Note that you need the path to the seqtk executable, not just the pwd output.

#### Second, we could make a symbolic link (aka symlink, soft link).
This is a shortcut link to where we installed seqtk. So if I go to my home directory and run the following, this creates a symlink in my home directory to where I installed seqtk.
```
ln -s /pscratch/kdu224_iceland_bootcamps2024/students/julian/programs/seqtk/seqtk seqtk_link
```
Now if I'm in my home directory, I can run `./seqtk` and it will call the program. Note that this only works in my home directory because that is where the symlink is.

### Knowing your PATH
The third option is to add seqtk to our PATH. $PATH is an environmental variable that the computer looks at to find executables and software. 

1. Run `echo $PATH` to see what's in your PATH.

The output looks complicated, but it's just a long list of directory locations, separated by `:`, showing all the locations your system will look for a command.

We can add the location of seqtk to PATH by running the following:
```
export PATH=$PATH:/path/to/your/seqtk/installation/directory/
```

This adds that location to your PATH for this session only, so now you can be in any folder and access seqtk by just typing `seqtk`. 

A couple things to NOTE:
* This export only works for your current session. So if you log off on the cluster and log back in, your PATH will no longer contain this location.
* **You need to export the directory location of where the executable is, not the actual executable.** This is a common error when installing and trying out new software.

### ~/.bash_profile
The fourth and permanent option is to add that export path command to a file named `.bash_profile` in your home directory. Let's do this option.

1. Create/open your bash_profile file: `nano ~/.bash_profile`
    * `~/` means your home directory.
    * The `.` makes the file invisible to keep you from accidentally deleting it. 
2. At the bottom of the file add the following but replace /path/to/your/seqtk/directory/ with your information.
    ```
    export PATH=$PATH:/path/to/your/seqtk/directory/
    ```
3. Save and exit nano (`control + x`)
4. Run `source ~/.bash_profile` to reload your .bash_profile
5. Try to call seqtk from somewhere outside of the seqtk directory `seqtk`

Every time you log into this cluster, the system looks in this file and adds all those locations with `export PATH` lines to your PATH. This creates a permanent solution for adding locations to PATH. 

NOTE, when you edit .bash_profile, the changes don't immediately go into effect. As I said, the system looks here every time you **login**. So, once you edit this file, you need to log out and log back in or run `source ~/.bash_profile` to make the change effective. 

___

## Using seqtk
Ok, now that you've got seqtk installed and in your PATH, let's use this tool. You should have seen from above (`grep ">" GCF_023373825.1_ASM2337382v1_genomic.fna | wc -l`) that this genome file has 587 scaffolds, but if you omit the `wc -l` from that command, you can see there are 6 named chromosomes, the mitochondrial genome, and 580 random scaffolds. Knowing that this species has 6 autosomes gives us a pretty good hint that these 580 random scaffolds are probably lower quality or poorly mapping/assembling regions. Let's test that out by calculating the size of each of these entries in this fasta file. We can do that with samtools, and MCC has a module for samtools.

In your personal folder on the cluster, where you previously downloaded the _B. dorsalis_ reference genome, run:
1. `module load samtools-1.12-gcc-9.3.0-zo3utt7`
2. `ls -l`
3. `samtools faidx GCF_023373825.1_ASM2337382v1_genomic.fna`
4. `ls -l`
    * What's new here?
5. `cat GCF_023373825.1_ASM2337382v1_genomic.fna.fai`

The `.fai` file is an index file, and is often required for mapping reads or manipulating genomes and associated data (we'll use genome index files frequently for this class). You can see what the column headers mean [here](http://www.htslib.org/doc/faidx.html), but the main thing for right now is that column 1 is the scaffold name and column 2 is how long that scaffold is. So you can see those first 6 scaffolds (the named chromosomes) are all much longer than the the other scaffolds, and the mitogenome is ~16k bp, which is standard for most insects. 

This is a pretty good genome, and for that reason, we might want to just limit ourselves to the named chromosomes and ignore the rest of the assembly since it's not going to give us any genomic location information. Seqtk can help us do that. Unfortunately, the documentation for seqtk is not great, but what we can use is the `subseq` command, which extracts fasta sequences from a multi-fasta file we provide. 

1. Create a file named `Bdor_chromosomes` with the fasta headers for the chromosome sequences.
    ```
    NC_064303.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 1, ASM2337382v1, whole genome shotgun sequence
    NC_064304.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 2, ASM2337382v1, whole genome shotgun sequence
    NC_064305.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 3, ASM2337382v1, whole genome shotgun sequence
    NC_064306.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 4, ASM2337382v1, whole genome shotgun sequence
    NC_064307.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 5, ASM2337382v1, whole genome shotgun sequencfe
    NC_064308.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 6, ASM2337382v1, whole genome shotgun sequence
    ```

2. Extract just those sequences and write them to a new file.
    ```
    seqtk subseq GCF_023373825.1_ASM2337382v1_genomic.fna Bdor_chromosomes > GCF_023373825.1_ASM2337382v1_chromosomes.fa
    ```

    Actually, most multi-fasta file interpreters will only look at the first part of each scaffold name (before any whitespace), so we could even have just this in our file, and it would work the same:
    ```
    NC_064303.1
    NC_064304.1
    NC_064305.1
    NC_064306.1
    NC_064307.1
    NC_064308.1
    ```

    We don't even need seqtk for this task (although seqtk has other functions which make it useful). We could have used samtools as well, e.g., `samtools faidx GCF_023373825.1_ASM2337382v1_genomic.fna NC_064303.1 NC_064304.1 NC_064305.1 NC_064306.1 NC_064307.1 NC_064308.1 > GCF_023373825.1_ASM2337382v1_chromosomes.fa`

3. Finally, how about we simplify those chromosome names, so they are sensible ("Chromosome1") instead of the NCBI reference number ("NC_064303.1"). 
    ```
    seqtk rename GCF_023373825.1_ASM2337382v1_chromosomes.fa Chromosome > temp && awk '{print $1}' temp > GCF_023373825.1_ASM2337382v1_chromosomes_renamed.fa && rm temp
    ```
    Can you look at the poor help documentation of seqtk and figure out what this command is doing step by step?

    <details>
    <summary>Answer</summary>

    The usage for seqtk rename is `seqtk rename <in.fq> [prefix]`. What the function `rename` does is number the fasta sequences starting with 1 and uses the provided prefix (in this case "Chromosome").

    So we start by telling the program that the fasta file is GCF_023373825.1_ASM2337382v1_chromosomes.fa and that we want the sequence names to start with "Chromosome". The rename function adds numbers, and we redirect the output (the renamed assembly) to the temp file. 
    
    But now our fasta headers look something like this: `>Chromosome1 Bactrocera dorsalis isolate Fly_Bdor chromosome 1, ASM2337382v1, whole genome shotgun sequence`. To get rid of everything after >Chromosome#, we tell awk to read the temp file line-by-line, and for lines with whitespace (like the spaces in our fasta headers) to only keep the first item separated by a space ($1), and redirect the output to GCF_023373825.1_ASM2337382v1_chromosomes_renamed.fa.

    Finally the temp file is deleted.

    </details>
    <br>

4. Check that the final version of the assembly has six fasta sequences named Chromosome1-6 `grep ">" GCF_023373825.1_ASM2337382v1_chromosomes_renamed.fa`. The output should look like this:
    ```
    >Chromosome1
    >Chromosome2
    >Chromosome3
    >Chromosome4
    >Chromosome5
    >Chromosome6
    ```


___

## Summary
* In preparation for genotyping, we downloaded the reference genome, extracted the six chromosome sequences, and renamed their fasta headers.