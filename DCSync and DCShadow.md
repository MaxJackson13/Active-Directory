DCSync
------

Many ADs have multiple domain controllers for availability reasons. Each DC has a copy of the AD database and changes to the database on any one DC needs to be replicated across all domain controllers. DCSync is an attack native to Mimikatz as Benjamin Delpy- the creator of Mimikatz- pioneered this attack. The way DCSync works is the computer launching the attack temporarily impersonates a DC and requests user replication from another DC. The DC returns the data including password hashes of all domain-joined accounts. This has the same effect of copying NTDS.dit. From here the attacker may use the __krbtgt__ hash to forge a golden ticket for example.

The necessary privileges are
1. Replicating Directory Changes
2. Replicating Directory Changes All
3. Replicating Directory Changes in Filtered Set

These rights are typically afforded to administrators, domain admins and enterprise admins

From mimikatz in an elevated session run `lsadump::dcsync`. Historic password hashes are also returned.

DCShadow
--------
DCShadows appears similar to DCSync put they're almost the reverse of eachother. While DCSync makes a request to get data from a DC, DCShadow temporarily registers the current workstation as a DC, then triggers a change to be copied across to the other domain controllers via active directory replication.

From mimikatz run

`lsadump::dcshadow /object:'object' /attribute:'attribute' /value:'value'`
- object: the distinguished name of the AD object to modify
- attribute: the name of the AD schema attribute to modify
- value: the objecteds new value

Then run
`lsadump::dcshadow /push` 
to push the changes

for example
`lsadump::dcshadow /object:'CN=Backdoor,CN=Users,DC=scrm,DC=local' /attribute:primaryGroupID /value:512`

adds the user `scrm.local\backdoor` to the domain admins group (RID 512)


Detection
---------

DCShadow is especially stealthy as while events such as a user being added to the domain admin group would trigger an event ID 4728 (user added to security enabled global group), the logging would occur on the workstation from which the change originates. But in this case the workstation is the attacker's machine which immediately de-registers itself as a DC after the change is pushed to the other domain controllers, so no event logs are generated as the DC does not exist. One way to detect this is to inspect the logs of the legitimate domain controllers to verify if the change originated from any of them, and if not it may be a case of DCShadow.  

To detect DCSync, one way highlighted by Didier Stevens is to inspect network traffic between a **non-DC** and a **DC** for DCERPC (distributed computing environment remote procedure call) packets requesting to use DRSUAPI (directory replication services API), followed by a DRSUAPI call for the IDL_DRSGetNCChanges method which is opnum 3. This can be achieved by creating 2 IDS rules. One which flags on a tcp packet requesting a bind to the DRSUAPI interface. This packet will contains bytes corresponding to a tcp bind connection: `05 00 0b` as well as bytes matching the uuid of the DRSUAPI interface which can be found by inspecting the packets in wireshark. The second flags on the bytes `05 00 00` which is a tcp bind, as well as on the bytes `03 00` which is the opnum of the DSGetNCchanges function. The key is to monitor between non-DCs and DCs as these rules will flag on legitimate AD replication traffic between two domain controllers. 



Here's a great resource on DCSync and DCShadow
<a href="https://www.youtube.com/watch?v=SOr_G8oOstc&t=3037s">Detecting DCSync and DCShadow Network Traffic
</a>
