---
sort: 2
---

# Accessing resources (Under construction)

In order to acceess Resource Computing resources from off-campus you will have to connect to F&M's VPN, GlobalProtect, and DUO (soon switching to RapidIdentity) for authentication. Instructions for installing these programs are provided below:

- [Instructions for DUO](https://docs.google.com/document/d/1xZ3xIFEj_16zujJMGEPaSxO1-fAb2kbC25WxY7kP1jE/edit?usp=sharing)
- [Instructions for Global Protect VPN](https://docs.google.com/document/d/19nF8gaVCl8_c18pC95X19LQVZnGr3eLAkGlDcnsVV3c/edit?usp=sharing)
- [Instructions for Rapid Identity](coming soon)

If you are having trouble with these programs please contact the F&M helpdesk by calling them at 717-358-6789 or submitting a helpdesk ticket through heldesk.fandm.edu

In addition, there is operating system-specific setup(detailed below) that you will need in order to access our compute resources regardless of whether that access is on or off campus.

## Mac OS X setup

By default, Mac OS X comes with a program called `terminal` which can be used for connecting to systems.  The terminal program can be found in  Utilities folder which can be quickly opened using the keyboard shortcut `Shift+Command+U`.  Once you click on the terminal program to open it, we suggest you save it to your toolbar for faster access in the future.

Once terminal is running you connect using the `ssh` command as in `ssh userName@system.fandm.edu`.  For example, `ssh aweaver29@rcs-scsn.fandm.edu`  **Please note:** You will be prompted to enter your password.  As you type, nothing will display on the screen.  There is no problem and the system does know which characters are being typed.

In order to access graphical applications you must install [XQuartz](https://www.xquartz.org/).  Once installed, you need to start XQuartz (Go to Applications > Utilities > XQuartz) before doing the ssh command.  This will open a window which just has to be running.  Then when you ssh, the command changes slightly to use the `-Y` flag as `ssh -Y aweaver29@rcs-sc.fandm.edu`.

## Windows setup

For Windows machines we recommend installing PuTTY which can be found in the Microsoft Store (It's provided and updated by Simon Tatham).

To setup a new connection

1. Put `userName@system.fandm.edu` in the box labelled `Host Name (or IP address)` (label 1 in the picture below).  For example: `aweaver29@rcs-scsn.fandm.edu`
2. Save the connection for easier access later by entering a name (whatever you want to call it) for it in the box `Saved Sessions` (label 2)  and click `Save`
3. Click `Open` (label 3) to connect to the server.  A window will open up asking for your password.
   **Please note:** As you type, nothing will display on the screen.  There is no problem and the system does know which characters are being typed.

![PuTTY connection windows](putty.png)

To connect to a saved session, select it from the list and click `Load` and then `Open`

