Content Pack for Windows Dashboards and Reports  
config explorer app  

check port 8089 on tcpdump  
make sure 8089 is open  
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
