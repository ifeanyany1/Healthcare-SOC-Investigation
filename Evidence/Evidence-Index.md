# Screenshot Evidence Index

| Screenshot | What it shows | Caution |
|---|---|---|
| [01 — Lab connectivity and Sysmon](../Screenshots/01-Lab-Connectivity-and-Sysmon.png) | Initial lab checks | Supporting setup evidence only |
| [02 — Sysmon logging](../Screenshots/02-Sysmon-Logging-Validation.png) | Sysmon telemetry available | Supporting setup evidence |
| [03 — Sysmon process creation](../Screenshots/03-Sysmon-Process-Creation.png) | Example Sysmon Event 1 | Not direct evidence of the later file read |
| [04 — Additional Sysmon evidence](../Screenshots/04-Additional-Sysmon-Process-Evidence.png) | Additional process-creation view | Supporting telemetry |
| [05 — Windows 4624](../Screenshots/05-Windows-Event-4624-Analysis.png) | Successful-logon events present | Not correlated to the file-read logon ID |
| [07 — Claims dataset](../Screenshots/07-Healthcare-Claims-Dataset.png) | 104 synthetic claims verified | Original CSV not included in uploaded ZIP |
| [08 — Audit policy](../Screenshots/08-Healthcare-File-Auditing-Enabled.png) | File System audit policy set | Policy alone is not a file-specific SACL |
| [09 — File audit entry](../Screenshots/09-Healthcare-Claims-File-Auditing.png) | Everyone / Success / read-write-execute audit | This is auditing, not access permission |
| [10 — Test account](../Screenshots/10-Healthcare-Test-Employee-Account.png) | `claims.employee` exists and is enabled | No successful session demonstrated |
| [11 — Event 4663](../Screenshots/11-Healthcare-Claims-Access-4663.png) | Audit Success, claims file, account and timestamp | Strongest screenshot of the file access |
| [12 — Event 4663 details](../Screenshots/12-Healthcare-File-Access-Details.png) | Top of the 4663 General view | Process and access fields are **not visible**; see [transcribed event](Investigation-Notes.md) |

**Publication privacy:** Screenshots contain a local Windows username, computer name, and paths. Review before pushing to a public GitHub repository. No real patient records should be uploaded.
