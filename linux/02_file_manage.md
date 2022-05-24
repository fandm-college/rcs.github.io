# File organization and management

**UNDER CONSTRUCTION**

Understanding how files are organized in Linux is perhaps the biggest obstacle when it
comes to gaining proficiency and confidence working with the command line.  This tutorial will introduce
some basic concepts related to file organization as well as some important Linux commands
related to file management to hopefully demistify...

## Basic directory structure

- Absolute path
- Relative path
- Parent directory
- subdirectory
- home directory

`pwd`

## Navigating directories

The command to move from one directory to another is the `cd` command.  With no arguments this command
will change you to your home directory.

```bash
user@mycomputer:~/snap$ pwd
/home/user/snap
user@mycomputer:~/snap$ cd
user@mycomputer:~$ pwd
/home/user
```

The same result is also accomplished with the following `cd ~` (Recall that ~ is short hand for the home directory)
More often you will provide the path to some directory as an argument to the `cd` command.  That path
may either be an absolute or relative path.  For example:

```bash
user@mycomputer:~$ pwd
/home/user
user@mycomputer:~$ cd /data/git_repos
user@mycomputer:/data/git_repos$ pwd
/data/git_repos
user@mycomputer:/data/git_repos$ cd mycode_repo
user@mycomputer:/data/git_repos/mycode_repo$ pwd
/data/git_repos/mycode_repo
```

There are a few additional shortcuts that are very helpful when it comes to moving around directories from the command line.
Example uses are given below.  Please not that we use the `pwd` only to make it explicit which directory we are in at any given time.

1. `cd ..` changes directory to the parent directory of the current directory
2. `cd -` changes back to the directory you were just in.  This is useful if you need to switch back and forth
   between two directories whose absolute paths are very different from each other.
3. Use tab to auto-complete a path.  if you begin to type a path (absolute or relative) you can use tab to try to auto-complete it.
    - If there is more than one possible completetion, then pressing tab a second time will list all possible directory paths that
      may complete what you have typed so far.  You can then type additonal characters to complete the path.

```bash
user@mycomputer:/data/git_repos/mycode_repo$ pwd
/data/git_repos/mycode_repo
user@mycomputer:/data/git_repos/mycode_repo$ cd ..
user@mycomputer:/data/git_repos$ pwd
/data/git_repos
user@mycomputer:/data/git_repos$ cd
user@mycomputer:~$ pwd
/home/user
user@mycomputer:~$ cd -
user@mycomputer:/data/git_repos$ pwd
/data/git_repos
user@mycomputer:/data/git_repos$ cd -
user@mycomputer:~$ pwd
/home/user
user@mycomputer:~$ cd Dow <Tab>
user@mycomputer:~/Downloads$ pwd
/home/user/Downloads
user@mycomputer:~/Downloads$ cd
user@mycomputer:~$ cd Do <Tab><Tab>
Documents/ Downloads/
user@mycomputer:~$ cd Do
```

## Listing directory contents

The command to see the contents of a directory is `ls`.  By default it will list the contents of the directory
you are currently in.  You can see the contents of other directories without navigating to them first if you 
add either an  absolute or relative path to a directory after `ls` .  For example:

```bash
user@mycomputer:~$ pwd
/home/user
user@mycomputer:~$ ls
Desktop         Music       snap        workflow.txt
Documents       Pictures    sql
Downloads       Public      Templates
user@mycomputer:~$ ls \data\git_repos
conky       mycode_repo
dept.txt    website
user@mycomputer:~$ ls Documents
resume.docx
user@mycomputer:~$ 

In addition, there are a few options you can supply to the `ls` command which will output additional useful information.

- `-l`
- `-a`
- `-h`
- `-S`
- `-R` lists files in current directory as well as files in all subdirectories, organized by subdirectory

## Moving and Copying files
