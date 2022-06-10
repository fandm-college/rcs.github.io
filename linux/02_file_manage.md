# File organization and management

**UNDER CONSTRUCTION**

Understanding how directories and files are organized in Linux is perhaps the biggest obstacle when it
comes to gaining proficiency and confidence working with the command line.  This tutorial will introduce
some basic concepts related to file organization which will hopefully allow you to be more comfortable woking with the
Linux command-line.  This tutorial will also discuss some important Linux commands related to directory and file management.

## Basic directory structure

Directories (think of a directory as a folder) in Linux  are organized in a strictly hierarchical fashion known as a tree.
More appropriately this structure is what we might consider to be an upside-down tree with the base or root of the 
filesystem at the top (see picture below).  In fact the top-most level of the filesystem is a single directoy called 
the root directory.  The root directory contains all other directories and their subdirectories and is denoted as / in Linux. 

![Linux directory structure](filesystem.jpg)

In the example above, the immediate subdirectories of root are *bin*, *boot*, *dev*, *home*, and *usr*.  These are usually denoted `/bin`, `/boot`, 
`/dev`, `/home`, and `/usr`.  That is each one is specified in relation to the root directory.  This full specification of any 
directory on the system beginning with the root directory is known as its **absolute path**.  For example the subdirectories of 
the *cheese* directory would be specified as absolute paths `/bin/cheese/american`, `/bin/cheese/gouda`, 
and `/bin/cheese/swiss` respectively. 

In general you might think of a directory's path as instructions on how you would walk to that directory.  In the case of absolute paths it
is the directions for walking to a directory always starting from root.  Please note too that just typing a path does not navigate to a directory.
There is a specific command for navigating to a directoy discussed in the [Directory navigation section](#directory-navigation) further below.  In this 
section of the tutorial we are discussing the idea of paths in a general way because they are used in conjunction with many different Linux commands.  They 
are also often used when running scientific software to specify things like where output should go.

There is a second way to specify the path of a directory known as **relative paths**. They are relative becuase 
it is based on where you currently are in the tree.  For example, if you happened to be in the `/bin` directory (more on knowing where you are in a moment), 
then we could refer to the *cheese* directory as the relative path `cheese`.  Notice there is no leading `/` because we are not starting from root.  Similarly 
(again assumming we are in the *bin* directory) the relative paths for the *cheese* subdirectories would be
`cheese/american`, `cheese/gouda`, and `cheese/swiss`.

### Which directory am I in?

You can determine which directory you are currently in using the `pwd` command.  This command prints out the 
absolute path of the directory you are currently in.  Or put another way, it prints out your location in the filesystem tree.  
For example if we happened to be in the *gouda* directory then `pwd` would print out `/bin/cheese/gouda`.

###  Directory shortcuts

There are a few characters that represent directory shortcuts which are helpful to keep in mind.  The first is the the tilde, `~`.  When you access 
the Linux command line, you are initially placed into some default directory.  In most (but not all) cases this directory's absolute 
path is `/home/username` where *username* is your actual username.  This directory has a special name, called the **home directory** 
and `~` refers to the absolute path of your home directory.  So if auser is logged in `~` refers to `/home/auser` but if cnovak is 
logged in then it would refer to `/home/cnovak`.

Two additional and related shortcuts are `.` and `..`.  The first refers to the directory you are currently in.  The second
shortcut refers to the parent directory of the directory you are in.  For example, if you are in `/home/auser`, then
`.` refers to /home/auser and `..` refers to /home.  This second shortcut in particular can be especially helpful
when referring to directories using relative paths.  If, for example, you were in the /bin/cheese/swiss directory and
you needed to do something with the /bin/cheese/gouda directory, you could do so with the following relative path:
`../gouda`

Finally, you can usually use tab to autocomplete a path.  If you begin to type a path (absolute or relative) and 
press tab, then Linux will try to automatically complete the path.  If there is more than one possible completetion, 
then the first tab will not do anything but pressing tab a second time will  list all possible directory paths that 
may complete what you have typed so far.  You can then type additonal characters to complete the path.

### Should I use relative or absolute paths?

The question of whether to use relative or absolute paths has no definitive answer.  If the directory you need to access is
relatively near to where you are in the directory tree, then it will likely be easier/more straightforward to use relative
paths.  Otherwise you will likely have to use an absolute path.  What counts as "realtively near" is a matter of preference
and may evolve over time as you become more comfortable with the Linux command line and directory structure.

### Directory and file permissions


## Directory navigation

The examples in this section will be based on the directory structure given in the picture below.  Please note
that here we are making extensive use of `pwd` only to make clear which directory we are in after each command.  Normally you 
would not use it as frequently as we are here.

![Linux directory structure for navigation example](filesystem2.jpg)

The command to move from one directory to another is the `cd` command.  With no arguments this command
will change you to your home directory regardless of which directory you are currently in.

```bash
auser@mycomputer:~/Downloads$ pwd
/home/auser/Downloads
auser@mycomputer:~/Downloads$ cd
auser@mycomputer:~$ pwd
/home/auser
```

Most of the time you will provide the path to some directory as an argument to the `cd` command.  That path
may either be an absolute or relative path as the following example demonstrates.

```bash
auser@mycomputer:~/Downloads$ pwd
/home/auser/Downloads
auser@mycomputer:~/Downloads$ cd ~
auser@mycomputer:~$ pwd
/home/auser
auser@mycomputer:~$ cd CS101/HW1
auser@mycomputer:~/CS101/HW1$ pwd
/home/auser/CS101/HW1
auser@mycomputer:~/CS101/HW1$ cd ../HW2
auser@mycomputer:~/CS101/HW2$ pwd
/home/auser/CS101/HW2
auser@mycomputer:~/CS101/HW2$ cd /data/proj1/images
auser@mycomputer:/data/proj1/images$ pwd
/data/proj1/images
```

The first few examples here use relative paths and in some cases some of the directory shortcuts mentioned in the previous
section to navigate to different directories within auser's home directory.  The last example uses an absolute path
to navigate to the images directory which is in a much different (i.e. far away) branch of the directory tree
from where we were (/home/auser/CS101/HW2).

There is one additional shortcut that can be quite useful, especially in situations where you may need to navigate back and forth
between directories which are far away from each other in the directory tree (as in our last example).  Using `cd -` will navigate
to the directory you were just in.  For example:

```bash
auser@mycomputer:~/CS101/HW2$ pwd
/home/auser/CS101/HW2
auser@mycomputer:~/CS101/HW2$ cd /data/proj1/images
auser@mycomputer:/data/proj1/images$ pwd
/data/proj1/images
auser@mycomputer:/data/proj1/images$ cd -
auser@mycomputer:~/CS101/HW2$ pwd
/home/auser/CS101/HW2
auser@mycomputer:~/CS101/HW2$ cd -
auser@mycomputer:/data/proj1/images$ pwd
/data/proj1/images
```

The next example demonstrates the use of Tab for autocompletion

```bash
auser@mycomputer:~$ pwd
/home/auser
auser@mycomputer:~$ cd Dow <Tab>
auser@mycomputer:~/Downloads$ pwd
/home/auser/Downloads
auser@mycomputer:~/Downloads$ cd
auser@mycomputer:~$ cd CS <Tab><Tab>
CS101/ CS102/
auser@mycomputer:~$ cd CS
```

## Listing directory contents

The command to see the contents of a directory is `ls`.  By default it will list the contents of the directory
you are currently in.  It will show you both the files and directories.  You can see the contents of other directories 
without navigating to them first if you add the path of a directory after `ls` .  Here are some examples based again on the 
directory structure from the previous section.  Here we see both the directories and the files (myfile.txt and README, respectively)
in the different directories.

```bash
auser@mycomputer:~$ pwd
/home/auser
auser@mycomputer:~$ ls
CS101    CS102    Downloads   myfile.txt
auser@mycomputer:~$ ls /data
proj1    proj2    proj3       README
auser@mycomputer:~$ 
```

There are additional options you can supply to the `ls` command which will output display other useful information.  Multiple
options can be used at once (as the example will show)

- `-l` --> lists additional details include file permissions, date last modified, etc
- `-a` --> lists all contents of the directory including any hidden files.  In Linux, directories/files are considered hidden (i.e. not displayed by default) if their name begins with a period (e.g., .config, .cache)
- `-h` --> lists information in human readable format (mostly related to file sizes)
- `-lS` --> Displays files based on size from largest to smallest
- `-R`  --> lists files in current directory as well as files in all subdirectories, organized by subdirectory

## Where's my file or directory?

Sometimes you will need to access a file or directory but you don't remember where it is.  That is where the `find` command comes in
handy.
`find`

## Making a new directory

If you need to make a new directory, you can use the `mkdir`.  In its simplest form you would do `mkdir dirName`  replacing `dirName` with
the actual name of the directory you wish to make.  This will make a new directory in the directory you are currently in.  For example,

```bash
auser@mycomputer:~$ ls
CS101    CS102    Downloads   myfile.txt
auser@mycomputer:~$ mkdir CS201
auser@mycomputer:~$ ls
CS101    CS102    CS201    Downloads   myfile.txt
auser@mycomputer:~$ 
```

In this example we have created the directory `CS201` as a subdirectory of auser's home directory.


`mkdir`

## Copying and moving directories/files

There are separate commands for copying and moving.  We will begin with the copy command which is `cp`.

`mv`

`cp`

