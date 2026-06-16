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

**June 2026**
- [Ransomware Ecosystem Consolidation - Hyflock RaaS & Silent-Mode Encryption & GPO Spread](<Ransomware Ecosystem Consolidation - Hyflock RaaS & Silent-Mode Encryption & GPO Spread (June 2026)>)
- [M365 Copilot SearchLeak (CVE-2026-42824) - One-Click P2P Injection & Bing SSRF](<M365 Copilot SearchLeak (CVE-2026-42824) - One-Click P2P Injection & Bing SSRF (June 2026)>)
- [ShinyHunters Oracle PeopleSoft Zero-Day (CVE-2026-35273) - MeshCentral C2 & Education](<ShinyHunters Oracle PeopleSoft Zero-Day (CVE-2026-35273) - MeshCentral C2 & Education (June 2026)>)
- [ServiceNow Vulnerability Hunting - ACL Bypass & Table API Abuse Posture](<ServiceNow Vulnerability Hunting - ACL Bypass & Table API Abuse Posture (June 2026)>)
- [Weaponized ChatGPT Download - Electron Trojan & openew.An Typosquat](<Weaponized ChatGPT Download - Electron Trojan & openew.An Typosquat (June 2026)>)
- [RoguePlanet - Windows Defender TOCTOU Race LPE via ISO & Junction](<RoguePlanet - Windows Defender TOCTOU Race LPE via ISO & Junction (June 2026)>)
- [EDRChoker - QoS Bandwidth Throttling EDR Telemetry Suppression](<EDRChoker - QoS Bandwidth Throttling EDR Telemetry Suppression (June 2026)>)
- [Pink Hacking Group (CL-CRI-1147) - Vishing & Session Cookie Theft & M365 Auto-Exfil](<Pink Hacking Group (CL-CRI-1147) - Vishing & Session Cookie Theft & M365 Auto-Exfil (June 2026)>)
- [Vect 2.0 DEVMAN 3.0 - Broken Ransomware Decryptor & Race Conditions](<Vect 2.0 DEVMAN 3.0 - Broken Ransomware Decryptor & Race Conditions (June 2026)>)

**May 2026**
- [The Gentlemen Ransomware - Fortinet CVE-2024-55591 & G-BOT C2](<The Gentlemen Ransomware - Fortinet CVE-2024-55591 & G-BOT C2 (May 2026)>)
- [Jinx-0164 LinkedIn Social Engineering - AUDIOFIX & MINIRAT vs Crypto Orgs](<Jinx-0164 LinkedIn Social Engineering - AUDIOFIX & MINIRAT vs Crypto Orgs (May 2026)>)
- [Kali365 PhaaS - OAuth Device Code Phishing-as-a-Service vs Microsoft 365](<Kali365 PhaaS - OAuth Device Code Phishing-as-a-Service vs Microsoft 365 (May 2026)>)
- [SilverFox ValleyRAT - Fake Microsoft Teams Downloads & GameBox.exe Sideload](<SilverFox ValleyRAT - Fake Microsoft Teams Downloads & GameBox.exe Sideload (May 2026)>)
- [Nx Console VS Code Extension Compromise - TeamPCP GitHub Internal Repos Breach](<Nx Console VS Code Extension Compromise - TeamPCP GitHub Internal Repos Breach (May 2026)>)
- [Windows MiniPlasma Zero-Day - Cloud Filter Driver LPE Re-Released (CVE-2020-17103)](<Windows MiniPlasma Zero-Day - Cloud Filter Driver LPE Re-Released (CVE-2020-17103, May 2026)>)
- [First Public macOS Kernel Exploit - Apple M5 MIE Bypass LPE](<First Public macOS Kernel Exploit - Apple M5 MIE Bypass LPE (May 2026)>)
- [Pwn2Own Berlin 2026 Day 1 - Edge Sandbox & Windows LPE & LiteLLM RCE & AI Framework Bugs](<Pwn2Own Berlin 2026 Day 1 - Edge Sandbox & Windows LPE & LiteLLM RCE & AI Framework Bugs (May 2026)>)
- [TencShell Rshell-derived Framework - Screen Control & Tencent C2 Obfuscation](<TencShell Rshell-derived Framework - Screen Control & Tencent C2 Obfuscation (May 2026)>)
- [Shai-Hulud Supply Chain Contest - TeamPCP Crowdsourced npm and PyPI Worms](<Shai-Hulud Supply Chain Contest - TeamPCP Crowdsourced npm and PyPI Worms (May 2026)>)
- [Zoom Rooms & Workplace VDI Installer Vulnerabilities - DLL Hijack & Path Control](<Zoom Rooms & Workplace VDI Installer Vulnerabilities - DLL Hijack & Path Control (May 2026)>)
- [cPanel CVE-2026-41940 Auth Bypass - Mr_Rot13 Backdoor Campaign](<cPanel CVE-2026-41940 Auth Bypass - Mr_Rot13 Backdoor Campaign (May 2026)>)
- [Operational AI Abuse - Shadow LLM APIs & AI-Generated Exploit Tooling](<Operational AI Abuse - Shadow LLM APIs & AI-Generated Exploit Tooling (May 2026)>)
- [Hugging Face Malicious Model - Open-OSS Privacy-Filter Infostealer](<Hugging Face Malicious Model - Open-OSS Privacy-Filter Infostealer (May 2026)>)
- [TCLBanker - Brazilian Banker with WhatsApp & Outlook Worm Modules](<TCLBanker - Brazilian Banker with WhatsApp & Outlook Worm Modules (May 2026)>)
- [Microsoft 365 Copilot Hunting - Prompt Injection Posture & Anomalous Usage](<Microsoft 365 Copilot Hunting - Prompt Injection Posture & Anomalous Usage (May 2026)>)
- [MuddyWater Microsoft Teams Vishing - Stagecomp & Darkcomp Chaos False-Flag](<MuddyWater Microsoft Teams Vishing - Stagecomp & Darkcomp Chaos False-Flag (May 2026)>)
- [Entra ID Conditional Access Bypass - Device Code & Phantom Device Registration](<Entra ID Conditional Access Bypass - Device Code & Phantom Device Registration (May 2026)>)
- [Microsoft Edge Cleartext Password Vault Memory Exposure](<Microsoft Edge Cleartext Password Vault Memory Exposure (May 2026)>)
- [Email Bombing & Fake IT Support Teams Vishing - Quick Assist Abuse](<Email Bombing & Fake IT Support Teams Vishing - Quick Assist Abuse (May 2026)>)
- [Wireshark Multi-Dissector RCE & DoS - Pre-4.6.5 Vulnerabilities](<Wireshark Multi-Dissector RCE & DoS - Pre-4.6.5 Vulnerabilities (May 2026)>)

**April 2026**
- [BlueNoroff Fileless PowerShell ClickFix - Crypto Targeting Campaign](<BlueNoroff Fileless PowerShell ClickFix - Crypto Targeting Campaign (April 2026)>)
- [Linux Kernel "Copy Fail" - authencesn / AF_ALG LPE (CVE-2026-31431)](<Linux Kernel Copy Fail authencesn AF_ALG LPE (CVE-2026-31431)>)
- [SAP npm Supply Chain Compromise - TeamPCP mini Shai Hulud](<SAP npm Supply Chain Compromise - TeamPCP mini Shai Hulud (April 2026)>)
- [PhantomRPC - Windows RPC Runtime Impersonation LPE (Unpatched)](<PhantomRPC - Windows RPC Runtime Impersonation LPE (Unpatched April 2026)>)
- [UNC6692 Microsoft Teams External Chat Abuse - SNOW Ecosystem](<UNC6692 Microsoft Teams External Chat Abuse - SNOW Ecosystem (April 2026)>)
- [109 Fake GitHub Repositories - SmartLoader & StealC Campaign](<109 Fake GitHub Repositories - SmartLoader & StealC Campaign (April 2026)>)
- [Checkmarx KICS Supply Chain Compromise - TeamPCP Trojanized DevSecOps Scanner](<Checkmarx KICS Supply Chain Compromise - TeamPCP Trojanized DevSecOps Scanner (April 2026)>)
- [Anthropic Mythos Preview - Third-Party Contractor API Key Exposure](<Anthropic Mythos Preview - Third-Party Contractor API Key Exposure (April 2026)>)
- [SGLang Jinja SSTI RCE - Malicious GGUF Chat Template (CVE-2026-5760)](<SGLang Jinja SSTI RCE - Malicious GGUF Chat Template (CVE-2026-5760)>)
- [FUD Crypt - Signed Malware Loader with Azure Trusted Signing Abuse](<FUD Crypt - Signed Malware Loader with Azure Trusted Signing Abuse (April 2026)>)
- [Vercel Supply Chain Breach - Context.ai OAuth Compromise & Env Var Theft](<Vercel Supply Chain Breach - Context.ai OAuth Compromise & Env Var Theft (April 2026)>)
- [RedSun - Defender Cloud Files Rollback TieringEngineService LPE (Unpatched)](<RedSun - Defender Cloud Files Rollback TieringEngineService LPE (Unpatched April 2026)>)
- [Interlock Ransomware - Cisco FMC Zero-Day Exploitation (CVE-2026-20131)](<Interlock Ransomware - Cisco FMC Zero-Day Exploitation (CVE-2026-20131)>)
- [Active Directory RPC RCE - Domain Controller Memory Corruption (CVE-2026-33826)](<Active Directory RPC RCE - Domain Controller Memory Corruption (CVE-2026-33826)>)
- [BlueHammer - Defender Antimalware Platform Local Privilege Escalation (CVE-2026-33825)](<BlueHammer - Defender Antimalware Platform Local Privilege Escalation (CVE-2026-33825)>)
- [ShowDoc RCE - Unrestricted File Upload Webshell & Botnet Deployment (CVE-2025-0520)](<ShowDoc RCE - Unrestricted File Upload Webshell & Botnet Deployment (CVE-2025-0520)>)
- [Malicious Chrome Extensions - Data Exfil Session Theft Shared C2](<Malicious Chrome Extensions - Data Exfil Session Theft Shared C2 (April 2026)>)
- [Fake Proxifier ClipBanker - GitHub Supply Chain](<Fake Proxifier ClipBanker - GitHub Supply Chain (April 2026)>)
- [Malicious AI Browser Extensions](<Malicious AI Browser Extensions (April 2026)>)
- [Fake AI Installer PlugX Campaign](<Fake AI Installer PlugX Campaign (April 2026)>)
- [Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)](<Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)>)
- [EDR Killers - Ransomware BYOVD Defense Evasion](<EDR Killers - Ransomware BYOVD Defense Evasion (April 2026)>)
- [Cookie Theft & Session Hijacking - Infostealer Browser Credential Hunting](<Cookie Theft & Session Hijacking - Infostealer Browser Credential Hunting (April 2026)>)
- [ClickFix macOS Script Editor - Atomic Stealer Delivery](<ClickFix macOS Script Editor - Atomic Stealer Delivery (April 2026)>)
- [Adobe Reader 0-Day PDF Exploit - Data Theft & System Fingerprinting (CVE-2026-27220)](<Adobe Reader 0-Day PDF Exploit - Data Theft & System Fingerprinting (CVE-2026-27220)>)
- [ChipSoft HiX EHR - Supply Chain Ransomware Detection (CVE-2026-4404)](<ChipSoft HiX EHR - Supply Chain Ransomware Detection (CVE-2026-4404)>)

**March 2026**
- [EvilTokens & AMOS Stealer - OAuth Hijacking & macOS Credential Theft](<EvilTokens & AMOS Stealer - OAuth Hijacking & macOS Credential Theft (March 2026)>)
- [PureLog Stealer - Copyright Lure Fileless .NET Campaign](<PureLog Stealer - Copyright Lure Fileless .NET Campaign (March 2026)>)
- [MuddyWater - MENA Phishing-to-Backdoor Campaign](<MuddyWater - MENA Phishing-to-Backdoor Campaign (March 2026)>)
- [FoxKitsune - Cloud-Staged Malware Loader](<FoxKitsune - Cloud-Staged Malware Loader (March 2026)>)
- [Authentication Bypass - API Key Abuse Better Auth Plugin](<Authentication Bypass - API Key Abuse Better Auth Plugin (March 2026)>)

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
│
├── Linux Kernel Copy Fail authencesn AF_ALG LPE (CVE-2026-31431)/
│   └── Linux_Kernel_CopyFail_Detection.kql
├── SAP npm Supply Chain Compromise - TeamPCP mini Shai Hulud (April 2026)/
│   └── SAP_npm_TeamPCP_Detection.kql
├── PhantomRPC - Windows RPC Runtime Impersonation LPE (Unpatched April 2026)/
│   └── PhantomRPC_Detection.kql
├── UNC6692 Microsoft Teams External Chat Abuse - SNOW Ecosystem (April 2026)/
│   └── UNC6692_Teams_SNOW_Detection.kql
├── 109 Fake GitHub Repositories - SmartLoader & StealC Campaign (April 2026)/
│   └── SmartLoader_StealC_Detection.kql
├── Checkmarx KICS Supply Chain Compromise - TeamPCP Trojanized DevSecOps Scanner (April 2026)/
│   └── Checkmarx_KICS_Detection.kql
├── Anthropic Mythos Preview - Third-Party Contractor API Key Exposure (April 2026)/
│   └── Anthropic_Mythos_Detection.kql
├── SGLang Jinja SSTI RCE - Malicious GGUF Chat Template (CVE-2026-5760)/
│   └── CVE-2026-5760_Detection.kql
├── FUD Crypt - Signed Malware Loader with Azure Trusted Signing Abuse (April 2026)/
│   └── FUD_Crypt_Detection.kql
├── Vercel Supply Chain Breach - Context.ai OAuth Compromise & Env Var Theft (April 2026)/
│   └── Vercel_Breach_Detection.kql
├── RedSun - Defender Cloud Files Rollback TieringEngineService LPE (Unpatched April 2026)/
│   └── RedSun_Defender_LPE_Detection.kql
├── Interlock Ransomware - Cisco FMC Zero-Day Exploitation (CVE-2026-20131)/
│   └── Interlock_Cisco_FMC_Detection.kql
├── Active Directory RPC RCE - Domain Controller Memory Corruption (CVE-2026-33826)/
│   └── CVE-2026-33826_Detection.kql
├── BlueHammer - Defender Antimalware Platform Local Privilege Escalation (CVE-2026-33825)/
│   └── CVE-2026-33825_Detection.kql
├── ShowDoc RCE - Unrestricted File Upload Webshell & Botnet Deployment (CVE-2025-0520)/
│   └── CVE-2025-0520_Detection.kql
├── Malicious Chrome Extensions - Data Exfil Session Theft Shared C2 (April 2026)/
│   └── Malicious_Chrome_Ext_C2_Detection.kql
├── Fake Proxifier ClipBanker - GitHub Supply Chain (April 2026)/
│   └── ClipBanker_Proxifier_Detection.kql
├── Malicious AI Browser Extensions (April 2026)/
│   └── Malicious_AI_Extensions_Detection.kql
├── Fake AI Installer PlugX Campaign (April 2026)/
│   └── FakeAI_PlugX_Detection.kql
├── Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)/
│   └── CVE-2026-34621_Detection.kql
├── EDR Killers - Ransomware BYOVD Defense Evasion (April 2026)/
│   └── EDR_Killers_Detection.kql
├── Cookie Theft & Session Hijacking - Infostealer Browser Credential Hunting (April 2026)/
│   └── Cookie_Theft_Session_Hijacking_Detection.kql
├── ClickFix macOS Script Editor - Atomic Stealer Delivery (April 2026)/
│   └── ClickFix_AtomicStealer_Detection.kql
├── Adobe Reader 0-Day PDF Exploit - Data Theft & System Fingerprinting (CVE-2026-27220)/
│   └── CVE-2026-27220_Detection.kql
├── ChipSoft HiX EHR - Supply Chain Ransomware Detection (CVE-2026-4404)/
│   └── CVE-2026-4404_Detection.kql
├── EvilTokens & AMOS Stealer - OAuth Hijacking & macOS Credential Theft (March 2026)/
│   └── EvilTokens_AMOS_Detection.kql
├── PureLog Stealer - Copyright Lure Fileless .NET Campaign (March 2026)/
│   └── PureLog_Stealer_Detection.kql
├── FoxKitsune - Cloud-Staged Malware Loader (March 2026)/
│   └── FoxKitsune_Detection.md
├── MuddyWater - MENA Phishing-to-Backdoor Campaign (March 2026)/
│   └── MuddyWater_Detection.md
└── Authentication Bypass - API Key Abuse Better Auth Plugin (March 2026)/
    └── BetterAuth_Detection.md
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
