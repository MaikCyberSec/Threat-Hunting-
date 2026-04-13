<p align="center">
  <img src="https://img.shields.io/badge/Platform-Microsoft%20Defender%20for%20Endpoint-0078D4?style=for-the-badge&logo=microsoft&logoColor=white" />
  <img src="https://img.shields.io/badge/Language-KQL-742774?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-E4002B?style=for-the-badge" />
</p>

<h1 align="center">Threat Hunting</h1>

<p align="center">
  <strong>Proactive threat detection using Kusto Query Language (KQL)</strong><br>
  Research-driven hunting queries for Microsoft Sentinel, Defender for Endpoint & Entra ID
</p>

<p align="center">
  <a href="#getting-started">Getting Started</a> &bull;
  <a href="#mitre-attck-coverage">ATT&CK Coverage</a> &bull;
  <a href="#contributing">Contributing</a>
</p>

---

## About

This repository contains production-ready KQL threat hunting queries designed for Security Operations Center (SOC) analysts, threat hunters, and detection engineers. Each detection targets a specific threat actor, campaign, or vulnerability — mapped to the MITRE ATT&CK framework and validated against real-world telemetry.

All queries are built for:

- **Microsoft Defender for Endpoint** (Advanced Hunting)
- **Microsoft Sentinel** (Log Analytics)
- **Microsoft Entra ID** (Sign-in & Audit Logs)

> **Philosophy**: Rather than waiting for alerts, these queries enable *proactive* hunting — identifying suspicious behaviors, uncovering hidden persistence mechanisms, and detecting attacker activity before it triggers automated detections.

---

### Recent Additions

**April 2026**
- `Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)` — Critical (CVSS 8.6) zero-day in Adobe Acrobat / Reader, patched April 11 via APSB26-43 (priority 1). Prototype Pollution enabling arbitrary code execution when a victim opens a crafted PDF. Disclosed by Haifei Li / EXPMON; active exploitation observed since December 2025. Exploit abuses `util.readFileIntoStream()` for local file exfiltration and `RSS.addFeed()` for C2. Known SHA256: `65dca34b04416f9a113f09718cbe51e11fd58e7287b7863e37f393ed4d25dde7`. 10 queries covering the known hash, vulnerable-version inventory (Acrobat DC ≤26.001.21367, Acrobat 2024 ≤24.001.30356), suspicious Acrobat children (LOLBins/shells), anomalous outbound from AcroRd32/Acrobat, sensitive local file reads (SSH/AWS/Azure/browser stores), PDF drops from mail/browser, obfuscated-JavaScript indicators, 5-minute PDF-open→beacon correlation, stage-2 executable drops, and full kill-chain correlation (deliver→open→bad signal in 1h).
- `EDR Killers - Ransomware BYOVD Defense Evasion` — ESET reports EDR killers have become a standard component of modern ransomware intrusions (EDRKillShifter, AvNeutralizer/AuKill, Terminator, RealBlindingEDR, GhostDriver/Poortry). Attackers drop legitimately-signed but vulnerable kernel drivers (rtcore64.sys, gdrv.sys, truesight.sys, dbutil_2_3.sys, mhyprot2.sys, procexp152.sys) and abuse their IOCTLs to kill protected security processes from kernel space. 10 queries covering vulnerable driver loads, driver drops to user-writable paths, kernel service creation, known EDR-killer binaries, Defender/EDR tampering, mass security-process termination, unsigned kernel loads, registry-based tampering, full kill-chain correlation, and a hash-free behavioural catch-all.
- `Cookie Theft & Session Hijacking` — With Google rolling out Device Bound Session Credentials (DBSC) in Chrome 146 to combat cookie theft, this detection hunts for the attacker side of session hijacking. Targets infostealers (Lumma, RedLine, Raccoon, Vidar, AMOS) reading browser credential stores, Local State encryption keys, DPAPI master keys, and cookie databases. 12 queries covering non-browser credential access, encryption key extraction, DPAPI abuse, staging/archiving, SQLite direct queries, multi-browser-vendor correlation, Entra ID token replay, impossible travel, C2 exfiltration, and full kill-chain correlation.
- `ClickFix macOS Script Editor` — New ClickFix social-engineering campaign bypassing Terminal entirely by abusing macOS Script Editor (osascript) to deliver Atomic Stealer (AMOS). Victims lured via fake CAPTCHAs and browser error dialogs. Steals Keychain credentials, browser passwords, cookies, crypto wallets, and session tokens. 12 queries covering browser-to-osascript execution, encoded payloads, curl-pipe-to-shell delivery, Keychain harvesting, browser credential theft, crypto wallet access, LaunchAgent persistence, Gatekeeper bypass, DMG execution chains, C2 exfiltration, and full kill-chain correlation.
- `Adobe Reader 0-Day PDF Exploit` — Active zero-day (CVE-2026-27220) discovered by EXPMON on March 26. Malicious PDF abuses JavaScript APIs (util.readFileIntoStream, RSS.addFeed) to steal local files and fingerprint systems. No user interaction beyond opening the PDF. C2: 169.40.2.68:45191. 12 queries covering C2 communication, vulnerable version detection, suspicious child processes, file read anomalies, and multi-signal correlation.
- `ChipSoft HiX EHR` — Following the April 7 ransomware attack on ChipSoft, the dominant Dutch healthcare EHR provider (~70% market share). Includes 9 queries covering software inventory, process detection, VPN connections, post-compromise indicators, and lateral movement. Z-CERT advisory: disconnect VPN immediately.
- `EvilTokens & AMOS Stealer` — Dual campaign: OAuth Device Code phishing hijacking enterprise M365 accounts (180+ phishing URLs/week), and AMOS macOS stealer targeting AI developers via ClickFix lures. 12 queries covering OAuth anomalies, C2 domains, behavioral patterns, persistence, credential harvesting, and multi-signal correlation.

**March 2026**
- `PureLog Stealer` — Copyright-themed lure campaign delivering fileless .NET stealer via renamed Python interpreter, AMSI bypass, and registry persistence.
- `MuddyWater` — Iranian APT targeting government and telecom sectors.
- `FoxKitsune` — Espionage-focused malware with sophisticated C2 communication.
- `Authentication Bypass` — API key abuse in Better Auth plugin.

---

## Getting Started

### Prerequisites

- Access to [Microsoft Defender Advanced Hunting](https://security.microsoft.com/v2/advanced-hunting) or Microsoft Sentinel
- Appropriate RBAC permissions (Security Reader or higher)
- Devices onboarded to Microsoft Defender for Endpoint

### Usage

1. **Clone** this repository
   ```bash
   git clone https://github.com/MaikCyberSec/Threat-Hunting-.git
   ```

2. **Open** Microsoft Defender Advanced Hunting or Sentinel Logs

3. **Copy** the relevant query into the query editor

4. **Adjust** the time range (`ago(7d)`, `ago(30d)`) based on your investigation scope

5. **Review** results and investigate findings

### Important Notes

> **Device Coverage**: KQL queries in Defender for Endpoint can only return results from **onboarded devices**. Unmanaged endpoints, BYOD, shadow IT, and devices without the Defender agent will not appear. Verify your onboarding coverage before concluding that a threat is absent.

> **Entra ID Queries**: Sign-in log queries (e.g., `AADSignInEventsBeta`) cover all authentication events regardless of device onboarding status, providing broader visibility for identity-based attacks.

> **Tuning**: Each detection includes false positive guidance in the file header. Tune thresholds and exclusions to your environment before deploying as scheduled rules.

---

## MITRE ATT&CK Coverage

```
                                    Threat Hunting — ATT&CK Heat Map
 ┌─────────────────────────────────────────────────────────────────────────────┐
 │ Initial Access     │ T1190  T1195  T1195.002  T1133  T1566.002  T1203      │
 │ Execution          │ T1059.001  T1059.002  T1059.004  T1059.007  T1204.002 │
 │ Persistence        │ T1547.001  T1547.011  T1543.001  T1078                │
 │ Privilege Esc.     │ T1078                                                 │
 │ Defense Evasion    │ T1027  T1027.013  T1036.005  T1497  T1562.001        │
 │ Discovery          │ T1082  T1518.001  T1005                               │
 │ Credential Access  │ T1528  T1539  T1550.001  T1555.001  T1555.003  T1185 │
 │ Collection         │ T1005  T1560.001                                      │
 │ Exfiltration       │ T1041                                                 │
 │ C2                 │ T1071.001  T1105                                      │
 │ Impact             │ T1486  T1489                                          │
 └─────────────────────────────────────────────────────────────────────────────┘
```

| Tactic | Techniques Covered | Detections |
|--------|-------------------|:----------:|
| Initial Access | Exploitation for Client Execution, Supply Chain, Phishing | 4 |
| Execution | PowerShell, AppleScript, Unix Shell, JavaScript, Malicious File | 6 |
| Persistence | Registry Run Keys, Plist Modification, LaunchAgent, Valid Accounts | 4 |
| Defense Evasion | Obfuscation, Masquerading, VM Detection, Disable Tools, Gatekeeper Bypass | 4 |
| Discovery | System Info Discovery, Software Discovery, Local Data | 2 |
| Credential Access | Steal OAuth Token, Steal Web Session Cookie, Keychain, Browser Credentials, Crypto Wallets, Man-in-the-Browser | 4 |
| Collection | Data from Local System, Archive Collected Data | 2 |
| Exfiltration | Exfiltration Over C2 Channel | 2 |
| Command & Control | Web Protocols, Ingress Tool Transfer | 5 |
| Impact | Data Encrypted for Impact (Ransomware) | 1 |

---

## Repository Structure

```
Threat-Hunting-/
├── README.md
├── Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)
├── EDR Killers - Ransomware BYOVD Defense Evasion (April 2026)
├── Cookie Theft & Session Hijacking - Infostealer Browser Credential Hunting (April 2026)
├── ClickFix macOS Script Editor - Atomic Stealer Delivery (April 2026)
├── Adobe Reader 0-Day PDF Exploit - Data Theft & System Fingerprinting (CVE-2026-27220)
├── ChipSoft HiX EHR - Supply Chain Ransomware Detection (CVE-2026-4404)
├── EvilTokens & AMOS Stealer - OAuth Hijacking & macOS Credential Theft (March 2026)
├── PureLog Stealer
├── FoxKitsune Malware Activity Detection
├── MuddyWater
└── Authentication Bypass via API Key Abuse (Better Auth Plugin)
```

Each detection file is self-contained with:
- Background context and threat intelligence summary
- MITRE ATT&CK technique mapping
- IOCs (domains, IPs, file hashes, file paths) where available
- KQL queries with inline documentation
- False positive guidance
- Source references

---

## Contributing

Contributions are welcome. To submit a new detection:

1. **Fork** this repository
2. **Create** a new file following the naming convention: `Threat Name - Brief Description`
3. **Include** in the file header:
   - Threat background and source intelligence
   - MITRE ATT&CK technique IDs
   - IOCs (if available)
   - False positive notes
   - References
4. **Submit** a pull request with a clear description

### Quality Standards

- Queries must be tested against Microsoft Defender Advanced Hunting
- Include comments explaining detection logic
- Map to at least one MITRE ATT&CK technique
- Document known false positives

---

## Disclaimer

These queries are provided for **defensive security and educational purposes only**. Always test queries in a non-production environment before deploying as scheduled detection rules. Results should be investigated by qualified analysts — query matches alone do not confirm compromise.

---

<p align="center">
  <strong>Maintained by <a href="https://github.com/MaikCyberSec">@MaikCyberSec</a></strong><br>
  <sub>SOC Analyst &bull; Threat Hunter &bull; Detection Engineer</sub>
</p>
