# Creating and removing directories/files

## Creating a directory/file

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

If you wish to make a new directory such as `/data/proj4/code/testcode` but some of the intermediate directories do not already exist, then you 
should use the `-p` option because `mkdir` does not create them by default. Based on our example further above, neither the `proj4` nor 
the `proj4/code` directories exist.  By adding the `-p` as in `mkdir -p /data/proj4/code/testcode`, then any missing directories will also be 
created.

## Deleting a directory/file

The first and most important thing to keep in mind when deleting anything using the command line is that it is **not reversible**.  Operating
systems like Windows, Mac OS X, and even when using a GUI with Linux, typically have a two-step process where directories/files first go
to trash and then the trash has to be emptied to permanently remove it.  On the command line, when you issue the command to delete something
it is immediately and permanently removed (unless it happens to be bcked up somewhere)
`rmdir`

`rm`