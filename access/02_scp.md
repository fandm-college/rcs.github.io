# SCP
SCP stands for secure copy and is the command used to copy files between your personal computer and the research server you are using
(inclding the cluster).  That is, all commands and examples assume you are on your personal computer and not on the research server.

This tutorial also assumes you have some familiarity with Linux directory structure [See our tutorial here](../linux/02_00_directory_intro.md).
Both Mac OS X and Windows employ a tree-like directory structure similar to Linux, so the same basic ideas apply on these operating systems with the caveat that
Windows uses a drive (usualy C:\\) as part of its convention.

On Mac OS X you will be using terminal to perform SCP using the command *scp*.  On Windows, you must have PuTTY installed and you will be using cmd.exe to do SCP using the command
*pscp* instead of *scp*.

## Copying a file
Below is an example showing the the basic form of the command to copy one (or possibly a few files) from your local computer to a remote server:

```bash
scp *.job auser@rcs-scsn.fandm.edu:/home/auser/
```

- **scp** (or pscp on Windows) is the command for SCP
- **\*.job** are the file(s) we want to copy to the server.  In this example, we are copying all files that end with *.job* in the current directory we are in on the personal computer
- **auser@rcs-scsn.fandm.edu** is the research server we want to copy the files to including our username.
- **:/home/auser** is the directory on the research server we are copying to.  In this example, it is the user's home directory.

The command to copy file(s) from the research server to your is very similar:

```bash
scp auser@rcs-scsn.fandm.edu:/home/auser/*.job /Users/freddy/Desktop/
```

- **scp** (or pscp on Windows) is the command for SCP
- **auser@rcs-scsn.fandm.edu** is the research server, including our username, we want to copy from
- **:/home/auser/\*.job** are the files we want to copy from the server.  In this example, we are copying all files that end with *.job* in auser's home directory
- **/Users/freddy/Desktop/** is the directory on our personal computer where we are copying the files to.  In this example, it is the user's Desktop directory.

## Copying a directory
The command to copy a directory is almost exactly the same as above except after the *scp* command we include *-r*.  The examples below
demonstrate copying a directory called *output* to/from a research server similar to the examples above.

```bash
scp -r output auser@rcs-scsn.fandm.edu:/home/auser/
scp - r auser@rcs-scsn.fandm.edu:/home/auser/output/ /Users/freddy/Desktop/
```
