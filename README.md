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
  <a href="#detections">Detections</a> &bull;
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

## Detections

| # | Detection | Threat Type | Platform | MITRE ATT&CK | Queries |
|:-:|-----------|------------|----------|---------------|:-------:|
| 1 | **[ChipSoft HiX EHR — Supply Chain Ransomware](ChipSoft%20HiX%20EHR%20-%20Supply%20Chain%20Ransomware%20Detection%20(CVE-2026-4404))** | Supply Chain / Ransomware | Windows | T1195, T1133, T1486 | 9 |
| 2 | **[EvilTokens & AMOS Stealer — OAuth Hijacking & macOS Theft](EvilTokens%20%26%20AMOS%20Stealer%20-%20OAuth%20Hijacking%20%26%20macOS%20Credential%20Theft%20(March%202026))** | Credential Theft / Phishing | Windows, macOS, Entra ID | T1528, T1566, T1555 | 12 |
| 3 | **[PureLog Stealer](PureLog%20Stealer)** | Infostealer / Fileless | Windows | T1566, T1059, T1547 | 9 |
| 4 | **[FoxKitsune Malware Activity](FoxKitsune%20Malware%20Activity%20Detection)** | RAT / Espionage | Windows | T1071, T1059, T1082 | — |
| 5 | **[MuddyWater](MuddyWater)** | APT / Nation-State | Windows | T1059, T1071, T1105 | — |
| 6 | **[Authentication Bypass — Better Auth Plugin](Authentication%20Bypass%20via%20API%20Key%20Abuse%20(Better%20Auth%20Plugin))** | API Abuse / AuthN Bypass | Web / API | T1190, T1078 | — |

### Recent Additions

**April 2026**
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
 │ Initial Access     │ T1190  T1195  T1195.002  T1133  T1566.002             │
 │ Execution          │ T1059.001  T1059.004  T1204.002                       │
 │ Persistence        │ T1547.001  T1547.011  T1078                           │
 │ Privilege Esc.     │ T1078                                                 │
 │ Defense Evasion    │ T1036.005  T1497.001  T1562.001                       │
 │ Credential Access  │ T1528  T1550.001  T1555.001  T1555.003               │
 │ Collection         │ T1560.001                                             │
 │ C2                 │ T1071.001  T1105                                      │
 │ Impact             │ T1486                                                 │
 └─────────────────────────────────────────────────────────────────────────────┘
```

| Tactic | Techniques Covered | Detections |
|--------|-------------------|:----------:|
| Initial Access | Supply Chain, External Remote Services, Phishing | 3 |
| Execution | PowerShell, Unix Shell, Malicious File | 4 |
| Persistence | Registry Run Keys, Plist Modification, Valid Accounts | 3 |
| Defense Evasion | Masquerading, VM Detection, Disable Security Tools | 2 |
| Credential Access | Steal OAuth Token, Keychain, Browser Credentials | 2 |
| Command & Control | Web Protocols, Ingress Tool Transfer | 3 |
| Impact | Data Encrypted for Impact (Ransomware) | 1 |

---

## Repository Structure

```
Threat-Hunting-/
├── README.md
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
