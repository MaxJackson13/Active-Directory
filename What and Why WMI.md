What
----

1. WMI (Windows Management Instrumentation) provides a powerful infrastructure for local and remote system management
2. WMI is Microsoft's implementation of the Web-Based Enterprise Management (WBEM) and Common Information Model (CIM) standards (though 'Web-Based' is perhaps a misnomer as WBEM implementations invoke some form of GUI, BUI or CLI yet the standard has nothing to say about this interface)
3. Can be used to:
                - Obtain system information (local and remote)
                        - Registry
                        - File system
                        - etc.
                - Execute commands (local and remote)
                - Subscribe to events

Why
---

'Imagine a technology that is built into every Windows operating system going back to Windows 95, runs as system, executes arbitrary code, persists across reboots and does not drop a single file to disk. Such a thing does exits and it's called WMI'- Matt Graeber (Blackhat 2015)

Let's break this down

1. WMI has been around since 1993, available as downloads for Windows NT, 95 and 98, and comes preinstalled on Windows 2000 and later
2. WMI remote code execution requires elevated privileges and runs as system
3. Persistent WMI objects (event filters and consumers) are stored in %SystemRoot%\System32\wbem\Repository\OBJECTS.DATA on disk so persists until deletion or corruption. 
4. Apart from the aforementioned objects which AV doesn't touch, there are no persistent artifacts regarding code execution via WMI. For example, PsExec drops an executable on the remote target then creates and starts a service to trigger the executable whereas WMI will not drop any files to disk or create any services.
5. WMI Logging is disabled by default and is very noisy when enabled so is often left disabled

You can check the logging WMI logging status with the command

`(Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Wbem\CIMOM).Logging`

The values are:
```
0 – No Logging/Disabled
1 – Log Errors Only
2 – Verbose Logging
```
Logs can be found in `%systemroot%\system32\wbem\Logs\`
