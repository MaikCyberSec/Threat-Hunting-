# MuddyWater (MENA) Phishing-to-Backdoor Campaign — Detection Notes + KQL

## Overview
This repo documents detection guidance for a targeted campaign attributed to **MuddyWater** impacting **MENA organizations**. The intrusion chain is commonly initiated via **spear-phishing** using **malicious Office documents**, followed by staged payload delivery and persistence.

The activity is consistent with **espionage-focused** operations: initial access through user execution, multi-stage tooling, stealthy persistence, and long-term remote control.

---

## Attack Summary (High Level)
**Initial Access**
- Spear-phishing email delivers Office attachment
- User interaction triggers macro/embedded execution

**Execution & Staging**
- Office spawns suspicious child processes (LOLBins / script engines)
- Payloads may be staged from cloud-hosted infrastructure
- Drops may occur in sensitive system paths (e.g., SysWOW64)

**Persistence**
- Unauthorized Windows service creation
- Potential scheduled task and registry-based persistence patterns

**Defense Evasion**
- Attempts to add Microsoft Defender exclusions
- Tampering with endpoint security configurations

---

## Who’s At Risk
- Government and public sector entities
- Telecom, energy, maritime, finance, and critical infrastructure
- Any organization with:
  - Macro-enabled Office workflows
  - Limited email filtering / phishing controls
  - Incomplete Defender onboarding or weak endpoint telemetry coverage

---

## Defensive Guidance (Quick Actions)
- Disable/limit Office macros (allow only signed macros where required)
- Block Office files from the internet and enforce Mark-of-the-Web protections
- Harden email security and quarantine macro-enabled attachments by default
- Audit and alert on:
  - Office spawning script engines and LOLBins
  - New/modified Windows services
  - Defender exclusions and preference changes
- Ensure all endpoints are onboarded to Microsoft Defender to avoid telemetry gaps

---

## Detection: Office Spawning Suspicious Children (KQL)
**Purpose:** Identify suspicious process creation where Office apps spawn common LOLBins or scripting engines often used in phishing-led malware staging.

```kql
let Lookback = 14d;
let OfficeParents = dynamic(["winword.exe","excel.exe","powerpnt.exe","outlook.exe"]);
let SuspChildren = dynamic([
  "powershell.exe","pwsh.exe","cmd.exe","wscript.exe","cscript.exe",
  "mshta.exe","rundll32.exe","regsvr32.exe","bitsadmin.exe","certutil.exe","curl.exe","wget.exe"
]);
DeviceProcessEvents
| where Timestamp >= ago(Lookback)
| where tolower(InitiatingProcessFileName) in (OfficeParents)
| where tolower(FileName) in (SuspChildren)
| project Timestamp, DeviceName, AccountName,
          InitiatingProcessFileName, InitiatingProcessCommandLine,
          FileName, ProcessCommandLine,
          InitiatingProcessParentFileName, ReportId
| order by Timestamp desc



