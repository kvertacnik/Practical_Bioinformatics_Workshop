# Introduction to command line

## Getting started
We're going to use the "Bash" version of the command line.

Linux: Open the program “Terminal” <br>
macOS: Open the program “Terminal” and run `exec bash` <br>
Windows: Install and open "Ubuntu" following the "Install WSL command" section [here](https://learn.microsoft.com/en-us/windows/wsl/install#install-wsl-command) 

___

## Directory navigation
![Directory Structure](assets/command_line/linux_directory_structure.png)

Computer files are organized in a series of nested, hierarchical directories (folders) starting with the root directory. Each user has their own home directory where their files are stored. To distinguish files from directories, directory names end in `/` while file names do not.

NB: "Folder" and "directory" are synonymous terms that are used interchangeably.

### Paths
Each directory and file has a "path". Think of it as an address within the hierarchical structure. Paths start at root and go down each level until it reaches the directory or file. For example, the path to user1's desktop directory is `/home/user1/Desktop/`

For directory paths, the last `/` can be omitted, so the path to user1's desktop directory can also be written as `/home/user1/Desktop`

The path to your home directory can be abbreviated as `~`, so the path to user1's desktop directory can also be written as `~/Desktop`

Instead of typing the full name, **paths and files can be auto-filled using the tab key** (tab-completion). For example, typing `~/Des` `tab` will automatically complete with `~/Desktop`

**_Question:_** Based on the above figure, why would `~/D` `tab` fail to tab-complete?

NB: Command line has a hard time understanding whitespace in file or directory names. For example, is `my file.txt` one file or two files named "my" and "file.txt"? **Best practice is to never have whitespace in names.** "my file.text" should be "my_file.text", "my-file.txt", "myFile.txt", etc. Also, do not include any symbols in file names except for `-` or `_` or `.`

___

## Basic commands

`pwd` = "print working directory". It answers the question "Where am I?" by showing the path to the current directory. `tree` displays a visualization of pwd.

`ls` = "list". It shows the contents of the current directory. To see information like file size or last access date use `ls -lh`

`cd` = "change directory", with the usage `cd /path/to/directory`. To move up one level use `cd ..`

`mkdir` =  "make directory", with the usage `mkdir new_folder_name`. It makes a new directory within the current directory.

`cp` = "copy", with the usage `cp file_name copy_file_name`

`mv` = "move", with the usage `mv file_name /path/to/directory`. If a file with the same name already exists, `mv` will overwrite the original file without asking permission. To rename a file, use `mv file_name new_file_name`

`rm` = "remove", with the usage `rm file_name`. Note that command line does not have an "undo" function so **_deletions are permanent_**.

`cat` = "concatentate". It combines multiple files `cat file1 file2 > file3` and can print the contents of a file to the screen `cat file_name`

`head -n <number> file_name` displays the first `<number>` lines of the file

`tail -n <number> file_name` displays the last `<number>` lines of the file

For directories, add `-r` to `cp`, `mv`, and `rm`. For example, use `rm -r folder_name` to delete a folder.

**_Task:_** Go to your desktop directory. Make a folder named "examples". (Do not include " " in the folder name.)

NB: If the command line gets stuck or you want to cancel a running command use `control + c`

NB: Command line saves a history of executed commands. Use the up arrow to go through your most recent commands; this is useful when you need to rerun the same command.

___

## Make and edit a new text file
Most bioinformatics analysis programs cannot read files that are written in familiar programs such as Word and Excel. 

What happens when we try to look at an Excel file?
![Excel example](assets/command_line/head_excel.png)

Instead, we will write files in plain text. Multiple text editors are built into the command line. We will use `nano`.

**_Task:_** Go into your ~/Desktop/examples directory. <br>
1. Open the text editor by running `nano` or `nano hello.txt` <br>
2. Type `Hello World!`<br>
3. Exit nano `control+x`. It will ask you to "Save modified buffer". Type "Y" and use the file name "hello.txt". <br>

We can also make a new, empty file from the command line using `touch new_file_name`

Did you catch the three ways to make a new file?
* `touch new_file_name`
* `nano`
* `nano new_file_name`

NB: File extensions are important for understanding file contents. For example, a file named "sample1" could be anything, e.g., a text file with metadata or a fastq file with sequence reads. Naming the file sample1.txt or sample1.fq avoids this problem.

___

## Editing and viewing files from the command line
Files can be edited without opening a text editor. These command line tools have many options; we'll look at the basics.

NB: By default, the output of these commands prints to the screen. If you want to save the output to a file, redirect the output by adding `> new_file_name` to the end of the command.

**_Task:_** Redirect a command output
1. In your "examples" folder try `ls`
2. Then try `ls > output.txt` A new file should appear named "output.txt"
3. Does the output of `ls` match the contents of `output.txt`?

Now we will create a data file and use command line tools to extract information from the file **without opening the file**.

**_Task:_** Create a data file.
1. In your "examples" directory make a new text file named "log.txt".
2. Copy and paste the data below (use right-click to paste the text into nano).
3. Save the file and exit nano.
```
Timestamp       Category        Message
1598843202      INFO    Booting up system
1598843402      INFO    Booting up critical service: Authorization
1598843502      INFO    System booted successfully
1598853502      INFO    User admin requested access for userlist
1598863888      ERROR   User anonymous attempt to access protected resource without credentials
1598863891      INFO    System health check status: passed
1598863901      ERROR   Requested resource not found
1598864411      INFO    User admin logged out
```

### Find text with `grep`
`grep` searches every line in the file for word(s) that you provide. If a line has matching text, grep will print that line to the screen; grep only searches for text and cannot make edits.

The grep format is `grep [OPTIONS] "search_word" file_to_search` <br>
The search_word must be in quotes and is case-sensitive.

**_Task:_** We want to know how many "ERROR" events are in log.txt. We can:
1. Print every line with the word "ERROR" `grep "ERROR" log.txt` <br>
2. Count the number of lines with the word "ERROR" `grep -c "ERROR" log_file.txt` <br>

### Find and replace text with `sed`
`sed` can edit text within a file. One common use of sed is find and replace.

The sed find and replace format is `sed 's/search_word/replace_word/g' file` <br>
Unlike grep, search_word is not in quotes but it is still case-sensitive. However, the command `'s/search_word/replace_word/g'` must be in quotes.

**_Task:_** Replace all occurrences of the word "ERROR" with the word "check".

### `awk`: it's complicated
`awk` is an entire programming language that can do the same tasks as grep and sed but also a lot more. 

One common reason to use `awk` is to access data in columns. Columns are numbered from $1 to $n.

**_Task:_** Print column 2 of log.txt by running `awk '{print $2}' log.txt`

### Viewing files
`cat` prints the entire file to screen

`head -n <number> file_name` displays the first `<number>` lines of the file

`tail -n <number> file_name` displays the last `<number>` lines of the file

**_Task:_** Get the first two lines of log.txt.
___

## For loops
For loops are useful when you need to run the same command(s) on multiple files.

In Bash, the for loop format is:

```for item in [LIST]; do [COMMANDS] $item; done```

**_Task:_** In log.txt, replace the words "INFO" and "ERROR" with the word "okay" by running 
```
for item in ERROR INFO; do sed "s/${item}/okay/g" log.txt; done
```

A few things to note:
* The words in the list "ERROR INFO" are separated by spaces, not commas.
* "item" is a variable that changes meaning from "ERROR" to "INFO". Because of this special property, in the "do" part of the loop "item" is written as `${item}`
* Because of the `${item}` variable, `s/${item}/okay/g` is in double quotes instead of single quotes <br>

[LIST] can also be a file. In this scenario, each line in the file is a different item. The for loop processes each line until it reaches the end of the file

**_Task:_**  In log.txt, replace the words "INFO" and "ERROR" with the word "okay", but this time "INFO" and "ERROR" will be read from a file. <br>
1. Make a new text file named "search.txt" and list "INFO" and "ERROR" in a column like this:
```
ERROR
INFO
```
2. Save and close the text file. Then run:
```
while read item; do sed "s/${item}/okay/g" log.txt; done < search.txt
```

A couple things to note:
* Since we are reading our search terms from a file, `for item in [LIST]` is replaced by `while read item`
* To input our search file, `< search.txt` is added to the end of the loop. This is the opposite of using `>` to redirect output.