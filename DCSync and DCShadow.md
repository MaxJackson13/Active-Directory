DCSync
------

Many ADs have multiple domain controllers for availability reasons. Each DC has a copy of the AD database and changes to the database on any one DC needs to be replicated across all domain controllers. DCSync is an attack native to Mimikatz as Benjamin Delpy- the creator of Mimikatz- pioneered this attack. The way DCSync works is the computer launching the attack temporarily impersonates a DC and requests user replication from another DC. The DC returns the data including password hashes of all domain-joined accounts. This has the same effect of copying NTDS.dit. From here the attacker may use the krbtgt hash to forge a golden ticket for example.

The necessary privileges are
1. Replicating Directory Changes
2. Replicating Directory Changes All
3. Replicating Directory Changes in Filtered Set

These rights are typically afforded to administrators, domain admins and enterprise admins

From mimikatz in an elevated session run `lsadump::dcsync`. Historic password hashes are also returned.
