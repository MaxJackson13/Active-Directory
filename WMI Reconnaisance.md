Reconnaisance
-------------

For default configurations of the event log and auditing processes, WMIC requests won’t be logged, which makes WMI attractive to attackers.

`wmic useraccount list full`

Get a full list of local useraccounts

`wmic group list full`

Get a full list of local group accounts

`wmic netuse list full` 

Get a full list of mapped drives

`wmic process get Description,ExecuteablePath,ProcessID`

Get a list of running processes returning description, pid and path to the executable that spawned the process

`wmic qfe get HotFixID,Description,Caption`

Get a list of patches added to the local system' (qfe stands for quick fix engineering)

These commands can be paired with the '/node:REMOTE-IP' flag and admin credentials to execute any of the commands on remote systems. There is practically no information that cannot be retrieved through WMI; it's a database of system information.

Through Powershell
------------------

(Less preferred due to the increased logging capabilities of powershell)

Equally, in powershell the 'Get-WmiObject' cmdlet gets instances of WMI classes or information about the available WMI classes.
For example 

`$Service = Get-WMIObject -Class Win32_Service -ComputerName Workstation01 -Filter "name='WinRM'"`

`$Service.StopService` 

(terminates the service on the remote host provided the current user is a local admin on the remote machine)

`$Service | fl *` 

(shows all WinRM service information)

`Get-WMIObject -Class` has been updated with the more performant but largely equivalent `Get-CIMInstance -ClassName`
