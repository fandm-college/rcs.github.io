# Create/remove/copy/move files and directories

In some regards, Linux treats directories and file as mostly the same.  As a result, many of the commands described below
can be used on either files or directories with little to no modifaction/distinction.

## Creating a directory

If you need to make a new directory, you can use the `mkdir`.  In its simplest form you would do `mkdir dirName`  replacing `dirName` with
the actual name of the directory you wish to make.  This will make a new directory in the directory you are currently in.  For example,
to create the directory `CS201` as a subdirectory of auser's home directory you would type `mkdir CS201`:

```bash
auser@mycomputer:~$ ls
CS101    CS102    Downloads   myfile.txt
auser@mycomputer:~$ mkdir CS201
auser@mycomputer:~$ ls
CS101    CS102    CS201    Downloads   myfile.txt
auser@mycomputer:~$ 
```

If you wish to make a new directory such as `/home/auser/proj4/code/testcode` but some of the intermediate directories do not already exist, then you 
should use the `-p` option because `mkdir` does not create them by default. Based on the example above, neither the `proj4` nor 
the `proj4/code` directories exist.  By adding the `-p`  then any missing intermediate directories (e..g., /proj4 and /code) will also be created in addition to the /testcode directory.

```bash
auser@mycomputer:~$ mkdir -p /home/auser/proj4/code/testcode
```

## Creating a file

There are two basic ways to create a file.   The first, `touch <filename>` will create a new empty file in the current directory.  For example, `touch instructions.txt`.
You can also add directory/path information to create it somewhere else.  

The second way to create a file using an editor.  See the [text editing tutorial](03_file_edit.md) for more details.

## Deleting a file/directory

The first and most important thing to keep in mind when deleting anything using the command line is that it is **not reversible**.  Operating
systems like Windows, Mac OS X, and even when using a GUI with Linux, typically have a two-step process where directories/files first go
to trash and then the trash has to be emptied to permanently remove it.  The Linux command line is different in this regard.  When you issue the command to delete something 
it is immediately and permanently removed with no way to recover it(unless you happened to back it up somewhere).

In order to delete either a file or a directory, you can use the `rm` command:

1. `rm <filenName>` to delete a file
    - `rm instructions.txt`
2. `rm -rf <directoryName>` to delete a directory and all of its contents
    - `rm -rf CS201`

## Copying files/directories

The `cp` can be used to copy files/directories from one place to another.  It's basic usage is `cp Source Destination`.  As with most other commands, files/directories can be specified with additional path information, but the examples below will be relatively simple just to demonstrate the basics.

```bash
# Make a copy of a file
auser@mycomputer:~$ ls
CS101    CS102    Downloads   myfile.txt    testfile.inf
auser@mycomputer:~$ cp myfile.txt myfile.txt.bkp
auser@mycomputer:~$ ls
CS101    CS102    Downloads   myfile.txt    myfile.txt.bkp  testfile.inf

# Copy a file to another directory
# myfile.txt will now be in the CS101 directory as well
auser@mycomputer:~$ cp myfile.txt CS101/
auser@mycomputer:~$ cd CS101
auser@mycomputer:~$ ls
hw1.py  hw2.py  myfile.txt

# Copy a file to another directory with a different file name
# myfile.txt will now be in the CS101 directory but with the file name myfile.txt.copy
auser@mycomputer:~$ cp testfile.inf CS101/testfile.inf.copy
auser@mycomputer:~$ cd CS101
auser@mycomputer:~$ ls
hw1.py  hw2.py  myfile.txt  testfile.inf.copy

# Copy one directory (and all it's contents) to another directory
# Requires -R
# If destination directory does not exist it will be created
auser@mycomputer:~$ cp -R CS102 CS102_BKP
auser@mycomputer:~$ ls
CS101    CS102  CS102_BKP    Downloads   myfile.txt    myfile.txt.bkp
auser@mycomputer:~$ cd CS102
auser@mycomputer:~$ ls
hw1.py  hw2.py  hw3.py  lect01.py   lect02.py   slides
auser@mycomputer:~$ cd ../CS102_BKP
auser@mycomputer:~$ ls
hw1.py  hw2.py  hw3.py  lect01.py   lect02.py   slides

```

## Moving files/directories

**UNDER CONSTRUCTION**