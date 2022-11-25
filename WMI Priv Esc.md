Unquoted service paths
----------------------

`wmic service get name,displayname,pathname,startmode |findstr /i "auto" |findstr /i /v "c:\windows\\" |findstr /i /v """`

This command finds any services where the binary executable path contains spaces and isn't wrapped in quotes and has startmode 'auto'. Consider the service with binary path:

`C:\Program Files\This is\a fake\service path\vulnerable.exe`

Then we if we can write into any of the intermediate folders (C:\Program Files, C:\Program Files\This is, C:\Program Files\This is\a fake, ...) 
we can drop a malicious executable into the writable folder with the appropriate name and upon restarting the service our executable will be loaded before vulnerable.exe.

Says we run the command in powershell `Get-ACL -Path 'C:\Program Files\This is'` or from cmd.exe `icacls 'C:\Program Files\This is'`
and everyone has full control. 
We may then drop an executable named This.exe into the folder 'C:\Program Files\This is' and upon restarting the service, the system will search for C:\Program.exe and realise this doesn't exist, then it will try and load c:\Program Files\This.exe, which does exist, so our executable will be launched under the security context of the account running the service (which is often NT AUTHORITY/SYSTEM)

Kernel Exploits
---------------

`wmic qfe get HotFixID,Description,Caption`

This allows an attacker to enumerate missing patches on the system so they can leverage kernel level exploits to elevate privileges e.g. MS16-032

Pass the Ticket
---------------

Traditionally pass the ticket attack requires mimikatz which may trigger AV/EDR and certainly leave behind IOCs e.g. Sysmon would trigger with 

```
Event 10 (Process Access)
...[snip]...
SourceImage: C:\Users\Administrator\Downloads\x64\mimikatz.exe  
TargetImage C:\windows\system32\lsass.exe
...[snip]...
```

however pass the ticket can be achieved natively using WMI via the command

`wmic /authority:"kerberos:DOMAIN\TARGET" /node:10.10.10.10 process call create "whatever process + arguments"`
