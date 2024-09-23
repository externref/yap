---
title: Linux How-TOs | A must know list of commands in Linux shells
description: Here's a list of useful tools and commands that are required often for using Linux as a daily driver or even only for development.
date: 2024-09-23
authors: 
  - externref
categories:
  - linux
  - shell
  - cli
comments: true
---

# Linux How-TOs ❓ | A must know list of commands in Linux shells

Hello Everyone 👋.

Here's a list of useful tools and commands that are required often for using Linux as a daily driver or even only for development.
<!-- more -->
This article will cover commands from bash, [zsh](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH#zsh), and [fish](https://github.com/fish-shell/fish-shell) since they are mostly compatible.

### Topics covered in this article
**Concepts**: `Environment variables`, `Aliasing`, `Path`
**Built in commands**: `set(fish)`, `cd`, `pwd`, `ls`, `mkdir`, `touch`, `cat`, `echo`, `rm`, `cp`, `mv`, `grep`, `wget`, `tar`
**Installable commands**: `nano`, `mousepad`

### Basics
Before starting with commands here are some core concepts you should be aware of:
* `~` in a path represents the home directory (eg: `/home/username/` )
* `/` represents the root directory 
* Values starting with a `$` denote an environment variable. For example, `$HOME` returns the path to your home directory. If the environment variable is not found an empty string is returned.

```sh
sarthak@sarthak ~> echo $HOME
/home/sarthak
sarthak@sarthak ~> echo $THIS_ENV_DONT_EXIST

sarthak@sarthak ~>  
```

* There might be some differences between syntax patterns in different shells, in that case, alternatives will be used.

These are basic commands you should know to use the terminal efficiently.

### Environment Variables
Environment variables are used to store values for a process into the system, there are several Environment variables like `PATH`, `PWD`, `USER` etc set up by the processes on the system. Here are some operations you can perform on them.
```shell
# reading a env variable
echo $HOME # prefixing the name with $
ENV_NAME="VALUE" # setting value for a session
# Example
sarthak@sarthak:~$ VAR2="sarthak in bash" # using bash
sarthak@sarthak:~$ echo $VAR1 $VAR2
sarthak in bash
sarthak@sarthak ~> set VAR1 "sarthak in fish" # using fish
sarthak@sarthak ~> echo $VAR1
sarthak in fish
```

---

### Creating aliases
You can create aliases for long commands using `alias` command. These are useful when you need to repeat same task multiple times.
```shell
alias [ALIAS] [COMMAND]
```
**Example**

![grep example](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/32jehnmqvfrfejs324ld.png)

---

### The PATH variable
The path variable stores directory addresses where executable files are stored. These are stored in a single string seperated by a colons. You can access the path using the `PATH` variable in the shell.
**Adding a path temporarily**
To add a path temporarily you can use the `export` command in the active shell 
```shell
export PATH=$PATH:/path/to/directory # for bash and zsh
fish_add_path /path/to/directory # for fish
```
This will add the PATH to the path variable for current shell session.
**Adding a path permanently**
You need to edit your RC file which should be named after your shell ( `.bashrc`, `.zshrc`) to execute the commands from above. For fish shell you need to add the set command in `~/.config/fish/config.fish`

---

### Navigation
* **cd**: The `cd` command is used to move from one directory to another. The syntax for the command is 
```shell
cd [DIRECTORY] # where directory is the path of the directory to navigate to 
cd .. # to navigate to the parent directory of your current
# you can also navigate to other dir in the parent dir
cd ../other_directory_in_parent_directory 
cd ~ # for the home directory
cd / # for the root directory

# exclusive to fish, you can type in the directory path directly
directory/
```
* **pwd**:  **P**rint **W**orking **D**irectory prints the path to the current working directory.
```shell
# example
sarthak@sarthak ~/p/codeforces (main)> pwd
/home/sarthak/projects/codeforces
```
* **ls**: This is used to list the contents of a directory. Command syntax:
```shell
ls [DIR_NAME=pwd] # current directory is listed by default
ls -R # shows files and folders inside the dir recusively
# you don't want to use the -R tag most of the times.
```
---

### Working with directories and files
* **mkdir**: The `mkdir` command is used to `m`a`k`e `dir`ectories in the current working directory. Command syntax: 
```shell
mkdir [DIRECTORY_NAME] # creates a directory with the name provided
# if the directory is a subdirectory and the parent dir does not exist
mkdir -p [DIRECTORY_PATH] 
# example
mkdir hello/world # will error since dir hello does not exist
mkdir -p hello/world # will create hello dir with world dir inside it
```
* **touch**: This command is actually meant to modify edit and create time of files, but can also be used to create them. Command syntax:
```shell
touch hello.txt # creates a new file called hello.txt
```
* **cat**: This command is used to con`cat`inate the contents of file(s) and display them. Command Syntax:
```shell
cat [FILE(s)]
# example
cat file1.txt file2.txt
```
* **echo**: The echo command is mainly used to display text in the stdout but can also be used to write in a file. Command syntax: 
```shell
echo "text to display in shell"
echo "text to display in file" > filename
# Example
sarthak@sarthak ~> echo "import hello" > main.py
sarthak@sarthak ~> cat main.py
import hello
```
* **rm**: The `rm` command is used to `r`e`m`ove files or dirs specified in the argument. Some useful flags for this command are:
     * `r`: recursively remove items from the directory
     * `f`: ignore files that do not exist without any prompts

    Command Syntax: 
    ```shell
    rm <flags> [TARGET] 
    # Example
    rm hello.txt # will remove the hello.txt file 
    rm -rf src/ 
    # will remove all the files and dirs inside the src dir and delete it
    ```

* **cp**: This command is used to create a `c`o`p`y of a file. Command syntax: 
```shell
cp [SOURCE_FILE] [TARGET_FILE] # target file created if doesn't exist
# Example
sarthak@sarthak ~> cat hello.py
import hello
sarthak@sarthak ~> cp hello.py test.py
sarthak@sarthak ~> cat test.py
import hello
```
* **mv**: The `m`o`v`e command is used to move the file from one location to another.  It can also be used to rename the file.
```shell
mv [FILE] [TARGET] # target can be a directory or a file name
# Example
sarthak@sarthak ~> mkdir test_dir
# moving to a new directory
sarthak@sarthak ~> mv hello.py test_dir/
sarthak@sarthak ~> cat test_dir/hello.py
import hello
# renaming the file
sarthak@sarthak ~> mv test_dir/hello.py test_dir/renamed_file.py
sarthak@sarthak ~> cat test_dir/renamed_file.py
import hello
```
* **grep**: The grep command is used to find for text in a file. Command syntax 
```shell
grep <tags> [STR_TO_SEARCH_FOR] [FILENAME]
# Example
sarthak@sarthak ~ [1]> grep "py" space/src/lib.rs
use pyo3::prelude::*;
#[pyfunction]
#[pymodule(name="space")]
    m.add_function(wrap_pyfunction!(test_function, m)?)?;
# For better output use the --color and -n tags
# it will highlight the output and add line numbers to the matches
```
![grep example](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/j9kqxa7dgelv3tj6l3ur.png)

* **wget**: This utility is used to download files to the local system.
Command Syntax:
```shell
wget <-c> [URL] # the c tag is used to establish a continous download
# Example
sarthak@sarthak ~> wget -c https://bootstrap.pypa.io/get-pip.py
--2024-08-24 08:01:29--  https://bootstrap.pypa.io/get-pip.py
Resolving bootstrap.pypa.io (bootstrap.pypa.io)... 151.101.156.175, 2a04:4e42:25::175
Connecting to bootstrap.pypa.io (bootstrap.pypa.io)|151.101.156.175|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2266755 (2.2M) [text/x-python]
Saving to: ‘get-pip.py’

get-pip.py                    100%[=================================================>]   2.16M  10.3MB/s    in 0.2s

2024-08-24 08:01:30 (10.3 MB/s) - ‘get-pip.py’ saved [2266755/2266755]
```
---

### Text Editing
Text editing tools like `nano` and `vim` come pre installed on most linux distributions. You can use `nano` for basic text editing inside the terminal. 
**Usage**: `nano [FILENAME]`
Alternatively you can use `mousepad` for GUI based editing.
**Usage**: `mousepad [FILENAME]`

![nano usage](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/abjgg2wj93yp2k3i0h57.gif)

---

### Working with Archives
The `tar` command is used to work with `tar.gz` format which is mostly used in linux OS(s).
**Usage**: `tar [options] [archive-file]`
You can use this command to unarchive files.
```shell
tar -cvzf [archive].tar.gz [FOLDER NAME]
```
---

These are the commands I use for development using Ubuntu(WSL) and Arch(Bare Metal) as a daily driver. Fell free to comment for improvements and other relevant useful information that can be added 😄.
