# SOC Investigation Report — Synthetic Healthcare Claims File Access

**Case ID:** HC-SOC-2026-001  
**Case type:** File-access monitoring / SOC triage exercise  
**Lab:** Windows 11 FLARE-VM, Oracle VirtualBox  
**Evidence date:** November 8, 2026 (VM local event timestamps)  
**Disposition:** **Benign / authorized controlled test**  
**Confidence:** High that the reviewed 4663 event represents the controlled file read; limited assessment of unrelated activity.

## 1. Executive summary

A controlled read of a synthetic healthcare claims CSV was performed to test Windows file-access auditing. Windows Security Event ID 4663 recorded `ReadData` access to the monitored file by `powershell.exe` under the analyst's existing local Windows account. The file-access process identifier was `0x198c` (6540 decimal). Sysmon process-creation events for `runas.exe` and `whoami.exe` identified PowerShell PID 6540 as their parent and shared the Windows logon ID `0xE42E4` with the file-access event. The observed activity is consistent with the documented authorized lab test. No evidence reviewed confirms unauthorized access, exfiltration, or compromise.

## 2. Scope and environment

- Windows 11 FLARE-VM in VirtualBox, with Sysmon installed and running.
- Monitored file: `C:\Healthcare-SOC-Lab\Patient_Claims.csv`.
- Dataset: 104 fictional claims with synthetic claim and patient identifiers, claim types, amounts, and statuses.
- Logging: Windows Security log and Sysmon Operational log.
- Collection: Event Viewer screenshots, PowerShell validation, and manually transcribed event details.
- Out of scope: Real patient data, production systems, HIPAA compliance validation, actual attacker attribution.

## 3. Objective and hypothesis

**Question:** Who accessed the claims file, what operation occurred, and is the observed access suspicious?  
**Initial scenario:** A fictional healthcare organization is concerned about unusual claims-file access.  
**Test method:** Intentionally read the monitored file under an authorized lab account, then validate the resulting telemetry.

## 4. Evidence and timeline

| VM local timestamp | Source | Event / observation | Relevance |
|---|---|---|---|
| Nov 8, 2026, 10:32:46 PM | Sysmon ID 1 | `runas.exe` launched; parent `powershell.exe` PID 6540 | Attempt to launch fictional employee account |
| Nov 8, 2026, 10:33:11 PM | Sysmon ID 1 | `whoami.exe` launched; parent `powershell.exe` PID 6540 | Identity check returned existing account |
| Nov 8, 2026, 10:33–10:34 PM | PowerShell console | `Get-Content ... | Out-Null` executed without error | Authorized controlled file read |
| Nov 8, 2026, 10:34:47 PM | Security ID 4663 | `ReadData` for `Patient_Claims.csv` by `powershell.exe`, PID `0x198c` | Confirms audited file read |

*Times are reported as displayed by the VM. Sysmon's UTC fields and displayed local timestamps were not independently reconciled; the timeline uses the displayed local event times.*

## 5. Detailed Windows Security evidence

**Event:** 4663 — An attempt was made to access an object  
**Account:** `IFY1\ifean`  
**Logon ID:** `0xE42E4`  
**Object type:** File  
**Object name:** `C:\Healthcare-SOC-Lab\Patient_Claims.csv`  
**Process:** `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe`  
**Process ID:** `0x198c` (6540 decimal)  
**Access:** `ReadData (or ListDirectory)`  
**Access mask:** `0x1`  
**Audit keyword:** Audit Success

**Assessment:** The access mask and event text support a file read. Event 4663 is an audit record, not by itself an alert or evidence of malicious intent.

## 6. Sysmon correlation

The supplied Sysmon output shows two relevant process-creation events:

- `runas.exe`, Nov 8 at 10:32:46 PM, parent image `powershell.exe`, parent PID 6540, logon ID `0xE42E4`.
- `whoami.exe`, Nov 8 at 10:33:11 PM, parent image `powershell.exe`, parent PID 6540, logon ID `0xE42E4`.

`0x198c` (hexadecimal) equals `6540` (decimal), and the shared logon ID strengthens the linkage. The Sysmon output establishes child-process activity associated with that PowerShell instance; it **does not directly show the original PowerShell process-creation event**.

The `runas` attempts ended with `Unable to acquire user password`, and the identity check returned the original Windows account. The fictional `claims.employee` account was created but did not perform the confirmed file read.

## 7. Analysis and disposition

**Observed facts:** A PowerShell process under the existing Windows account successfully read the synthetic claims file, and the audit event was recorded. Related Sysmon events provide process-context correlation.

**Not observed or not verified:** Successful login as `claims.employee`; unauthorized access; exfiltration; malware; suspicious persistence; or a confirmed incident. A saved 4624 screenshot was not correlated to the relevant logon ID, so it is not used as evidence of a specific session.

**Disposition:** Benign authorized lab test. No incident escalation indicated from the reviewed evidence alone.

## 8. Recommendations

1. Restrict sensitive claims files by least-privilege access controls and periodic access review.
2. Audit sensitive file reads and writes selectively to balance visibility and log volume.
3. Collect Windows Security and Sysmon telemetry centrally, with protected retention.
4. Correlate account, host, process, logon ID, file path, and timestamp in triage workflows.
5. Build baselines and alerts for unusual bulk reads, off-hours activity, and unexpected tools before labeling activity suspicious.
6. In production, use organization-specific healthcare security and privacy policies and assess applicable regulatory obligations separately.

## 9. Limitations and evidence handling

- Fictional dataset and single-host controlled test only.
- The original CSV was not present in the uploaded evidence archive.
- Screenshot 12 shows the top of the 4663 General view; the lower process/access fields were provided as a separate textual event transcription.
- Sysmon and Windows displayed timestamps were not independently normalized to UTC.
- No MITRE ATT&CK mapping is claimed without an adversarial action.
- Before public posting, review screenshots for personal usernames, hostnames, and paths.

## 10. Supporting evidence

See [`../Evidence/Evidence-Index.md`](../Evidence/Evidence-Index.md) and [`../Evidence/Investigation-Notes.md`](../Evidence/Investigation-Notes.md). The original screenshots are preserved in `../Screenshots/`.
