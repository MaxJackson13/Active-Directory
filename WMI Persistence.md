Persistence
-----------

WMI is especially effective for persistence with the idea of eventing. Common persistence methods like startup folders, registry run keys or sheduled tasks leave behind obvious, highly detectable artifacts.

WMI allows filters to be set that when triggered run scripts or executables. There are three components
1. Event filter ----> trigger condition in the form of a WQL query
2. Event consumer ----> action to take when trigger occurs
3. Filter-Consumer binding ----> links a filter to consumer

An event filter can be set to virtually anything- a failed login, a certain file access, a time of day etc.
An events consumer is the action taken once the trigger occurs- execute a script, run an executable etc.
The binding links a specific filter to a consumer

Imagine a filter that is a failed login and an action that executes a batch file containing an encoded reverse shell. Then all an attacker needs to do is fail to login, then they get a reverse shell (as system no less because WMI subscription execution is proxied by the WMI Provider Host process (WmiPrvSe.exe) which runs as system)

Detection 
---------
1. Monitor executed commands and arguments that can be used for WMI persistence such as `Register-WMIEvent` in powershell
2. Monitor newly executed processes resulting from the execution of subscriptions e.g. unusual processes spawned from WIMPrivSe.exe
3. Monitor for the creation of new `EventFilter`, `EventConsumer` and `FiltertoCOnsumerBindings`. These are sysmon event IDs 19, 20 and 21 respectively. Also on Windows 10 and above event ID 5861 in the Microsoft-Windows-WMI-Activity/Operational.evtx log alerts on a WMI subscription creation.

Mitigations
------------
1. Enable attack surface reduction on Windows 10
2. Prevent credential overlap of privileged accounts between systems
3. Restrict users who can connect remotely using WMI
