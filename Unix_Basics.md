# Unix Cheat Sheet

**[Unix Cheat Sheet](https://files.fosswire.com/2007/08/fwunixref.pdf)**

# Command line activity
We're going to be performing today's activitiy using the linux cluster on campus called Colossus.  This a cluster of computers that you are able to log on to remotely.

## Log on to colossus using terminal or putty
If you are using a PC you need to download the SSH client [putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).  This will allow you to connect to Colossus and run commands on colossus.

If you are using Mac you can connet using the terminal application that is already installed.

[MTU Remote Login Instructions](https://servicedesk.mtu.edu/TDClient/1801/Portal/KB/ArticleDet?ID=53584) - Note that these instructions are for a differnet cluster.

### Connecting using Putty (Windows)
1. Open an ssh client, such as PuTTY.  
2. Enter colossus.it.mtu.edu for the hostname.  
3. Select the Open button.  
4. Accept the host key if prompted.  
5. Enter your Michigan Tech account name at the login as: prompt.  
6. Enter your Michigan Tech password at the accountname@guardian.it.mtu.edu's password: prompt.  (Note the cursor will not move when typing in your password).

### Connecting using Terminal (Mac)
1. Open a terminal. Enter the following command, using your Michigan Tech account name: `ssh accountname@colossus.it.mtu.edu`
2. Accept the host key if prompted. 
3. Enter your Michigan Tech password.
4. Hit the Enter key.

## Log on to Filezilla
We will also want to get an FTP client to easily add files to and remove files from colossus.

[FileZilla](https://filezilla-project.org/) is a great FTP client.  If you're on a school computer you can use WinSCP from the AppsAnywhere page.

Hostname: `colossus.it.mtu.edu`  
Username: Your MTU ID
Password: Your MTU passowrd
Port: 22
Then click Quickconnect

## Pracice Unix activity

### Determine your username

The `whoami` command will print your username to the screen

```{BASH}
whoami
```
### Determine your current working directory (Path)

The `pwd` command will print your working directory as an absolute path

```{BASH}
pwd
```
### Determine what files and directories are your working directory

The `ls` command will list the files that are in your working directory

```{BASH}
ls
```
### Make a new directory

The `mkdir` command will make a new directory

```{BASH}
mkdir BL2700
```
### Change directoriees

The `cd` command will change directories

```{BASH}
cd BL2700
```

# Unix Filters Activity

Unix filters are programs that are in the unix environment that allow you to interact with the computer by navigating through the directory structure, creating files and directories, or processing data.  These filters often will take a text input and perform some function on that text input to print a text ouput to the terminal window.

## Basic commands for navigating around the shell

### Download file 
Download a fasta file using the `wget` command.

The `wget` command will get a file from the web.

```{BASH}
wget https://raw.githubusercontent.com/stechtmann/BL2700/master/data/Loki_protein.fasta
```

### Look at the first few lines of this file.

The `head` command will look at the first few lines of a file.  You can add the `-n` option to look at a specific number of lines

```{BASH}
head Loki_protein.fasta
```

**Activity: What is the command to look at the first 50 lines of a file**


### Look at the last few lines of this file.

The `tail` command is like the `head` command, except it looks at the last few lines of the file

```{BASH}
tail Loki_protein.fasta
```

## Unix filters for processing files

### Pattern matching and serching of files using the `grep` command

Pattern matching is an important process in handling data on the unix shell and is a very powerful command

The 'grep` command allows for pattern matching of regular expressions.  The name means "globally search for a regular expression and print matching lines"

You can use the grep command to match a pattern and print all of the lines that match that pattern to the terminal window.

If we want to print all of the lines that include sequence names from a fasta file we can search for the character that indicates a new sequence name `>`

#### Search for sequence names

```{BASH}
grep ">" Loki_protein.fasta
```

#### Count the number of sequences in this fasta file.

The `-c` option allows you to count the lines rather than print them to the screen.  To count the number of sequences in a fasta file, we can count the number `>` occurances in our file.

```{BASH}
grep -c ">" Loki_protein.fasta
```

**Activity: how would you list all of the names of hypothetical proteins in this file?**
**Activity: Count all of the proteins that are hypothetical proteins.**

#### Extracting the sequence along with the name from a fasta file

The `-A 1` option will allow you to print the line that matches the pattern and also print the next line which includes the sequence.

```{BASH}
grep -A 1 "hypothetical protein" Loki_protein.fasta
```

#### Create a file with all of the names of proteins with tranferase in the name

You can send the output from a unix filter to a file using `>`.  Note this is a different use than how the same symbol is used in fasta file.  Inside of the fasta file the `>` indicates a new sequence.  In the unix shell that `>` indicates that you want to create a file and fill it with what is printed to the screen by the unix filter.

```{BASH}
grep "transferase" Loki_protein.fasta > T_names.txt
```

## Processing data using the `cut` command

The `cut` command allows you split lines based a delimeter. Delimeters are symbols that are used as a break between strings.  The `cut` command will allow you to then cut out particular fields from the split string.

#### Remove the `>` and gene IDs from the names of the transferase genes and save as a new file.

```{BASH}
cut -d ' ' -f 2- T_names.txt > T_only_names.txt
```

In this example the delimters is a space.  This is indiated by the `-d` option.  
The fields to be kept in this example are all of the fields following field 2.  This is specified by the `-f` option.


## Use `uniq` to determine if there are any duplicated genes in the same transferase group.

The `uniq` command will dereplicate lines.  If there are two consecutive lines that match, the `uniq` command will merge those into a single line.  The output of the `uniq` command is all of the uniq entries.  The big thing to note with `uniq` is that it will only match **consecutive** entries.

```{BASH}
uniq T_only_names.txt
```

The `-c` option will count how many matching consecutive entries that you have.

## Sort the names of the transferases to be in order

The `sort` command will reorder the lines.

```{BASH}
sort T_only_names.txt
```
By default `sort` will order alphabetically. The `-n` option will sort numerically


## Use a pipe to connect the sort and uniq commands to determine if there are duplicate names
```{BASH}
sort T_only_names.txt | uniq -c
```
**How could you make this easier to identify which groups have more than one gene?**


**Activity: Use a series of pipes (`|`) to determine which groups of dehydrogenases have more than one gene.**



