**CLOUD HOST MAINTENANCE TICKET -DISK REPLACEMENT ISSUE RUNBOOK**

This runbook will help you handle issues related to the host maintenance common incidents such aS DISK REPLACEMENT ISSUES


1) DISK REPLACEMENT ISSUES 

For disk replacement issues: 

a)  Connect to the host using ssh connection using the root user
b) Verify the arrays with the following command: 

/opt/MegaRAID/storcli/storcli64 /c0 /vall show


if the output will show  STATE DGRD (in the colum state) it means that the volume is degraded and the issue must be addressed: 

```
/opt/MegaRAID/storcli/storcli64 /c0 /vall show
Controller = 0
Status = Success
Description = None


Virtual Drives :
==============

------------------------------------------------------------------
DG/VD TYPE   State Access Consist Cache Cac sCC     Size Name     
------------------------------------------------------------------
0/0   RAID10 Dgrd  RW     Yes     NRWBD -   ON   60.0 GB RAID10-A 
0/1   RAID10 Dgrd  RW     Yes     NRWBD -   ON  1.687 TB RAID10-B 
------------------------------------------------------------------

Cac=CacheCade|Rec=Recovery|OfLn=OffLine|Pdgd=Partially Degraded|dgrd=Degraded
Optl=Optimal|RO=Read Only|RW=Read Write|B=Blocked|Consist=Consistent|
R=Read Ahead Always|NR=No Read Ahead|WB=WriteBack|
AWB=Always WriteBack|WT=WriteThrough|C=Cached IO|D=Direct IO|sCC=Scheduled
Check Consistency
```


	

c) copy and paiste the output of this command into the ims ticket 

d) As next step run the following command in order to verify the  drive states :

/opt/MegaRAID/storcli/storcli64 /c0 /eall /sall show	

 If the output of this command contains will show a drive marked as BAD/UBAD: 

```

 /opt/MegaRAID/storcli/storcli64 /c0 /eall /sall show
Controller = 0
Status = Success
Description = Show Drive Information Succeeded.


Drive Information :
=================

----------------------------------------------------------------------------------------------
EID:Slt DID State DG      Size Intf Med SED PI SeSz Model                                  Sp 
----------------------------------------------------------------------------------------------
252:0     9 Onln   0 893.75 GB SATA SSD Y   N  512B Micron_5200_MTFDDAK960TDN              U  
252:1     8 Onln   0 893.75 GB SATA SSD Y   N  512B Micron_5200_MTFDDAK960TDN              U  
252:2    11 Onln   0 893.75 GB SATA SSD Y   N  512B Micron_5200_MTFDDAK960TDN              U  
252:3    10 UBad   - 893.75 GB SATA SSD Y   N  512B      -      0n        t     p        - U  
----------------------------------------------------------------------------------------------

EID-Enclosure Device ID|Slt-Slot No.|DID-Device ID|DG-DriveGroup
DHS-Dedicated Hot Spare|UGood-Unconfigured Good|GHS-Global Hotspare
UBad-Unconfigured Bad|Onln-Online|Offln-Offline|Intf-Interface
Med-Media Type|SED-Self Encryptive Drive|PI-Protection Info
SeSz-Sector Size|Sp-Spun|U-Up|D-Down|T-Transition|F-Foreign
UGUnsp-Unsupported|UGShld-UnConfigured shielded|HSPShld-Hotspare shielded
CFShld-Configured shielded


```

it means that one disk is faulty.

In this example is the disk n.3 (hdd3): 

```
252:3    10 UBad   - 893.75 GB SATA SSD Y   N  512B      -      0n        t     p        - U  
```


 e) Next step please check drive order, serial numbers, media errors, and smart errors :
 
 ```
 /opt/MegaRAID/storcli/storcli64 /c0 /eall /sall show all | grep -P '(Det|Cou|S\.M|^SN)(?!.*\s(No|0)$)'
Drive /c0/e252/s0 - Detailed Information :
SN = 18331E0F6CEA
Drive /c0/e252/s1 - Detailed Information :
SN = 18331E10CCC3
Drive /c0/e252/s2 - Detailed Information :
SN = 18331E10D413
Drive /c0/e252/s3 - Detailed Information :
SN =         18331E10E840
```

By checking the serial numbers agains the IMS data of the drives, you will be able to confirm that faulty drive. 
(in this case it will be the number 3) 

In order to do this ,please go to the ims portal, 
from the ticket related to the case select view: 



then edit: 



then select hardware and then select the tab properties related to the faulty drive. In this case hdd3. You will be able to see the serial number of the drive and compare it with the one found it previously in this step.



 
g) once you have confirmed the faulty drivem please send the ticket to the Hardware team in order to get the disk replaced, with the following message: 

```
Hello DC,

Please hotswap the failed drive noted in the output above (HDDX). Please keep in mind that this is an active host with customer VSIs onboard. Once the hostswap is complete please send this ticket back to the Compute Cloud Host Maintenance queue in my name. 

Thank you,
YourNameHere
```

h) once the hW team replace the disk you should receive back the ticket in your queue.

At this point, please, gconnect to the host using ssh connection using the root user

run the following command: 

/opt/MegaRAID/storcli/storcli64 /c0 /eall /sall show rebuild

if the output message is successfull: 

```
Controller = 0  
Status = Success  
Description = Show Drive Rebuild Status Succeeded.  
  
  
----------------------------------------------------------  
Drive-ID Progress% Status Estimated Time Left  
----------------------------------------------------------  
/c0/e252/s0 - Not in progress -  
/c0/e252/s1 - Not in progress -  
/c0/e252/s2 - Not in progress -  
/c0/e252/s3 - Not in progress -  
----------------------------------------------------------
```

f) if the rebuild status is succeeded , reinable the resource allocation and close the ticket 

g) before closing the ticket please run the following command, 

in order to check cache vault status (BBU): 


/opt/MegaRAID/storcli/storcli64 /c0 /cv show status	


 If in this error message at the end there is something like this: 


```
Cachevault_Info :
===============

-----------------------------------
Model State Temp Mode MfgDate
-----------------------------------
CVPM02 Failed 22C - 2018/05/19
-------
```
Plese go to the section BBU Replacemtent of this runbook. 

If there will no error showed you may close this ticket as solved. 

