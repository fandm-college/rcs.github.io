---
sort: 5
---

# Listing directory contents

The command to see the contents of a directory is `ls`.  By default it will list the contents of the directory
you are currently in.  It will show you both files and directories.  You can see the contents of other directories 
*without navigating to them first* if you add the path of a directory after `ls` .  Here are some examples based again on the 
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

There are additional options you can supply to the `ls` command which will display other useful information.  This is not a complete
list of options, but are some of the more commonly used ones.  Multiple options can be used at once (as the example will show).

| **Option**   | **Meaning**                     |
|:-------------|:--------------------------------| 
| `l`          | Displays additional details such as file permissions, date last modified, etc. |
| `a`          | Displays entire directory contents including any hidden directories/files.  In Linux directories/files are hidden (not displayed by default) if their name begins with a period (e.g., .config, .cache) |
| `h`          | Use with the `l` flag to display directory/file sizes in a more human readable format |
| `S`          | Use with the `l` flag to display directories/files based on size from largest to smallest |
| `R`          | Displays the contents of the current directory as well as the contents of subdirectories, organized by subdirectory |

Below are several examples that demonstrate what information these options display as well as how they can be combined.  In this first 
example you can see the difference between `ls` and `ls -a`  Recall that `.` is the shortcut for the current directory, 
and `..` is the shortcut for the parent directory.  These two directories as well as `.config` and `.hiddenFile` do not show up without
using `a` because they all begin with `.`   

```bash
auser@mycomputer:~$ pwd
/home/auser
auser@mycomputer:~$ ls
CS101    CS102    Downloads   myfile.txt
auser@mycomputer:~$ ls -a
.  ..  .config  CS101  CS102  Downloads  .hiddenFile  myfile.txt
```

This example demonstrates how  `l` option provides more information about directories/files.  This includes things like

1. Whether something is a directory or file (line begins with `d` or not)
2. Size of the directory or file (in bytes by default)
3. Last edit date
4. Permissions

When used in conjunction with the `a` flag we also see this information for hidden directories/files.

```bash
auser@mycomputer:~$ ls -l
total 16
drwxrwxr-x 4 auser auser 4096 Jun 14 07:05 CS101
drwxrwxr-x 2 auser auser 4096 Jun 14 07:05 CS102
drwxrwxr-x 2 auser auser 4096 Jun 14 07:05 Downloads
-rw-rw-r-- 1 auser auser   88 Jun 14 07:29 myfile.txt

auser@mycomputer:~$ ls -la
total 28
drwxrwxr-x 6 auser auser 4096 Jun 14 07:30 .
drwxrwxr-x 4 auser auser 4096 Jun 14 07:06 ..
drwxrwxr-x 2 auser auser 4096 Jun 14 07:30 .config
drwxrwxr-x 4 auser auser 4096 Jun 14 07:05 CS101
drwxrwxr-x 2 auser auser 4096 Jun 14 07:05 CS102
drwxrwxr-x 2 auser auser 4096 Jun 14 07:05 Downloads
-rw-rw-r-- 1 auser auser    0 Jun 14 07:30 .hiddenFile
-rw-rw-r-- 1 auser auser   88 Jun 14 07:29 myfile.txt
```

This next example shows the output of the`R` option both on its own and in conjuction with other flags.

```bash
auser@mycomputer:~$ cd /
auser@mycomputer:/$ ls -R data
data:
proj1  proj2  proj3

data/proj1:
code  data  images

data/proj1/code:

data/proj1/data:

data/proj1/images:
IMG_0007.JPG  IMG_0009.JPG  IMG_0011.JPG  IMG_0013.JPG  IMG_0016.JPG  IMG_0018.JPG  IMG_0021.JPG  IMG_0023.JPG  IMG_0025.JPG
IMG_0008.JPG  IMG_0010.JPG  IMG_0012.JPG  IMG_0015.JPG  IMG_0017.JPG  IMG_0020.JPG  IMG_0022.JPG  IMG_0024.JPG

data/proj2:

data/proj3:

auser@mycomputer:/$ ls -lR data
data:
total 12
drwxrwxr-x 5 auser auser 4096 Jun 14 07:06 proj1
drwxrwxr-x 2 auser auser 4096 Jun 14 07:06 proj2
drwxrwxr-x 2 auser auser 4096 Jun 14 07:06 proj3

data/proj1:
total 12
drwxrwxr-x 2 auser auser 4096 Jun 14 07:06 code
drwxrwxr-x 2 auser auser 4096 Jun 14 07:06 data
drwxrwxr-x 2 auser auser 4096 Jun 14 07:11 images

data/proj1/code:
total 0

data/proj1/data:
total 0

data/proj1/images:
total 20688
-rwxrw-rw- 1 auser auser 1271975 Dec  1  2012 IMG_0007.JPG
-rwxrw-rw- 1 auser auser 1224024 Dec  1  2012 IMG_0008.JPG
-rwxrw-rw- 1 auser auser 1215564 Dec  1  2012 IMG_0009.JPG
-rwxrw-rw- 1 auser auser 1148051 Dec  1  2012 IMG_0010.JPG
-rwxrw-rw- 1 auser auser 1233984 Dec  1  2012 IMG_0011.JPG
-rwxrw-rw- 1 auser auser 1313847 Dec  1  2012 IMG_0012.JPG
-rwxrw-rw- 1 auser auser 1217888 Dec  1  2012 IMG_0013.JPG
-rwxrw-rw- 1 auser auser 1202087 Dec  1  2012 IMG_0015.JPG
-rwxrw-rw- 1 auser auser 1207796 Dec  1  2012 IMG_0016.JPG
-rwxrw-rw- 1 auser auser 1278853 Dec  1  2012 IMG_0017.JPG
-rwxrw-rw- 1 auser auser 1179913 Dec  1  2012 IMG_0018.JPG
-rwxrw-rw- 1 auser auser 1204752 Dec  1  2012 IMG_0020.JPG
-rwxrw-rw- 1 auser auser 1278673 Dec  1  2012 IMG_0021.JPG
-rwxrw-rw- 1 auser auser 1105002 Dec  1  2012 IMG_0022.JPG
-rwxrw-rw- 1 auser auser 1359670 Dec  1  2012 IMG_0023.JPG
-rwxrw-rw- 1 auser auser 1335757 Dec  1  2012 IMG_0024.JPG
-rwxrw-rw- 1 auser auser 1371282 Dec  1  2012 IMG_0025.JPG

data/proj2:
total 0

data/proj3:
total 0

```

These final examples demonstrate the effect of using the `h` and `S` flags.  In particular it is much easier to determine 
each individual file size as well as the total amount of space being used in a directory when using the `h` flag.

```bash
auser@mycomputer:/data/proj1/images$ pwd
/data/proj1/images
auser@mycomputer:/data/proj1/images$ ls -la
total 20696
drwxrwxr-x 2 auser auser    4096 Jun 14 07:11 .
drwxrwxr-x 5 auser auser    4096 Jun 14 07:06 ..
-rwxrw-rw- 1 auser auser 1271975 Dec  1  2012 IMG_0007.JPG
-rwxrw-rw- 1 auser auser 1224024 Dec  1  2012 IMG_0008.JPG
-rwxrw-rw- 1 auser auser 1215564 Dec  1  2012 IMG_0009.JPG
-rwxrw-rw- 1 auser auser 1148051 Dec  1  2012 IMG_0010.JPG
-rwxrw-rw- 1 auser auser 1233984 Dec  1  2012 IMG_0011.JPG
-rwxrw-rw- 1 auser auser 1313847 Dec  1  2012 IMG_0012.JPG
-rwxrw-rw- 1 auser auser 1217888 Dec  1  2012 IMG_0013.JPG
-rwxrw-rw- 1 auser auser 1202087 Dec  1  2012 IMG_0015.JPG
-rwxrw-rw- 1 auser auser 1207796 Dec  1  2012 IMG_0016.JPG
-rwxrw-rw- 1 auser auser 1278853 Dec  1  2012 IMG_0017.JPG
-rwxrw-rw- 1 auser auser 1179913 Dec  1  2012 IMG_0018.JPG
-rwxrw-rw- 1 auser auser 1204752 Dec  1  2012 IMG_0020.JPG
-rwxrw-rw- 1 auser auser 1278673 Dec  1  2012 IMG_0021.JPG
-rwxrw-rw- 1 auser auser 1105002 Dec  1  2012 IMG_0022.JPG
-rwxrw-rw- 1 auser auser 1359670 Dec  1  2012 IMG_0023.JPG
-rwxrw-rw- 1 auser auser 1335757 Dec  1  2012 IMG_0024.JPG
-rwxrw-rw- 1 auser auser 1371282 Dec  1  2012 IMG_0025.JPG
```

```bash
auser@mycomputer:/data/proj1/images$ ls -lh
total 21M
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0007.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0008.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0009.JPG
-rwxrw-rw- 1 auser auser 1.1M Dec  1  2012 IMG_0010.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0011.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0012.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0013.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0015.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0016.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0017.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0018.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0020.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0021.JPG
-rwxrw-rw- 1 auser auser 1.1M Dec  1  2012 IMG_0022.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0023.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0024.JPG
-rwxrw-rw- 1 auser auser 1.4M Dec  1  2012 IMG_0025.JPG
```

```bash
auser@mycomputer:/data/proj1/images$ ls -lhS
total 21M
-rwxrw-rw- 1 auser auser 1.4M Dec  1  2012 IMG_0025.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0023.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0024.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0012.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0017.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0021.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0007.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0011.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0008.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0013.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0009.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0016.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0020.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0015.JPG
-rwxrw-rw- 1 auser auser 1.2M Dec  1  2012 IMG_0018.JPG
-rwxrw-rw- 1 auser auser 1.1M Dec  1  2012 IMG_0010.JPG
-rwxrw-rw- 1 auser auser 1.1M Dec  1  2012 IMG_0022.JPG
```

## Directory/file permissions

Here we will briefly discuss directory/file permissions as it impacts things like writing files, creating directories, etc.
To begin, every directory and file is associated (we use the term owned) by a specific individual user and a group.  A group is a colection of
one or more users.  For now it's not too important how groups get created or how an indivdual user gets added to various groups.  As an aside, to see
what groups you belong to use the command `groups`

You can see the user and group associated with files/directories when you use the `-l` option with `ls`.  In the example below, the third column lists
the user (auser in this case) and the fourth column lists the group (also auser, but the group auser not the individual user) who own the files.

```bash
auser@mycomputer:/data/proj1/images$ ls -lhS
total 21M
-rwxrw-rw- 1 auser auser 1.4M Dec  1  2012 IMG_0025.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0023.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0024.JPG
-rwxrw-rw- 1 auser auser 1.3M Dec  1  2012 IMG_0012.JPG
```

To make the distinction between users and groups consider the example below where profnovak is the user who owns the files and the novaklab group (i.e., profnovak and anyone working with them)
is the group associated with the files

```bash
auser@mycomputer:/data/novak/proj1$ ls -lhS
total 21M
-rwxrw---- 1 profnovak novaklab 1.4M Dec  1  2012 IMG_0025.JPG
-rwxrw---- 1 profnovak novaklab 1.3M Dec  1  2012 IMG_0023.JPG
-rwxrw---- 1 profnovak novaklab 1.3M Dec  1  2012 IMG_0024.JPG
-rwxrw---- 1 profnovak novaklab 1.3M Dec  1  2012 IMG_0012.JPG
```

Understanding of user and group ownership is important because permissions (specifically permission to read, to write, and to execute) are enforced at three different levels

1. User level
2. Group level
3. World level (i.e., everyone else)

The permissions are also displayed when using the `-l` option with `ls`.  It is the first column displayed. Except for the first character in the permissions, a dash (- ) means that 
particular permission is not available.  The meaning of permissions are broken down as follows:

1. The first character indicates if the it is a file (denoted by -) or a directory (denoted by d).  There are a few other characters that may appear in the first position but those aren't important for now.
2. The next three characters denote the read, write, and execute permission for the user.  In the above example, the user can read, write, and execute the files (although it doesn't make sense to run the picture as a command)
3. After the user permissions is the three characters to provide the group permissions.  In this case, members of the group can also read and write the files but not execute them.
4. The last group of three provide the permissions for everyone else on the system.  In this case it's all dashes meaning no one else can do anything.

Practically speaking, that means if you get a permission error to read, write, or execute either a file or a directory you have some sense of what that means.  Since users always have a home directory, the permissions are typically correctly set so that you should not get permission errors when dealing with files/directories in your home directory.  Outside of your home directory permissions may need to be adjusted (by a system administrator) to allow access.
