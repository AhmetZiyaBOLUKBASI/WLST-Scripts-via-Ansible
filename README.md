# HOW to EXECUTE WLST SCRIPTS via ANSIBLE PLAYBOOKS

This project serves as an example of executing scripts prepared for the WebLogic Scripting Tool across multiple servers via Ansible. In cases where not all servers have the same WebLogic environment variables set, the required environment variables are retrieved from a running WebLogic process on the server to avoid errors. Consequently, the playbook template provided in this project enables the execution of any desired WLST script through Ansible.

## Scenario:

Assume that the DevOps team deploys applications using their own CI/CD pipelines. However, in some cases, after performing a manual deployment for troubleshooting, the "Release Configuration" button on the WebLogic server may inadvertently be overlooked. In such a scenario, the CI/CD pipeline will encounter an error on its next run. To resolve this issue, the [releaseConfiguration.py](https://github.com/AhmetZiyaBOLUKBASI/WLST-Scripts-via-Ansible/blob/main/releaseConfiguration.py) script is manually executed on the server each time.
```bash
cat ./releaseConfiguration.py
   connect("<your username>", "<your password>", "t3s://<your admin consol URL>:<your admin console https port>")
   cd('edit:/')
   cancelEdit('y')
   disconnect()
   print "---------------------------------------------------------------------------------"
   print " It Could Be Deploy New Package on the Server <your servername> !"
   print "---------------------------------------------------------------------------------"
   exit()

<Oracle Installation Path>/oracle_common/common/bin/wlst.sh releaseConfiguration.py
```

 
![Lock and Edit Button](https://docs.oracle.com/en/storage/storage-software/storagetek-tape-analytics/2.4/stais/img/wl_lockedit.png)

To address this, we will develop a project that can be automatically executed via the Ansible Automation API.

## How Do the Playbook Tasks Work?

- A variable named **work_on_hosts** is defined for the servers where the playbook will run. By declaring this variable as an extra variable, we achieve a flexible playbook that can operate under any circumstances.
- In the first task, titled "**Read wlst.sh Path From Weblogic Process**", the *ORACLE_HOME* variable is retrieved from any running WebLogic process on the server.
- In the task titled "**Set WLST Path Variable for Ansible**", the full path of the *wlst.sh* script is assigned to the variable ***wlstToolPath***.
- Finally, the task "**Run WLST Script For Release Config**" executes the necessary script.

> [!IMPORTANT]
> Consider that four variables:
>  - userName
>  - console_pass
>  - inventory_hostname_short
>  - port
>    
> These variables could be read from variable file encrpyted by **ansible vault** for *security*.
>
> In second way , it should not be forgatten that using extra variables before execution.
