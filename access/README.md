---
sort: 2
---

# Accessing resources (Under construction)

Off-campus access to resources requires the use of F&M's VPN, GlobalProtect, and DUO for authentication.  Instructions for installing them are provided below:

- [Instructions for DUO](https://docs.google.com/document/d/1xZ3xIFEj_16zujJMGEPaSxO1-fAb2kbC25WxY7kP1jE/edit?usp=sharing)
- [Instructions for Global Protect VPN](https://docs.google.com/document/d/19nF8gaVCl8_c18pC95X19LQVZnGr3eLAkGlDcnsVV3c/edit?usp=sharing)

If you are having trouble with these programs please contact the F&M helpdesk by calling them at 717-358-6789 or submitting a helpdesk ticket through heldesk.fandm.edu

Regardless of whether access is on or off campus you will need to setup your system to be able to use SSH.

## Mac OS X setup

By default, Mac OS X comes with a program called `terminal` which can be used for connecting to systems.  Terminal is in the Utilities folder.  The Utilities folder can be quickly opened using the keyboard shortcut `Shift+Command+U`.  Once you click on the terminal program to open it, we suggest you save it to your toolbar for faster access in the furture.

Once terminal is running you can connect to the system using the `ssh` command as in `ssh userName@system.fandm.edu`.  For example, `ssh aweaver29@rcs-sc.fandm.edu`

In order to access graphical applications remotely you should install [XQuartz](https://www.xquartz.org/).  The ssh command then changes slightly with the addition of the  `-Y` flag as in `ssh -Y aweaver29@rcs-sc.fandm.edu`.

## Windows setup

For Windows machines we recommend installing MobaXterm Home Edition found [here](https://mobaxterm.mobatek.net/download.html).

Once you have installed MobaXterm....
