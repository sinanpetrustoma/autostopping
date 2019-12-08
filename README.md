
Step 1:
Install and configure OCI CLI on a Compute Intance (Linux VM with CPU would be ok) in your tenancy.
https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm
It takes about 10min


Step 2:
Save the delivered scripts on a VM, e.g. in /home/opc/ocicli/autostopping/


Step 3:
Make the shell scripts executable
chmod +x /home/opc/ocicli/autostopping/*.sh


Step 4: 
Create an automation using corntab. In the following example shutting down the resources every day at 6p.m.
0 18 * * * /home/opc/ocicli/autostopping/stop_adbs.sh > /home/opc/ocicli/autostopping/stop_adbs.log
0 18 * * * /home/opc/ocicli/autostopping/stop_dbsystem_nodes.sh > /home/opc/ocicli/autostopping/stop_dbsystem_nodes.log
0 18 * * * /home/opc/ocicli/autostopping/stop_computes.sh > /home/opc/ocicli/autostopping/stop_computes.log


IMPORTANT: the scripts do not start the resources in the next morning:
Option 1: every one in the team starts only the resources needed manually.
Option 2: duplicate the scripts and replace "stop" by "start" and create crontab roles to run them


Step 5 (Optional):
To prevent specific instances from auto stop, add a TAG named "AutoStopping" with the value "NO" to the resource
