
Following instructions are for stopping:
- Autonomous Databases
- Compute VMs
- DBCS VMs
- OAC native

Step 1:
Install and configure OCI CLI on a Compute Intance (Linux VM with one CPU would be ok) in your tenancy.
https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm
It takes about 10min

This version uses Instance Principals for Authenticaion:
create a Dynamic Group and add a rule for the OCID of the compute instance where the scripts will run
create a policy in the root compartment as follows:
Allow dynamic-group <group_name> to use all-resources in tenancy


Step 2:
Save the delivered shell scripts on the VM, e.g. in /home/opc/ocicli/autostopping/
Create a sub-directory named log: i.e. /home/opc/ocicli/autostopping/log/


Step 3:
Change the WHITELIST_FILE path in the .sh scripts accordingly. See Step 7.
e.g.: WHITELIST_FILE="/home/opc/ocicli/autostopping/whitelist.txt"


Step 4:
Make the shell scripts executable
chmod +x /home/opc/ocicli/autostopping/*.sh


Step 5: 
As we are using Instance Principals for Authentication, there is no config file contaning the Tenancy ID:
Change the value of TENANCY_OCID to your tenancy ID in all scripts


Step 6: 
Create an automation using corntab. In the following example shutting down the resources every day at 6p.m. and keeping the log files for 7 days.
0 18 * * * /home/opc/ocicli/autostopping/stop_adbs.sh > /home/opc/ocicli/autostopping/log/stop_adbs_`date '+\%Y\%m\%d_\%H\%M\%S'`.log
0 18 * * * /home/opc/ocicli/autostopping/stop_dbsystem_nodes.sh > /home/opc/ocicli/autostopping/log/stop_dbsystem_nodes_`date '+\%Y\%m\%d_\%H\%M\%S'`.log
0 18 * * * /home/opc/ocicli/autostopping/stop_computes.sh > /home/opc/ocicli/autostopping/log/stop_computes_`date '+\%Y\%m\%d_\%H\%M\%S'`.log
0 18 * * * /home/opc/ocicli/autostopping/stop_oac.sh > /home/opc/ocicli/autostopping/log/stop_oac_`date '+\%Y\%m\%d_\%H\%M\%S'`.log
0 3 * * * find /home/opc/ocicli/autostopping/log/ -type f -mtime +7 -name '*.log' -execdir rm -- '{}' \;

make sure your environment is sourced in cron by add following line to crontab (change the path to your .profile file): 
SHELL=/bin/bash
BASH_ENV=/home/opc/.bash_profile

IMPORTANT: the scripts do not start the resources in the next morning:
Option 1: every one in the team starts only the resources needed manually.
Option 2: duplicate the scripts and replace "stop" by "start" and create crontab roles to run them


Step 7 (Optional):
To prevent specific instances from auto stop, add a TAG named "AutoStopping" with the value "NO" to the resource


Step 8 (Optional):
To prevent all ADBs, Compute, and DB Systems of a specific Compartment to be stopped, add the TAG to the Compartment itself.
Some Compartments like ManagedCompartmentForPaaS cannot be edited. In this case add the Compartment OCID ot the whitelist.txt file.
