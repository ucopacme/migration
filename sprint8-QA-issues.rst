
QA issues found 
===============

- opsmond01.ucop.edu is not working on the AWS environment
     - Reason: Current hostnames and IPs are hardcoded on the application, so it is still resolving to S

- User should be able to access the GUI for the infrastructure manager(opsmond03) - Previously 128.48.76.166/adminconsoleapp/ should now be changed to 10.48.76.166/adminconsoleapp/  

- No of probes on the opsmond03 will not exceed 30, however, there are probes on the other servers and the best port number range to be defined in security groups is 48000-48050. It is not going to exceed this. 

- On the infrastructure manager, the IP address field on the all the probes is showing as 128.48.76/164/5/6for opsmondo1, opsmond02, opsmond03 servers respectively. This should be showing as 10.48.76/164/5/6 respectively. Probable reason: Hostnames and IPs are hardcoded on the application. 

