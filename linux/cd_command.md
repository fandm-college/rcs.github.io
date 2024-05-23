---
sort: 4
---

# Directory navigation

Now that we've covered some basics regarding the organization of directories within Linux, let us now turn our attention
to navigating from one directory to another.  Please note that throughout the rest of this tutorial, our examples will
make extensive use of the `pwd` command.  This is only to make it clear which directory we are in at any given point
in the example.  Normally you would not need to make such extensive use of it.

![Linux directory structure for navigation example](../images/filesystem2.jpg)

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

The first few examples here use relative paths/directory shortcuts mentioned previously to navigate to different 
directories within auser's home directory.  The last example uses an absolute path to navigate to the `images` 
directory which is in a much different (i.e. far away) branch of the directory tree from where we were (`/home/auser/CS101/HW2`).

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

The next example demonstrates the use of `tab` for autocompletion.

```bash
auser@mycomputer:~$ pwd
/home/auser
auser@mycomputer:~$ cd Dow<tab>
auser@mycomputer:~/Downloads$ pwd
/home/auser/Downloads
auser@mycomputer:~/Downloads$ cd
auser@mycomputer:~$ cd CS<tab><tab>
CS101/ CS102/
auser@mycomputer:~$ cd CS
```
In the first case, pressing `tab` once will autocomplete because there is only one possible completion, `Downloads`.  In the second 
case, pressing `tab` just once will not autocomplete because there is more than one possible completion.  Pressing `tab` a 
second time lists the possibilities.  From there you can type a few more letter and try `tab` again, or just type the whole thing.
