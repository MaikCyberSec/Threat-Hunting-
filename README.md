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
- `Active Directory RPC RCE - Domain Controller Memory Corruption (CVE-2026-33826)` — Critical (CVSS 8.0–9.8) Remote Code Execution in Active Directory Domain Services via specially crafted RPC calls. Improper input validation (CWE-20) in the AD DS RPC interface causes memory corruption yielding SYSTEM-level code execution on domain controllers. Microsoft rates exploitation as "More Likely." Affects all Windows Server versions from 2012 R2 through 2025 (Standard + Core). Patches: KB5082063 (Server 2025), KB5082142 (Server 2022). 12 queries covering vulnerable DC inventory, anomalous RPC endpoint mapper traffic (TCP 135) to DCs, high-volume dynamic RPC port burst detection, unexpected child processes from lsass/ntdsa/AD Web Services, RPC named pipe access from non-DC hosts, special privilege assignment (Event 4672), NTLM authentication downgrade anomalies, LSASS memory access / credential dumping, ntds.dit exfiltration via ntdsutil/vssadmin, DCSync replication from non-DC sources, audit log clearing on DCs, and full RPC-burst-to-exec-to-credential-theft kill-chain correlation.
- `BlueHammer - Defender Antimalware Platform Local Privilege Escalation (CVE-2026-33825)` — Microsoft Defender 0-day patched in April 15, 2026 Patch Tuesday. CVSS 7.8 local privilege escalation in the Defender Antimalware Platform signature-update mechanism. Chains four legitimate Windows components (Defender update workflow, VSS, Cloud Files API, oplocks) into a TOCTOU race that yields NT AUTHORITY\SYSTEM from standard user context. PoC ("BlueHammer") leaked April 3 via github.com/Nightmare-Eclipse/BlueHammer. Exploitation: EICAR drop → Defender scan → VSS snapshot → Cloud Files sync-root registration + `.lock` placeholder → oplock freezes Defender → SAM/SYSTEM/SECURITY hive extraction from shadow copy → NTLM decrypt → local admin password change → SYSTEM service creation → password hash restored. Microsoft signature `Exploit:Win32/DfndrPEBluHmr.BB` only catches the original PoC binary. Affected: Antimalware Platform <= 4.18.26020.6. Patched: 4.18.26050.3011. 12 queries covering vulnerable version inventory, Defender signature hits, EICAR test file drops, VSS shadow copy enumeration by non-backup processes, SAM/SYSTEM/SECURITY hive access from shadow copies, Cloud Files API (cldapi.dll) sync-root registration abuse, `.lock` placeholder drops, local admin 4723/4724 password-change-and-restore pairs (SecurityEvent template), service creation with SYSTEM privileges from low-priv user, medium-integrity parent spawning high-integrity child, BlueHammer PoC filename artifacts, and full trigger-to-CloudFiles-to-hive-read-to-elevation kill-chain correlation.
- `ShowDoc RCE - Unrestricted File Upload Webshell & Botnet Deployment (CVE-2025-0520)` — Critical (CVSS 9.4) unauthenticated unrestricted file upload in the ShowDoc documentation platform's `/index.php?s=/home/page/uploadImg` endpoint. Exploited in the wild (VulnCheck, April 2026) via automated Python scanners. Attackers bypass extension validation using angle-bracket filenames (e.g., `test.<>php`), drop PHP webshells to `Public/Uploads/`, and deploy Srv-Hello, Mirai, BillGates DDoS trojans plus Monero cryptominers. 2,000+ unpatched internet-facing instances remain exposed. Affected: ShowDoc < 2.8.7. 12 queries covering known-bad C2 IPs/domains (1.117.4.172, 194.145.227.21, 1w.kacdn.cn), 29 known MD5 hashes, ShowDoc inventory/footprint discovery, PHP webshell drops to Public/Uploads, angle-bracket filename bypass pattern, web server spawning shell/curl/wget, ldr.sh and sys.x86_64 loader downloads, XMRig/Monero wallet indicators, botnet scanning fan-out, web-log template for /uploadImg POSTs, and full webshell-to-exec-to-C2 kill-chain correlation.
- `Malicious Chrome Extensions - Data Exfil Session Theft Shared C2` — Socket Threat Research identified 108 malicious Chrome extensions (~20,000 installs) under five front identities (Yana Project, GameGen, SideGames, Rodeo Games, InterAlt) sharing a single C2 server at 144.126.135.238 (Contabo GmbH). 54 extensions harvested Google account identities via OAuth2 (chrome.identity.getAuthToken). A "Telegram Multi-account" extension stole user_auth tokens from localStorage every 15 seconds for full Telegram Web session hijack. 45 extensions had a universal backdoor opening arbitrary URLs at browser startup. 78 extensions contained content injection via unsanitized innerHTML. C2 domain: cloudapi[.]stream with Strapi CMS backend on port 1337. 13 queries covering C2 domain/IP beacons, Strapi port 1337 connections, OAuth2 token harvesting, Telegram session theft, 15-second beacon pattern detection, browser startup URL injection, declarativeNetRequest header stripping, /user_info exfil endpoint, threat actor attribution artifacts, bulk extension install detection, content injection indicators, full install-to-OAuth-to-C2 kill-chain correlation, and a live external threat feed (malicious_extension_sentry) join for auto-updating malicious extension ID detection.
- `Fake Proxifier ClipBanker - GitHub Supply Chain` — Trojanized Proxifier installer hosted on GitHub (lukecodix/Proxifier releases), distributing ClipBanker RAT since early 2025. 2,000+ victims (India/Vietnam focus). Infection chain: malicious wrapper → Defender exclusion abuse (TMP/powershell/conhost) → stub Proxifier*.tmp donor process → api_updater.exe / proxifierupdater.exe injection → conhost.exe / fontdrvhost.exe hollowing → fileless C++ ClipBanker replacing crypto wallet addresses across 26+ blockchains. Multi-stage payload from pastebin/snippet.host/chiaselinks.com/rlim.com/paste.kealper.com. Persistence via `HKLM\SOFTWARE\System::Config` registry + scheduled task. 12 queries covering known hashes, trojanized binary names, Defender exclusion abuse, registry persistence, schtask→PowerShell→registry chain, process hollowing into OS binaries, C2/payload-hosting domains, Pastebin/Gist exact-path IOC hits, fontdrvhost/conhost outbound anomaly, Proxifier*.tmp stub detection, GitHub-release-to-execution correlation, and full kill-chain correlation.
- `Malicious AI Browser Extensions` — Hunt pack built from LayerX's April 11 research showing AI browser extensions are 60% more likely to carry a CVE, 3× more likely to access cookies, 2.5× more likely to run remote scripts, and 6× more likely to request elevated permissions. Incorporates the Dec 2024 Cyberhaven supply-chain cluster (36 extensions, 2.6M users) and the Jan 2026 Huntress "CrashFix" extension-to-RAT campaign. 10 queries covering known-bad extension IDs, silent manifest installs by non-browser parents, forced-install policy registry abuse, dangerous-permission pivots, browser loading remote `.js` from non-CDN hosts, cookie/Local State reads by non-browser processes, CrashFix/ClickFix `explorer → mshta/powershell` correlation, new-extension-plus-new-outbound correlation, exfil burst detection, and full install→remote-script→LOLBin kill-chain correlation.
- `Fake AI Installer PlugX Campaign` — Active campaign distributing trojanized installers of popular AI developer tooling via typosquatted / lookalike domains and malicious search ads. Victims receive a working front-end while the installer deploys PlugX RAT through DLL sideloading of a signed G DATA updater (`NOVUpdate.exe` → malicious `avk.dll` → XOR-encrypted `NOVUpdate.exe.dat`). Beacons to `8.217.190.58:443` within ~22 seconds. Parallel vectors: trojanized VS Code extensions calling `*.official-version[.]com` and macOS MacSync stealer calling `a2abotnet[.]com`. Known SHA256s: `d5590802bf0926ac30d8e31c0911439c35aead82bf17771cfd1f9a785a7bf143` (avk.dll), `8ac88aeecd19d842729f000c6ab732261cb11dd15cdcbb2dd137dc768b2f12bc` (payload). 10 queries covering known hashes, avk.dll sideloading outside legitimate G DATA paths, NOVUpdate.exe from user-writable paths, XOR payload artifacts, known C2 IP/domain beacons, typosquatted installer archive drops, TCP/IP registry tampering post-execution, Defender exclusion abuse, and full drop→sideload→C2 kill-chain correlation.
- `Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)` — Critical (CVSS 8.6) zero-day in Adobe Acrobat / Reader, patched April 11 via APSB26-43 (priority 1). Prototype Pollution enabling arbitrary code execution when a victim opens a crafted PDF. Disclosed by Haifei Li / EXPMON; active exploitation observed since December 2025. Exploit abuses `util.readFileIntoStream()` for local file exfiltration and `RSS.addFeed()` for C2. Known SHA256: `65dca34b04416f9a113f09718cbe51e11fd58e7287b7863e37f393ed4d25dde7`. 10 queries covering the known hash, vulnerable-version inventory (Acrobat DC ≤26.001.21367, Acrobat 2024 ≤24.001.30356), suspicious Acrobat children (LOLBins/shells), anomalous outbound from AcroRd32/Acrobat, sensitive local file reads (SSH/AWS/Azure/browser stores), PDF drops from mail/browser, obfuscated-JavaScript indicators, 5-minute PDF-open→beacon correlation, stage-2 executable drops, and full kill-chain correlation (deliver→open→bad signal in 1h).
- `EDR Killers - Ransomware BYOVD Defense Evasion` — ESET reports EDR killers have become a standard component of modern ransomware intrusions (EDRKillShifter, AvNeutralizer/AuKill, Terminator, RealBlindingEDR, GhostDriver/Poortry). Attackers drop legitimately-signed but vulnerable kernel drivers (rtcore64.sys, gdrv.sys, truesight.sys, dbutil_2_3.sys, mhyprot2.sys, procexp152.sys) and abuse their IOCTLs to kill protected security processes from kernel space. 10 queries covering vulnerable driver loads, driver drops to user-writable paths, kernel service creation, known EDR-killer binaries, Defender/EDR tampering, mass security-process termination, unsigned kernel loads, registry-based tampering, full kill-chain correlation, and a hash-free behavioural catch-all.
- `Cookie Theft & Session Hijacking` — With Google rolling out Device Bound Session Credentials (DBSC) in Chrome 146 to combat cookie theft, this detection hunts for the attacker side of session hijacking. Targets infostealers (Lumma, RedLine, Raccoon, Vidar, AMOS) reading browser credential stores, Local State encryption keys, DPAPI master keys, and cookie databases. 12 queries covering non-browser credential access, encryption key extraction, DPAPI abuse, staging/archiving, SQLite direct queries, multi-browser-vendor correlation, Entra ID token replay, impossible travel, C2 exfiltration, and full kill-chain correlation.
- `ClickFix macOS Script Editor` — New ClickFix social-engineering campaign bypassing Terminal entirely by abusing macOS Script Editor (osascript) to deliver Atomic Stealer (AMOS). Victims lured via fake CAPTCHAs and browser error dialogs. Steals Keychain credentials, browser passwords, cookies, crypto wallets, and session tokens. 12 queries covering browser-to-osascript execution, encoded payloads, curl-pipe-to-shell delivery, Keychain harvesting, browser credential theft, crypto wallet access, LaunchAgent persistence, Gatekeeper bypass, DMG execution chains, C2 exfiltration, and full kill-chain correlation.
- `Adobe Reader 0-Day PDF Exploit` — Active zero-day (CVE-2026-27220) discovered by EXPMON on March 26. Malicious PDF abuses JavaScript APIs (util.readFileIntoStream, RSS.addFeed) to steal local files and fingerprint systems. No user interaction beyond opening the PDF. C2: 169.40.2.68:45191. 12 queries covering C2 communication, vulnerable version detection, suspicious child processes, file read anomalies, and multi-signal correlation.
- `ChipSoft HiX EHR` — Following the April 7 ransomware attack on ChipSoft, the dominant Dutch healthcare EHR provider (~70% market share). Includes 9 queries covering software inventory, process detection, VPN connections, post-compromise indicators, and lateral movement. Z-CERT advisory: disconnect VPN immediately.
- `EvilTokens & AMOS Stealer` — Dual campaign: OAuth Device Code phishing hijacking enterprise M365 accounts (180+ phishing URLs/week), and AMOS macOS stealer targeting AI developers via ClickFix lures. 12 queries covering OAuth anomalies, C2 domains, behavioral patterns, persistence, credential harvesting, and multi-signal correlation.

**March 2026**
- `PureLog Stealer - Copyright Lure Fileless .NET Campaign` — Copyright-themed lure campaign delivering fileless .NET stealer via renamed Python interpreter, AMSI bypass, and registry persistence.
- `MuddyWater - MENA Phishing-to-Backdoor Campaign` — Iranian APT targeting government and telecom sectors via spear-phishing with malicious Office documents, staged payload delivery, and long-term persistence.
- `FoxKitsune - Cloud-Staged Malware Loader` — Malware loader leveraging trusted cloud infrastructure (Cloudflare Pages, Netlify, Discord) for payload staging, in-memory execution, process masquerading, and Defender tampering.
- `Authentication Bypass - API Key Abuse Better Auth Plugin` — API key abuse vulnerability in Better Auth plugin enabling authentication bypass, account impersonation, and privilege escalation.

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
