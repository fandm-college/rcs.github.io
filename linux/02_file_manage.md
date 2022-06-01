# File organization and management

**UNDER CONSTRUCTION**

Understanding how files are organized in Linux is perhaps the biggest obstacle when it
comes to gaining proficiency and confidence working with the command line.  This tutorial will introduce
some basic concepts related to file organization as well as some important Linux commands
related to file management to hopefully demistify...

## Basic directory structure

Directories (think of a directory as a folder) in Linux  are organized in a strictly hierarchical fashion known as a tree.
More appropriately this structure is what we might consider to be an upside-down tree with the base or root of the 
filesystem at the top (see picture below).  In fact the top-most level of the filesystem is a single directoy called 
the root directory.  The root directory it contains all other directories and their subdirectories and is denoted as / in Linux. 

![Linux directory structure](filesystem.jpg)

In the example above, the immediate subdirectories of root are *bin*, *boot*, *dev*, *home*, and *usr*.  These would be denoted `/bin`, `/boot`, 
`/dev`, `/home`, and `/usr`.  That is each one is specified always beginning with the root directory.  This full specification of any 
directory on the system beginning with the root directory is known as its **absolute path**.  For example the subdirectories of 
the *cheese* directory would be specified as absolute paths `/bin/cheese/american`, `/bin/cheese/gouda`, 
and `/bin/cheese/swiss` respectively. 

In general you might think of a directory's path as instructions on how you would walk to that directory.  In the case of absolute paths it
is the directions for walking to a directory always starting from root.  Please note too that just typing a path does not navigate to a directory.
There is a specific command for that discussed further down.  In this section we are discussing the idea of paths in a general way
because they are used in conjunction with many different Linux commands.  They are also often used when running scientific software to specify 
things like where output should go.

In Linux, there is a second way to specify the path of a directory known as **relative paths**. They are relative becuase 
it is based on where you currently are in the tree.  For example, if you happened to be in the `/bin` directory (more on knowing where you are in a moment), then we could refer to the *cheese* directory as the relative path `cheese`.  Notice there is no leading `/` because we are not starting from root.  Similarly (again assumming we are in the *bin* directory) the relative paths for the *cheese* subdirectories would be
`cheese/american`, `cheese/gouda`, and `cheese/swiss`.

When to use absolute vs relative paths.....

Getting to the question of how do you know where you are, that's where the `pwd` command comes into play.  PWD stands for 
print working directory and it prints out the absolute path of the directory you are currently in.  That is it prints out 
your location in the filesystem tree.  Continuing with our example from above if, if we happened to be in the *gouda* directory
then `pwd` would print out `/bin/cheese/gouda`.

Finally, when you log in you are placed in a certain directory by default.  In most cases this directory's absolute 
path is `/home/username` where *username* is your actual username.  This directory has a special name, called the **home directory**.  
This directory also has a special shorthand denoted by the tilde `~` and always refers to the individual user's home directory.  
So if auser is logged in it refers to `/home/auser` but if cnovak is logged in then it would refer to `/home/cnovak`.

### Directory permissions


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
user@mycomputer:~$ ls /data/git_repos
conky       mycode_repo
dept.txt    website
user@mycomputer:~$ ls Documents
resume.docx
user@mycomputer:~$ 
```

In addition, there are a few options you can supply to the `ls` command which will output additional useful information.

- `-l`
- `-a`
- `-h`
- `-S`
- `-R` lists files in current directory as well as files in all subdirectories, organized by subdirectory

## Moving and Copying files
