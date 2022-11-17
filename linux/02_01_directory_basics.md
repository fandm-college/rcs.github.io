# Basic directory structure

In Linux, directories (think of a directory as a folder) are organized in a strictly hierarchical fashion known as a tree.
Or more appropriately, an upside-down tree with the base or root of the tree at the top (see picture below).  In fact this top most
directory is a single directory called the *root* directory.  The root directory contains all other directories and their 
subdirectories. In Linux the root directory is denoted by `/`. 

![Linux directory structure](filesystem.jpg)

In the example above, the root directory contains five immediate subdirectories,*bin*, *boot*, *dev*, *home*, 
and *usr*.  These are usually denoted `/bin`, `/boot`, 
`/dev`, `/home`, and `/usr`.  That is each one is specified in relation to the root directory.  This full specification of any 
directory on the system beginning with the root directory is known as its **absolute path**.  For example the subdirectories of 
the *cheese* directory would be specified as absolute paths `/bin/cheese/american`, `/bin/cheese/gouda`, 
and `/bin/cheese/swiss` respectively. 

In general you might think of a directory's path as instructions on how you might get to that directory.  In the case of absolute paths it
is the directions for getting to a directory always starting from root.

There is a second way to specify the path of a directory known as **relative paths**. They are relative becuase 
they are based on where you currently are in the tree.  For example, if you happened to be in the `/bin` directory (more on knowing where you are in a moment), then we could refer to the *cheese* directory as the relative path `cheese`.  Notice there is no leading `/` because we are not starting from root.  Instead we are starting from where we currently are.  Similarly, again assumming we are in the `/bin` directory,
the relative paths for the `cheese` subdirectories would be `cheese/american`, `cheese/gouda`, and `cheese/swiss`.

Please note too that just typing a path does not actually navigate to a directory. There is a specific command for navigating to a directoy discussed in the next portion of this tutorial.  Here we are discussing the idea of paths in a general way because they are used in conjunction with many different Linux commands.  They are also often used when running scientific software to specify things like where output should go. So
it is important (and hopefully easier) to understand them to some extent on their own before using them in conjuction with various commands.

## Which directory am I in?

You can determine which directory you are currently in using the `pwd` command.  This command prints out the 
absolute path of the directory you are currently in.  Or put another way, it prints out your location in the filesystem tree.  
For example if we happened to be in the *gouda* directory then `pwd` would print out `/bin/cheese/gouda`.

Also, when you initially log in, Linux places you into a default directory.  In most (but not all) cases this directory's absolute 
path is `/home/username` where *username* is your actual username.  For example, for user *auser* home is `/home/auser`, and for
user *cnovak* it is `/home/cnovak`.  This directory has a special name, the **home directory** or **home** for short.

##  Directory shortcuts

There are a few characters that represent directory shortcuts which are helpful to keep in mind.  The first is the the tilde, `~`, which
refers to your home directory.  Two additional and related shortcuts are `.` and `..`.  The first refers to the directory 
you are currently in.  The second  refers to the parent directory of the directory you are in.  For example, if you 
are in `/home/auser`, then `.` refers to `/home/auser` and `..` refers to `/home`.  This second shortcut in particular can
be especially helpful when referring to directories using relative paths.  If, for example, you were in the 
`/bin/cheese/swiss` directory and you needed to do something with the `/bin/cheese/gouda` directory, you might
 do so with the following relative path:

`../gouda`

Finally, you can usually use `tab` to autocomplete a path.  If you begin to type a path (absolute or relative) and 
press tab, then Linux will try to automatically complete the path.  If there is more than one possible completetion, 
then the first tab will not do anything but pressing tab a second time will list all possible directory paths that 
may complete what you have typed so far.  You can then type additonal characters to complete the path.

## Should I use relative or absolute paths?

The question of whether to use relative or absolute paths has no definitive answer.  If the directory you need to access is
relatively near to where you are in the directory tree, then it will likely be easier/more straightforward to use relative
paths.  Otherwise you will likely have to use an absolute path.  What counts as "realtively near" is a matter of preference
and may evolve over time as you become more comfortable with the Linux command line and directory structure.