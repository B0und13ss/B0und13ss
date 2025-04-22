Content Pack for Windows Dashboards and Reports  
config explorer app  

check port 8089 on tcpdump  
make sure 8089 is open  
-----UFS ARE LIKELY FAILING TO CONNECT
Might have to remove the local deploy server file on the deployment client and re-init using the cli  

                            set deploy-poll <ip>:8089  
then splunk restart  

```
✅ 5. Time Skew or SSL Issues
Problem: If there's a significant time difference between UFs and DS, or if the DS is expecting SSL and the UF isn’t configured for it (or vice versa), the handshake will fail.

Check:

Make sure NTP is enabled and time is synchronized.

Check logs for SSL errors.
```
-------------------------------------
```
On the UF:

/opt/splunkforwarder/var/log/splunk/splunkd.log

Look for entries related to DeploymentClient, e.g.:

pgsql
Copy
Edit
WARN  DeploymentClient - Unable to connect to deployment server
On the DS:

/opt/splunk/var/log/splunk/splunkd.log

Look for connections from the UFs.

Unable to connect to deployment server

Handshake failed

Server did not respond

ERROR DeploymentClient - Server returned a non-200 response code
```
-----------------------------------------------
```
7. Debug connection from UF side (curl test)
You can test if the UF can talk to the DS by running this from the UF:

bash
Copy
Edit
curl -k https://<deployment_server_ip>:8089
You should get back something like:

xml
Copy
Edit
<response>
   <message type="FATAL">Method not allowed.</message>
</response>
That means Splunk is at least responding.
```
```
💡 Key Point: Windows + DeploymentClient + Admin Mode
✅ Yes — file permissions can still matter, even on Windows and even when run as Administrator.
Here’s why:

Splunk runs as a service, usually under the Local System account.

Editing files as Administrator doesn't necessarily mean the Splunk service can read them.

If the config file was created or edited by another user, it might be unreadable to the Splunk service.

🔍 Action: Check File Permissions on deploymentclient.conf
1. Navigate to the config file:

perl
Copy
Edit
C:\Program Files\SplunkUniversalForwarder\etc\system\local\deploymentclient.conf
2. Right-click > Properties > Security Tab

Make sure SYSTEM has read access.

If not, click Edit, select SYSTEM, and allow Read access.

Alternatively, run PowerShell as admin:

powershell
Copy
Edit
icacls "C:\Program Files\SplunkUniversalForwarder\etc\system\local\deploymentclient.conf"
You should see SYSTEM with at least R (read) permissions.
```
```
✅ Double Check the DS URI Formatting
Sometimes the URI gets messed up due to encoding or copy-paste weirdness.

Check in your config that this line is exactly like this:

ini
Copy
Edit
targetUri = 192.168.1.100:8089
Make sure there are:

No smart quotes (“ or ”)

No trailing spaces

No http/https — just IP/hostname and port
```
```
✅ Best Practice
Stick to:

ini
Copy
Edit
targetUri = 192.168.1.100:8089
Not:

ini
Copy
Edit
targetUri = https://192.168.1.100:8089 ❌
```
```
✅ Example Output:
ini
Copy
Edit
# showing: C:\Program Files\SplunkUniversalForwarder\etc\system\local\deploymentclient.conf
[deployment-client]
clientName = myHostName

[target-broker:deploymentServer]
targetUri = 192.168.1.100:8089
```
