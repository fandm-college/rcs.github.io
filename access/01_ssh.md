# SSH

You should use SSh in order to connect to a server to run programs.  When you connect, you will be using a command-line based interface to interact with the server.
If you aren't familiar with such means of interaction and/or aren't familiar with the Linux operating system, we recommend looking through our Linux tutorial
beginning with the ![introduction to the command line](../linux/01_commandline.md)  

Two quick notes related to `ssh`

1. The first time you connect to a seerver, you may see a message about the authenticity of the host.  Type `yes` (the entire word) to continue connecting.
2. If you are prompted for a password, when you type your password **nothing will display on the screen**.  There is no problem and the system does know which characters are being typed.

## Mac OS X

1. Open the terminal application
2. If you need to run a graphical application on the server (e.g., Matlab), start XQuartz (Go to Applications > Utilities > XQuartz).  This will open a window which just has to be running
3. Use the `ssh` command to connect to the server
    - `ssh <userName>@<serverName>` For example `ssh rliu7@rcs-scsn.fandm.edu`
    - In the case where you are running a graphical application, the command changes to `ssh -Y <userName>@<serverName>` as in `ssh -Y rliu7@rcs-scsn.fandm.edu`

## Windows

To setup a new connection

1. Put `<userName>@<serverName>` in the box labelled **Host Name (or IP address)** (label 1 in the picture below).  For example: `rliu7@rcs-scsn.fandm.edu`
2. Save the connection for easier access later by entering a name (whatever you want to call it) it in the box labelled **Saved Sessions** (label 2) and click **Save**

![PuTTY connection window](putty.png)

For sessions that run graphical applications, some adjustments must be made to the configuration:
  
1. Select the session that needs to use graphical applications and click **Load** 
2. In the box on the left-hand-side of the window labelled **Category:** click the following (in order)
    a. **Connection**
    b. **SSH**
    c. **X11**
3. Check the box labelled **Enable X11 forwarding**.  At this point, your PuTTY window should look similar to the picture below.
4. Go back to the **Category:** box and click **Session**.  Then click **Save** to save the new settings.

![PuTTY X11 forwarding](puttyX11.jpg)

To actually connect to a server, double click the name of the server in the **Saved Sessions** box, or single click it and then click **Open**.  If will be using a 
graphical application, you should start the VcXsrv application before connecting.

There may be times you wish to copy/paste text to/from the PuTTY window.  This can be done but doesn't work the way it normally would in Windows.
Instructions can be found [here](https://www.alphr.com/copy-paste-putty/) on how to do this.

## Troubleshooting Connection Problems

From time to time you may encounter issues connecting.  Often times the cause is one of the following:

1. Not connecting to VPN when off-campus
2. Username is mistyped
3. Server name is mistyped
4. Password mistyped.  This one is trickier to determine but check things like Caps Lock, Num Lock, etc.

If you continue to have issues connecting then contact us at: dorc@fandm.edu