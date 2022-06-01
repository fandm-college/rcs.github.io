# File organization and management

**UNDER CONSTRUCTION**

Understanding how files are organized in Linux is perhaps the biggest obstacle when it
comes to gaining proficiency and confidence working with the command line.  This tutorial will introduce
some basic concepts related to file organization as well as some important Linux commands
related to file management to hopefully demistify...

## Basic directory structure

Directories (think of a directory as a folder) in Linux  are organized in a strictly hierarchical fashion known as a tree.
More appropriately this structure is what we might consider to be an upside-down tree with the base or root of the filesystem at the top.  
Indeed the top-most level of the filesystem is a single directoy called the root directory.  That is, it contains all other directories
and their subdirectories.  The root directory is denoted as / in Linux.

For example, a few common immediate subdirectories of root are *bin*, *boot*, *dev*, *home*, and *usr*.  These (and the other immediate 
subdirectories) are denoted `/bin`, `/boot`, `/dev`, `/home`, and `/usr`.  That is each one is specified always beginning with the root
directory.  The specification of any directory on the system is known as its **path**.  You might think of the path as a way of walking
from the root directory to the directory you need to get to.  For example, if there were a subdirectory in *bin* 
called *cheese*, then it's path would be `/bin/cheese`.  We would *walk* from the *root* directory, to the *bin* directory and finally 
to the *cheese* directory. 

Further, in Linux there are two different ways to specify path.  The first is using the **absolute** (or full) path.  The absolute
path is the path starting with root.  It is how we have specified directories so far.  The path specified above, `/bin/cheese`, 
is an example of a directory specified by its absolute path.  Importantly, you can always use the absolute path to specify a directory
regardless of what directory you happen to be in (or alternatively independent of your location in the hierarchy of the filesystem tree).

The second way to specify directories is using **relative** paths.  They are relative becuase it is based on where you currently are
in the tree.  For example, if we happened to be in the `/bin` directory (more on knowing where you are in a moment), then we could refer
to the *cheese* directory as `cheese`.  Notice there is no leading `/` because we are not starting from root.  
Again, assuming we are in the `bin` directory and the `cheese` directory itself had subdirectories *american*, *swiss*, 
and *gouda* then their relative paths would be specified as `cheese/american`, `cheese/swiss`, and `cheese/gouda` respectively
while their absolute paths would be `/bin/cheese/american`, `/bin/cheese/swiss`, and `/bin/cheese/gouda`.

The use of relative paths certainly saves some typing especially...  However, when we wish to access folders in a different 
part of the filesystem tree that is *above* where we are in that tree, using relative paths gets a little more tricky.  It is
not impossible but it may be better to use absolute paths.  For example, if we happened to be in the `/bin/cheese` directory
but then wanted to access something in a say a subdirectory, *shoe* of the *boot* directory, it is probably most clear to access that 
subdirectory using the full path, for example `/boot/shoe` rather than ...

Getting to the question of how do you know where you are, that's where the `pwd` command comes into play.  PWD stands for 
print working directory and it prints out the absolute path of the directory you are currently in.  That is it prints out 
your location in the filesystem tree.  Continuing with our example from above if, if we happened to be in the *gouda* directory
then `pwd` would print out `/bin/cheese/gouda`.

Finally, when you log in you are placed in a certain directory by default.  This directory has a special name, called the **home directory**.
In most cases this directory's absolute path is `/home/username` where *username* is your actual username.  This directory also has a special 
shorthand denoted by the tilde `~`.

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
