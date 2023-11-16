# Introduction to command line

## Getting started
We're going to use the "Bash" version of the command line.

Linux: Open the program “Terminal” <br>
macOS: Open the program “Terminal” and run `exec bash` <br>
Windows: Install and open "Ubuntu" [steps](https://learn.microsoft.com/en-us/windows/wsl/install#install-wsl-command)

___

## Directory navigation
![Directory Structure](assets/linux_directory_structure.png)

Computer files are organized in a series of nested, hierarchical directories (folders) starting with the root directory. Each user has their own home directory where their files are stored. To distinguish files from directories, directory names end in `/` while file names do not.

### Paths
Each directory and file has a "path". Think of it as an address within the hierarchical structure. Paths start at root and go down each level until it reaches the directory or file. For example, the path to user1's desktop directory is `/home/usr1/Desktop/`

When typing out a path to a directory, the last `/` can be left out, so the path to user1's desktop directory can also be written as `/home/usr1/Desktop`

The path to your home directory can be abbreviated as `~`  so the path to user1's desktop directory can also be written as `~/Desktop`

Instead of typing the full name of each directory, paths can be auto-filled using the tab key. For example, typing `~/Des` and then pressing tab will print `~/Desktop` <br>
Question: based on the above figure, why would `~/D` `tab` fail to auto-fill?

NB: Command line has a hard time understanding whitespace in file or directory names. For example, is `my file.txt` one file or two files named "my" and "file.txt"? Best practice is to never have whitespace in a file or directory name. "my file.text" should be "my_file.text", "my-file.txt", "myFile.txt", etc. Also, do not include any symbols in file or directory names except for "-" or "_".

### Basic commands

`pwd` = "print working directory". It lists the path to the current folder, answering the question "Where am I?" `tree` provides a visualization of pwd.

`ls` = "list". It lists the contents of the current directory. `ls -a` lists all contents including hidden files and `ls -lh` lists contents with additional information. 

`cd` = "change directory", with the usage `cd /path/to/new/directory`. To move up one directory use `cd ..`. To return to your home directory use simply `cd`

`mkdir` =  "make directory", with the usage `mkdir new_folder_name`. It makes a new directory within the current directory.

`cp` = "copy", with the usage `cp file_name copy_file_name`

`mv` = "move", with the usage `mv file_name /new/directory/path`. If the new directory already has a file with the same name, `mv` will overwrite the original file. To rename a file, use `mv file_name new_file_name`

`rm` = "remove", with the usage `rm file_name`. Note that command line does not have an "undo" function so deletions are permanent.

For directories, add `-r` to `cp`, `mv`, and `rm`. For example, use `rm -r folder_name` to delete a folder.

Command line saves a history of what you ran. Use the up arrow to go through your most recent commands; this is useful when you need to rerun the same commands.

Task: Go to your home folder. Make a folder for this course. Go into the course folder and make a folder named "examples".

___

## Make and edit a new text file
To make a new file from the command line, use `touch new_file_name`.

Multiple text editors are built into command line. We will use `nano`.

Task: Go to the "examples" directory you made previously.<br>
Open the text editor by running `nano` or `nano hello.txt`<br>
Type `"Hello World!`<br>
Save the file `control+o` and use the file name "hello.txt" <br>
Exit nano `control+x`

Did you catch the three ways to make a new file?
* `touch new_file_name`
* `nano`
* `nano new_file_name`

NB: File extensions are very important for understanding file contents. For example, a file named "sample1" could be anything, e.g., a text file with metadata or a fastq file with sequence reads. Naming the file sample1.txt or sample1.fq avoids this problem.

___

## Editing and viewing files from the command line
Text in files can be edited without opening a text editor. These command line tools have many options; we'll look at the basics.

NB: By default, the output of these commands prints to screen. If you want to save the output in a file, add `> new_file_name` to the end of the command. For example, `sed 's/ERROR/check/log.txt > edited_log.txt` will redirect the `sed` output to `edited_log.txt` instead of printing to screen.

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
Task: In your "examples" directory make a new file named "log.txt" in `nano`. Copy and paste the above data and save the file.

### Find text with `grep`
The format for grep commands is `grep [OPTIONS] "search_word" file_to_search` <br>
The search_word must be in quotes and are case-sensitive.

Task: We want to know how many "ERROR" events are in log.txt. We can: <br>
* Print every line with the word "ERROR" `grep "ERROR" log.txt` <br>
* Count the number of lines with the word "ERROR" `grep -c "ERROR" log_file.txt` <br>
* See the line before the line with the word "ERROR" `grep -B 1 "ERROR" log.txt`

### Find and replace text with `sed`
The format to find and replace text with `sed 's/search_word/replace_word/g' file` <br>
Unlike `grep`, the search_word is not in quotes but it is still case-sensitive. However, the `'s/search_word/replace_word/g'` must be in quotes (standard convention is single quotes).

Task: Replace all occurrences of the word "ERROR" with the word "check".

### `awk`: it's complicated
`awk` is an entire programming language that can do the same tasks as `grep` and `sed` but also a lot more. 

One common reason to use `awk` is when you want to access data in columns. Columns are numbered from $1 to $n.

Task: Print column 2 of log.txt. To do this, run `awk '{print $2}' log.txt`

### Viewing part of a file

`head -n <number> file_name` displays the first <number> lines of the file

`tail -n <number> file_name` displays the last <number> lines of the file

___

## For loops
When you need to run the same command on multiple files, for loops allow you to automate this process so that you do not need to manually process file one at a time. 

In Bash, the format of a for loop is: <br>
```
for item in [LIST]; do [COMMANDS] $item; done
```

Task: Going back to our log.txt file, let's replace the words "INFO" and "ERROR" with the word "ok".
```
for item in ERROR INFO; do sed s/${item}/okay/g log.txt; done
```
Note that the items in [LIST] are separated by spaces, not commas.