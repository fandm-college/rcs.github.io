---
sort: 2
---

# Accessing servers

You will primarily use two commands/utilities to access Research Computing resources from your personal computer.

1. `ssh` to connect to a server and run commands
2. `scp` to copy files to/from a server

Before getting into the details of these two commands, there is some setup required on your personal computer in order to use these commands.

## Preliminaries

In order to acceess Resource Computing resources from off-campus (i.e., not directly connected to F&M's network) you will have to connect to F&M's VPN, GlobalProtect, and use DUO (soon switching to RapidIdentity) for authentication. Instructions for installing these programs are provided below:

- [Instructions for DUO](https://docs.google.com/document/d/1xZ3xIFEj_16zujJMGEPaSxO1-fAb2kbC25WxY7kP1jE/edit?usp=sharing)
- [Instructions for Global Protect VPN](https://docs.google.com/document/d/19nF8gaVCl8_c18pC95X19LQVZnGr3eLAkGlDcnsVV3c/edit?usp=sharing)
- [Instructions for Rapid Identity (coming soon)](coming soon)

If you are having trouble with these programs please contact the F&M helpdesk by calling them at 717-358-6789 or submitting a helpdesk ticket through [heldesk.fandm.edu](https://helpdesk.fandm.edu)
In addition, there is operating system-specific setup(detailed below) that you will need to perform in order to access our compute resources regardless of whether that access is on or off campus.

## Mac OS X setup

By default, Mac OS X comes with a program called `terminal` which can be used for connecting to our systems.  The terminal program can be found in  Utilities folder which can be quickly opened using the keyboard shortcut `Shift+Command+U`.  Once you click on the terminal program to open it, we suggest you save it to your toolbar for faster access in the future.

If you will need to use an application that displays graphical windows (e.g., Matlab) then you must install [XQuartz](https://www.xquartz.org/).

## Windows setup

For Windows we recommend installing PuTTY which can be found in the Microsoft Store (It's provided and updated by Simon Tatham).
If you will need to use an application that displays graphical windows (e.g., Matlab) we recommend you install [VcXsrv](https://sourceforge.net/projects/vcxsrv/) (Click the green Download button). 

For scp we recommend using WinSCP which can be downloaded [here](https://winscp.net/eng/download.php) 