# Introduction to command line
Bioinformatics relies on free, open-source programs created by the scientific community. The vast majority of programs are solely text-based, so no buttons to click, drop-down menus for options, or search bars for help.

## Getting started
Linux: Open the program "Terminal" <br>
macOS: Open the program "Terminal" and run `chsh -s /bin/bash` <br>
Windows: Install and open "Ubuntu" following the "Install WSL command" section [here](https://learn.microsoft.com/en-us/windows/wsl/install#install-wsl-command).

In addition to `command line`, you may have heard the terms `terminal`, `console`, `shell`, or `CLI`. Technically they are different things, but people often use these words interchangeably.

Command line uses the keyboard to interact with the computer. You enter text commands to navigate, run programs, and configure your computer system. It's the same content as the point-and-click graphical user interface (GUI) you're more familiar with.

**_Question:_** Why do you think people want to work with computers solely through text, i.e., what are the advantages of command line over GUIs?

<details>
<summary>Answer</summary> 

Function over form. Some reasons include:
* Resource usage. Because it does not need to handle graphics, command line uses less computer resources like memory and processing power.
* Coding efficiency. If the same information can be conveyed through text, it's faster to code and release your program if you don't need to make graphical screens.

</details>

<br>

1. To run a command, type the command at the prompt and then press enter. Try this with `pwd`
<p align="left">
  <img src="assets/command_line/prompt.png" width="12%">
</p>

You've just run your first command!

___

## Tips
* Throughout these modules, the commands I want you to run are labeled "**_Task:_**" or are preceded by a number, e.g., `1. pwd`

* If you see something like `<user name>` or `[OPTIONS]`, replace the entire thing (including the < > or [ ]) with your information. So something like `<user name>` --> `kdu224`

* If you see something like  `/path/to/directory`, replace this with your path information.

* Do not include spaces (or any other whitespace) in file names.

* The command line is case-sensitive.

* Use tab-completion to reduce typing (see next section).

* When looking at code, `#` is used to mark the start of a comment, meaning that anything following this character on the same line is ignored and not run as code. It also means that you don't need to include the comments in your scripts (because they're not code).

* "Current working directory" refers to the current folder your Terminal is in.

___

## Directory navigation
![Directory structure](assets/command_line/linux_directory_structure.png)

Computer files are organized in a series of nested, hierarchical directories (folders) starting with the root directory (`/`). Each user has their own home directory where their files are stored. To distinguish files from directories, directory names end in `/` while file names do not, however, directories are often written without the `/` and it still works.

NB: "Folder" and "directory" are synonymous and used interchangeably.

### Paths
Each directory and file has a "path". Think of it as an address within the hierarchical structure. Paths start at root (`/`) and go down each level until it reaches the directory or file. For example, the path to user1's desktop directory is `/home/user1/Desktop/`

For directories, the last backslash `/` can be omitted, so the path to user1's desktop directory can also be written as `/home/user1/Desktop`

The path to your home directory can be abbreviated as `~`, so the path to user1's desktop directory can also be written as `~/Desktop`

On macOS you can get a file or folder path by dropping the item into the Terminal or by right-clicking on the file while holding down the option key.
<p align="left">
  <img src="assets/command_line/drag_and_drop_path.png" width="60%">
</p>

### Tab-completion
Instead of typing the full name, **paths and files can be auto-filled using the tab key** (tab-completion). For example, typing `~/De` `tab` will automatically complete with `~/Desktop`

**_Question:_** Based on the above figure, why does `~/D` `tab` fail to tab-complete but `~/De` `tab` automatically fills in `~/Desktop`? Try it!

<details>
<summary>Answer</summary> 

There are two directories that start with "D" (Desktop and Documents). Tab-complete doesn't know which one you want, so it can't complete the path for you. Once you have a unique word, in this case `~/De`, the only directory it can be is the Desktop directory, so tab-complete works. 

</details>

<br>

NB: Command line has a hard time understanding whitespace in file or directory names. For example, is `my file.txt` one file or two files named "my" and "file.txt"? **Best practice is to never have whitespace in names.** "my file.text" should be "my_file.text", "my-file.txt", "myFile.txt", etc. Also, do not include any symbols in file names except for `-` or `_` or `.`

___

## Basic commands

`pwd` = "print working directory". It answers the question "Where am I?" by showing the path to the current directory. 
* `tree` displays a visualization of pwd.

`ls` = "list". It shows the contents of the current directory. 
* To show long format information like file size or last access date use `ls -lh`

`cd` = "change directory", with the usage `cd /path/to/directory`
* To move up (go back) one level use `cd ..`
* To move up two levels use `cd ../..`, three levels `cd ../../..`, etc.

`mkdir` =  "make directory", with the usage `mkdir new_folder_name`. It makes a new directory within the current directory.

`cp` = "copy", with the usage `cp file_name copy_file_name`

`mv` = "move", with the usage `mv file_name /path/to/new/directory`. If a file with the same name already exists, `mv` will overwrite the original file without warning. 
* To rename a file, use `mv file_name new_file_name`

`rm` = "remove", with the usage `rm file_name`. Note that command line does not have an "undo" function so **_deletions are permanent_**.

`cat` = "concatenate". It combines multiple files `cat file1 file2 > file1and2` 
* It can also print the contents of a file to the screen `cat file_name`

<br>

For directories, add `-r` to `cp` and add `-rf` to `rm`. For example, use `rm -rf folder_name` to delete a folder.

**_Task:_** Go to your desktop directory. Make a folder named `examples`

NB: If the command line gets stuck or you want to cancel a running command press `control + c`

NB: Command line saves a history of executed commands. Use the up arrow to go through your most recent commands; this is useful when you need to rerun the same command.

___

## Text files
Command line cannot read files written in graphical programs like Word and Excel. 

What happens when we try to look at an Excel file?

![Excel example](assets/command_line/head_excel.png)

This isn't helpful. Instead, files are typically in plain text. Multiple text editors are built into the command line; these are programs within Terminal, so a program within a program.

We will use the `nano` text editor. 

**_Task:_** Use the command line to make and edit new text file.
1. Go into your examples directory by running `cd ~/Desktop/examples`
2. Open the text editor by running `nano` or `nano hello.txt`
3. Type `Hello World!`
4. Exit nano `control+x`. It will ask you to "Save modified buffer". Type "Y", use the file name `hello.txt`, and then press `Enter`

**_Question:_** Without clicking on anything, how can we use the command line to check if you made a new file in your examples folder?

<details>
<summary>Answer</summary> 

Use the `ls` command.

</details>

<br>

We can also make a new, empty file without opening a text editor by running `touch new_file_name`

Did you catch the three ways to make a new file?

* `nano`
* `nano new_file_name`
* `touch new_file_name`

<br>

NB: File extensions are important for understanding file contents. For example, a file named "sample1" could be anything, e.g., a text file with metadata or a fastq file with sequence reads. Naming the file sample1.txt or sample1.fq avoids this problem.

___

## Editing and viewing files
We will create a data file and use command line tools to extract information or manipulate the file **without opening the file**. These tools have many options; we'll look at the basics. 

**_Task:_** Create a text file.
1. Go to your "examples" directory
2. Make a new text file `nano log.txt`
3. Copy and paste the text below.
4. Save the file and exit nano `control+x`

```
Timestamp       Category        Message
1598843202      INFO    Booting up system
1598843402      INFO    Booting up critical service: Authorization
1598843502      INFO    System booted successfully
1598853502      INFO    User admin requested access for userlist
1598863888      ERROR   User anonymous access attempt
1598863891      INFO    System health check status: passed
1598863901      ERROR   Requested resource not found
1598864411      INFO    User admin logged out
```

### Viewing files
`cat` prints the entire file to screen. If you accidentally cat a large file, use `control + c` to kill the command.

`head -n <number> file_name` displays the first `<number>` lines of the file.

`tail -n <number> file_name` displays the last `<number>` lines of the file.

To get a specific range of lines, for example, lines 20-25, use `sed -n '20,25p' file_name` 

### How many lines?
`wc` (word count) is useful for counting characters/words/lines. You can use it as follows:
```
wc -c log.txt
wc -l log.txt
wc -w log.txt
```
**_Question:_** Can you figure out what each of these options do? 

<details>
<summary>Answer</summary> 

```
wc -c log.txt    # Counts how many characters are in the file
wc -l log.txt    # Counts how many lines are in the file
wc -w log.txt    # Counts how many words are in the file
```

</details>

___

## Redirecting output
By default, the normal output of a command prints to the screen, for example, the output from when you run `ls`

This is known as standard out (`stdout`). If you want to save stdout to a file, redirect it by adding `>` to the end of the command. You can also append data, i.e., add new data to the end of an exisiting file by using `>>`.

**_Task:_** Redirect a command output.
1. In your "examples" folder run `ls`
2. Then run `ls > output.txt`. Nothing will print to the screen but a new file should appear named `output.txt`
3. Does the output of `ls` match the contents of `output.txt`? (Yes)

___

### Find text with `grep`
`grep` searches every line in the file for words that you provide (the pattern). If a line has a matching pattern, grep will print that line; grep only searches for text and cannot make edits.

The grep format is `grep [OPTIONS] <search word> <file to search>`
* Search patterns are case-sensitive. 

<br>

**_Task:_** We want to know how many ERROR events are in log.txt. We can:
1. Print every line with the word "ERROR" `grep ERROR log.txt`
2. Count the number of lines with the word "ERROR" `grep -c ERROR log.txt`

<br>

It is common for search terms to be in quotes so that it is clear what the search pattern is, e.g., `grep "ERROR" log.txt`. 

If the search term has whitespace (e.g., `thank you`), put the pattern in quotation marks (`grep "thank you"`) so that grep knows you're looking for one pattern, not two. 

The characters `\^$.|?*+()[]{}` have special functions. If you want to include them literally (as plain text), they must be "escaped" with `\`. For example, if I wanted to search for the pattern `$variable`, `grep $variable` would not work but `grep \$variable` does. Look up regular expressions (regex) for a lot more information.


### Find and replace text with `sed`
`sed` can edit text within a file. One common use of sed is find and replace.

To replace all occurrences of a word, the sed find and replace format is <br>
`sed 's/<search word>/<replace word>/g' file` <br>
* `s` means "search"
* `g` means "global", where all occurances of the pattern are replaced. If you leave out the `g`, then only the first occurance of the pattern will be replaced.
* Just like grep, in sed the search patterns are case-sensitive.
* The sed command `'s/<search word>/<replace word>/g'` must be in quotes.

<br>

**_Task:_** Replace all occurrences of the word "ERROR" with the word "check" (do not redirect the output, just print to screen).


### `awk`: it's complicated
`awk` is an entire programming language that can do the same tasks as grep and sed but also a lot more. 

One common reason to use `awk` is to access data in columns. Columns are separated with whitespace and are numbered from $1 to $n.

**_Task:_** Print column 2 of log.txt by running `awk '{print $2}' log.txt`

___

## For loops
For loops are useful when you need to run the same command(s) on multiple files.

In Bash, this is the loop format:

```for item in [LIST]; do [COMMANDS] $item; done```

* "item" is a variable that changes meaning as the for loop works through each item in the list.
* The variable can be any word, not just "item"; it is very common for people to use "f" or "i".

### For loop from a list
**_Task:_** In log.txt, replace all occurrences of the words "ERROR" and "INFO" with the word "okay" by running `for item in ERROR INFO; do sed "s/$item/okay/g" log.txt; done`
* The words in the list "ERROR INFO" are separated by _spaces_, not commas.
* Because of the variable within the sed command, `s/$item/okay/g` is in double quotes instead of single quotes.

The output should look like this:
```
examples $ for item in ERROR INFO; do sed "s/$item/okay/g" log.txt; done
Timestamp       Category        Message
1598843202      INFO    Booting up system
1598843402      INFO    Booting up critical service: Authorization
1598843502      INFO    System booted successfully
1598853502      INFO    User admin requested access for userlist
1598863888      okay   User anonymous access attempt
1598863891      INFO    System health check status: passed
1598863901      okay   Requested resource not found
1598864411      INFO    User admin logged out
Timestamp       Category        Message
1598843202      okay    Booting up system
1598843402      okay    Booting up critical service: Authorization
1598843502      okay    System booted successfully
1598853502      okay    User admin requested access for userlist
1598863888      ERROR   User anonymous access attempt
1598863891      okay    System health check status: passed
1598863901      ERROR   Requested resource not found
1598864411      okay    User admin logged out
```

**_Question:_** In the output, why is the data displayed twice? 
<details>
<summary>Answer</summary>

Each iteration of the data represents the for loop working on each item in the list. Our list had two items, so we get two data outputs. 

</details>

<br>

**_Question:_** In the output, why does the data NOT look like this even though our for loop says to replace "INFO" and "ERROR" with "okay"?
 ```
Timestamp       Category        Message
1598843202      okay    Booting up system
1598843402      okay    Booting up critical service: Authorization
1598843502      okay    System booted successfully
1598853502      okay    User admin requested access for userlist
1598863888      okay   User anonymous access attempt
1598863891      okay    System health check status: passed
1598863901      okay   Requested resource not found
1598864411      okay    User admin logged out
 ```
<details>
<summary>Answer</summary>

We did not tell the loop to save the output and overwrite log.txt (the original input file), so the second loop iteration doesn't know that in the first iteration "ERROR" was replaced with "okay".

To generate the above output, I would use the `-i` flag, which tells sed to edit the input file "in place" `for item in ERROR INFO; do sed -i "s/$item/okay/g" log.txt; done`

</details>

<br>

**_Task:_** Now let's redirect the output and save the changes. 
1. Rerun the for loop, but this time redirect the output `for item in ERROR INFO; do sed "s/$item/okay/g" log.txt > log_edited1.txt; done`
2. Look at the output file `cat log_edited1.txt`

The output should look like this:
```
Timestamp       Category        Message
1598843202      okay    Booting up system
1598843402      okay    Booting up critical service: Authorization
1598843502      okay    System booted successfully
1598853502      okay    User admin requested access for userlist
1598863888      ERROR   User anonymous access attempt
1598863891      okay    System health check status: passed
1598863901      ERROR   Requested resource not found
1598864411      okay    User admin logged out
```

**_Question:_** Why is the data NOT displayed twice when the output is redirected to log_edited1.txt?

<details>
<summary>Answer</summary>

The for loop says that for each iteration of the loop, redirect the output to log_edited1.txt. Because the name of the output file is always the same, this means log_edited1.txt is _overwritten_ for each item in the list. What the file contains at the end of the for loop is the results of the last iteration.
<br>
<br>
Note that `>` redirects output to a new file while `>>` appends output to an existing file. If we had used `>>` then we would see the output of both loop iterations. Try it! `for item in ERROR INFO; do sed "s/$item/okay/g" log.txt >> log_edited1_appended.txt; done`

</details>

<br>

### For loop from a list in a file
[LIST] can also be a file where each line is a different item. The for loop reads each line one at a time and runs the same commands on every item in the list.

**_Task:_** In log.txt, replace the words "INFO" and "ERROR" with the word "okay", but this time "INFO" and "ERROR" will be read from a file.
1. In your "examples" directory make a new text file `nano search.txt`
2. List "ERROR" and "INFO" in a column like this:
    ```
    ERROR
    INFO
    ```
3. Save the file and exit nano `control+x`
4. Then run: 
    ```
    for item in `cat search.txt`; do sed "s/$item/okay/g" log.txt; done
    ```
    Pay attention to `` `cat search.txt` ``
    * The backtick character `` ` `` lets us call a function within another command. 
    * The backtick `` ` `` is _not_ the same as the single quote `'`. On a English keyboard, the backtick is usually to the left of the "1" key while the single quote is over on the right side of the keyboard next to the "enter" key; make sure not to mix these up!
5. The output should look the same as when you ran `for item in ERROR INFO; do sed "s/$item/okay/g" log.txt; done`. The only difference is in where the list information came from.

<br>

For the genotyping pipeline we will do many of this type of for loop.

**_Question:_** The command `echo` prints to the screen. A pipe `|` takes the output of the function on the left and passes it to the function on the right. What does this for loop do?

``for f in `cat search.txt`; do echo -n $f | wc -c; done``

<details>
<summary>Answer</summary>

It gets the number of characters for each item in the search.txt list.
<br><br>
Each item in search.txt is printed to the screen, but because we're using a pipe, the output is passed to the  `wc -c`  command which counts the number of characters.
<br><br>
In case you're wondering, the `-n` tells `echo` to not add a newline character to the end of the text, otherwise `wc` will also count that newline character and report an extra character.

</details>

___

## The bare minimum to remember
* Paths
* Command-line commands
* Nano text editor
* For loops
