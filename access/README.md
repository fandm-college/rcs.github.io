---
sort: 3
---

# How to access resources

Once an account has been created, you can access the different resources as follows in the table below.  Because many of our resources use Linux 
as the operating system, we strongly suggest you go through our Linux tutorial [here](../linux/README.md) if you aren't already familiar with Linux.

| **Resource** | **Access method** |
| MATLAB | COMING SOON|
| ArcGIS | COMING SOON|
| Jupyter | https://jupyterhub.fandm.edu:8000/ |
| RStduio Pro | https://math-r.fandm.edu |
| Webwork | https://math.fandm.edu/webwork2 |
| Globus | {::nomarkdown}<ul><li>Web access at https://app.globus.org/login</li><li>Or using the Connect Personal app downloadable at https://www.globus.org/globus-connect-personal</li>/<ul>{:/} |
| Most other systems | {::nomarkdown}<ul><li>SSH to connect to a server and run commands</li><li>SCP to copy files and directories to/from a server</li>/<ul>{:/} |
| Research cluster | {::nomarkdown}<ul><li>SSH/SCP</li><li>Web access through Open OnDemand at https://rcs-scsn.fandm.edu</li>/<ul>{:/} |

- **Note:** If you will be using SSH/SCP to connect to a server, you must first setup your personal computer using
  the instructions found [here](01_preliminaries)
- A guide to using SSH can be found [here](02_ssh.md)
- A guide to using SCP can be found [here](03_scp.md)
- An introduction to Open OnDemand for web access to the cluster can be found [here](../ood/README.md)

## Troubleshooting Connection Problems

From time to time you may encounter issues connecting especially using SSH/SCP.  Often times the cause is one of the following:

1. Not connecting to VPN when off-campus
2. Username is mistyped
3. Server name is mistyped
4. Password mistyped.  This one is trickier to determine but check things like Cap Lock, Num Lock, etc.

If you continue to have issues connecting then contact us at: dorc@fandm.edu