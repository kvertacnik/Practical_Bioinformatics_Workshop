# Genome manipulation

## Download the reference genome
Let's download a high-quality reference genome for _B. dorsalis_ and do some basic editing of the assembly, namely, fasta sequence extraction and renaming.

Download [this one](https://www.ncbi.nlm.nih.gov/datasets/genome/GCF_023373825.1/) to your personal folder on the cluster:
```
wget https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/023/373/825/GCF_023373825.1_ASM2337382v1/GCF_023373825.1_ASM2337382v1_genomic.fna.gz
```

One of the tools we will use for manipulating this genome file requires an uncompressed file, so let's decompress the genome file:
```
gunzip -c GCF_023373825.1_ASM2337382v1_genomic.fna.gz > GCF_023373825.1_ASM2337382v1_genomic.fna
```

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

Fasta sequences start with a header line. The header line starts with `>`, so when we search for `>`, we're looking at each fasta entry.

</details>
<br>

___

## seqtk installation
Now, let's download and install the program [seqtk](https://github.com/lh3/seqtk). The README has directions on how to install the program. Do this in your personal folder on the cluster (I suggest making a "programs" folder and installing seqtk in there):
```
git clone https://github.com/lh3/seqtk.git
cd seqtk
ls -l
make
```
If you run `ls -l` again, you should see something like this:
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
The `make` command created a new file that has the name of the software (seqtk) and executable permissions in the first column (`-rwxr-xr-x`). This is the program binary.

The next step is to test the binary and see if it works (i.e., see if the program was installed correctly). For any software installation, my first course of action is to try to execute the software and see whether it produces something sensible or some random error. 

My go-to commands are the following, using seqtk an example:
```
./seqtk           # just trying it out without any options
./seqtk --help    # usually help will bring up all the options if something is installed correctly
./seqtk -h        # some softwares allow both abbreviated and long versions of options, some only one or the other
```
Which one of these will work depends on the software; as you can see, seqtk doesn't like either help command, but does like being called without options, indicating the installation was successful. 

Even if you've never used the software you're installing, you should be able to tell whether the output of those commands is something that makes sense. Here, we know this software deals with sequence/genome manipulation and you see options specific to that task, as compared to some error message  that has nothing to do with sequencing. 

What if you try the above command without `./`?  You'll probably see something like this:
```
[jdu282@mcc-login001 seqtk]$ seqtk
-bash: seqtk: command not found
```

Even though you're in the same directory as seqtk, the command line does not automatically know to look here for the executable file (program). `./` tells the command line to look in the current directory for the executable file.


**_Question:_** Why do you get a similar "command not found" error if you do this?
```
cd ../
./seqtk
```

<details>
<summary>Answer</summary>

Since we moved up one directory, seqtk is no longer in the current directory despite what we are indicating with `./`, so the command line returns an error. 

</details>

<br>

There's a few options to solve this issue, and increase the flexibility for where you can install software. First, you could use the full path every time you call any software. So we just need to get the `pwd` of the location we downloaded seqtk:
```
[jdu282@mcc-login001 seqtk]$ pwd
/pscratch/jdu282_brazil_bootcamp2023/programs/seqtk
```
Now we can be in any directory and call seqtk using `/pscratch/jdu282_brazil_bootcamp2023/programs/seqtk/seqtk`. Give it a try by cd'ing somewhere else and calling your copy of seqtk. NOTE that you need the path to the seqtk executable, not just the pwd output.

Second, we could make a symbolic link (or soft link) in some directory that is basically a shortcut link to where we installed seqtk. So if you `cd $HOME` and then do the following, you've created a shortcut to this install position in your home directory.
```
ln -s /path/to/your/seqtk/executable
```
NOTE, here we're referring to the executable file itself with the soft link. So if you're in your home directory, you can then run `./seqtk`, and you should get a sensible output.

### Knowing your PATH
The third option is to add seqtk to our PATH. $PATH is an environment that the cluster system (or your own personal computer) looks in to find executables and software. We can see what's in our PATH with `echo $PATH`. The output to that might look complicated, but it's just a long list of directory locations, separated by `:` showing all the locations your system will look for a command every time you type that command.

We can add the location of seqtk to PATH with the following:
```
export PATH=$PATH:/path/to/your/seqtk/installation/directory/
```

This adds that location to your PATH for this session only, so now you can cd anywhere and access seqtk by just typing `seqtk`. 

Give it a try with seqtk. A couple things to NOTE:<br>
1. This export only works for your current session. So if you log off on the cluster and log back in, your PATH will no longer contain this location (see below, bash_profile for permanent solution)
2. **You need to export the directory location of where the executable is, not the actual executable.** This is a common error when installing and trying out new software.

### bash_profile
The permanent solution is to add that export path command to a file named `.bash_profile` in your home directory. We will need to generate it manually. The `.` makes the file invisible to keep you from accidentally deleting it. <br>
1. Create your bash_profile file: `nano ~/.bash_profile` <br>
(tilde `~/` is a nice shortcut for your home directory, so is $HOME; both are shorter than doing cd /home/[username]).
3. Paste in the following:
```
# .bash_profile

# User specific environment and startup programs
	
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
	. ~/.bashrc
fi
```
4. Now below that, you can paste in the `export PATH` command that you used before. So it should look something like this:
```
# .bash_profile

# User specific environment and startup programs
	
# Get the aliases and functions
if [ -f ~/.bashrc ]; then
	. ~/.bashrc
fi

export PATH=$PATH:/path/to/your/seqtk/installation/directory/
```

Now every time you log into this cluster, the system looks in this file and adds all those locations with `export PATH` lines to your PATH. This essentially creates a permanent solution for adding locations to PATH. 

NOTE, when you edit this file, the changes don't immediately go into effect. As I said, the system looks here every time you **login**. So, once you edit this file, you need to log out and log back in or run `source ~/.bash_profile` to make the change effective. 

___

## Using seqtk
Ok, now that you've got seqtk installed and in your PATH, let's use this tool. You should have seen from above (`grep ">" GCF_023373825.1_ASM2337382v1_genomic.fna | wc -l`) that this genome file has 587 scaffolds, but if you omit the `wc -l` from that command, you can see there are 6 named chromosomes, the mitochondrial genome, and 580 random scaffolds. Knowing that this species has 6 autosomes gives us a pretty good hint that these 580 random scaffolds are probably lower quality or poorly mapping/assembling regions. Let's test that out by calculating the size of each of these entries in this fasta file. We can do that with samtools, and MCC has a module for samtools.

In your personal folder on the cluster, where you previously downloaded the _B. dorsalis_ reference genome, run:
```
module load samtools-1.12-gcc-9.3.0-zo3utt7
samtools faidx GCF_023373825.1_ASM2337382v1_genomic.fna
ls -l       # what's new in here?
nano GCF_023373825.1_ASM2337382v1_genomic.fna.fai
```
The `.fai` file is an index file, and is often a requirement for mapping reads or manipulating genomes and associated data (we'll use these frequently for this class). You can see what the column headers mean [here](http://www.htslib.org/doc/faidx.html), but the main thing for right now is that column 1 is the scaffold name and column 2 is how long that scaffold is. So you can see those first 6 scaffolds (the named chromosomes) are all much longer than the the other scaffolds, and the mitogenome is ~16k bp, which is standard for most insects. 

This is a pretty good genome, and for that reason, we might want to just limit ourselves to the named chromosomes and ignore the rest of the assembly since it's not going to give us any location information (gene proximity). Seqtk can help us do that. Unfortunately, the documentation for seqtk is not great, but what we can use is the `subseq` command, which extracts fasta sequences from a multi-fasta file we provide. 

1. From the output above (listing the chromosomes in the assembly file), create a file named "Bdor_chromosomes" with the following contents:
```
NC_064303.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 1, ASM2337382v1, whole genome shotgun sequence
NC_064304.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 2, ASM2337382v1, whole genome shotgun sequence
NC_064305.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 3, ASM2337382v1, whole genome shotgun sequence
NC_064306.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 4, ASM2337382v1, whole genome shotgun sequence
NC_064307.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 5, ASM2337382v1, whole genome shotgun sequencfe
NC_064308.1 Bactrocera dorsalis isolate Fly_Bdor chromosome 6, ASM2337382v1, whole genome shotgun sequence
```

2. Extract just those sequences and write them to a new file with:
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

Actually, we don't even need seqtk for this task (although it can do other processes which make it useful). We could have used samtools as well: `samtools faidx GCF_023373825.1_ASM2337382v1_genomic.fna NC_064303.1 NC_064304.1 NC_064305.1 NC_064306.1 NC_064307.1 NC_064308.1` Give these a try to make sure I'm not lying to you!

3. Finally, how about we simplify those chromosome names, so they are sensible ("Chromosome1") instead of the NCBI reference number ("NC_064303.1"). 
```
seqtk rename GCF_023373825.1_ASM2337382v1_chromosomes.fa Chromosome > temp && awk '{print $1}' temp > GCF_023373825.1_ASM2337382v1_chromosomes_renamed.fa && rm temp
```
Can you look at the poor help documentation of seqtk and figure out what this command is doing step by step?

<details>
<summary>Answer</summary>

The usage for seqtk rename is `seqtk rename <in.fq> [prefix]`. What the function `rename` does is number the fasta sequences starting with 1 and uses the provided prefix (in this case "Chromosome").

So we start by telling the program that the fasta file is GCF_023373825.1_ASM2337382v1_chromosomes.fa and that we want the sequence names to start with "Chromosome". The rename function adds numbers, and we redirect the output (the renamed assembly) to the temp file. 

But now our fasta headers look something like this: `>Chromosome1 Bactrocera dorsalis isolate Fly_Bdor chromosome 1, ASM2337382v1, whole genome shotgun sequence`. To get rid of everything after >Chromosome#, we tell awk to read the temp file line-by-line, and for lines with whitespace (like the spaces our fasta headers) to only keep the first item separated by a space ($1), and redirect the output to GCF_023373825.1_ASM2337382v1_chromosomes_renamed.fa.

Finally the temp file is deleted.

</details>

<br>

4. Check that the final version of the assembly has six fasta sequences named Chromosome1-6 (hint: use one the grep commands we used earlier)