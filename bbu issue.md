**CLOUD HOST MAINTENANCE TICKET -RUNBOOK**

This runbook will help you handle issues related to the host maintenance common incidents.

This section regards the bbu issues.

2) BBU ISSUES

a)  Connect to the host using ssh connection using the root user
b)  check the disk/volume with the command: 

```
/opt/MegaRAID/storcli/storcli64 /c0 /cv show status
```
	

c) copy and paiste the output of this command into the ims ticket 

d) If the end  output of this command contains something like this: 

```

Cachevault_Info :
===============

-----------------------------------
Model State Temp Mode MfgDate
-----------------------------------
CVPM02 Failed 22C - 2018/05/19
-----------------------------------
```

with the CVPM02 failed therefore there is the need to substitute the bbu

Procedure: 

a) schedule an host evacuation

b) Check hardware age, and if it is more than 6 years old please reclaim the host after the scheduled evacuation

c) If the host is less than 6 years old, once the host has bee evacuated send the host to the hardware team 
d) once the bbu has been fixed and the tickat has been reassinged to you , 
e) Connect to the host using ssh connection using the root user

f) check the disk/volume with the command: 

/opt/MegaRAID/storcli/storcli64 /c0 show all

if the end of output is something like this 

```

Cachevault_Info :
===============

-----------------------------------
Model State Temp Mode MfgDate
-----------------------------------
CVPM02 Failed 30C - 2014/09/18
----------------------------------- 
-----------------------------------
```


disable the resource allocation from the IMS portal and schedule an host evacuation. 


g) once the evacuation has been approved (normally it would take 15 days) check if the host has been evacuated successfully

h) If the host has been evacuated successfully and there are no VSI on it, send the ticket to the hw team for BBU substitution and tell the hw team to send back the ticket to you.

i) once the BBU has been substituted you should received back the ticket.

at this point connect to the host with a root user and run the following command: 


```
/opt/MegaRAID/storcli/storcli64 /c0 /cv show status
```
	

if the end of output is something like this 


```
Cachevault_Info :
===============

------------------------------------
Model State Temp Mode MfgDate
------------------------------------
CVPM02 Optimal 30C - 2015/08/25
------------------------------------ 
```

it means that everything is ok and you may proceed to reload the host 

before closing the ticket.

Usually the reload takes about 5-6 hours to complete, if it takes more than that, either you can start some [troubleshooting](https://ibm-cloudplatform.slack.com/archives/C01HDJC3J7K/p1621770397007400) or send the ticket to DC to check it and once finished ask them to send back to cloud maintenance queue under your name.
Please As we're a low in capacity in most of the regions we need to push a bit and engage with DC to help us out when the reload get stuck. Don't leave the reload process hanging for days/weeks, ask for help if you need, please.
