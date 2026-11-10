# Investigation Notes — Event Details Supplied During Lab

These are selected **transcribed** event details provided during the investigation, not an exported raw `.evtx` log. They support the report where a screenshot did not show every field.

## Windows Security — Event 4663

```text
Event time (VM local): 11/08/2026 10:34:47 PM
Subject Security ID: IFY1\ifean
Account Name: ifean
Account Domain: IFY1
Logon ID: 0xE42E4
Object Type: File
Object Name: C:\Healthcare-SOC-Lab\Patient_Claims.csv
Process ID: 0x198c
Process Name: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Accesses: ReadData (or ListDirectory)
Access Mask: 0x1
```

## Sysmon — Event ID 1 (selected records)

```text
11/08/2026 10:32:46 PM | Image: C:\Windows\System32\runas.exe
CommandLine: runas.exe /user:.\claims.employee cmd.exe
ParentImage: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
ParentProcessId: 6540
LogonId: 0xE42E4

11/08/2026 10:33:11 PM | Image: C:\Windows\System32\whoami.exe
ParentImage: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
ParentProcessId: 6540
LogonId: 0xE42E4
```

**Process-ID check:** `int('198c', 16) = 6540`. The two Sysmon records are **child process creations**, not the PowerShell parent creation event itself.

## Analyst test action

```powershell
Get-Content "C:\Healthcare-SOC-Lab\Patient_Claims.csv" | Out-Null
```

The command completed without error in the PowerShell session. The observed Event 4663 is consistent with that controlled read.
