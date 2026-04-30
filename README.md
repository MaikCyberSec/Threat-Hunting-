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
- `SAP npm Supply Chain Compromise - TeamPCP mini Shai Hulud` — On Apr 29 2026 StepSecurity / Aikido / SafeDep / Socket / Wiz disclosed a coordinated TeamPCP hit on four widely-used SAP npm packages — `@cap-js/postgres@2.2.2`, `@cap-js/db-service@2.10.1`, `@cap-js/sqlite@2.2.2`, `mbt@1.2.48` — using the same Bun-based loader playbook as the prior **Trivy / LiteLLM / Checkmarx KICS** campaigns. Malicious `preinstall` hook drops `setup.mjs` (SHA1 `307d0fa7…f20431`) which fetches Bun and runs an 11 MB obfuscated `execution.js` (SHA256 `eb6eb415…ae8bcdb`). Steals GitHub tokens, `.npmrc`, AWS / Azure / GCP env vars, Kubernetes service-account tokens, and GitHub Actions runtime secrets — exfils to attacker-controlled GitHub repos, then uses stolen npm tokens to **republish other packages** owned by compromised maintainers (worm-style spread → "mini Shai Hulud"). Russian-locale early-exit and `__decodeScrambled` cipher are consistent attribution fingerprints. **12 KQL queries** covering the four tarball SHA256s + stage-2 hash, the SHA1 dropper, exact `package@version` strings on cmdline / lockfiles / `package.json`, `setup.mjs` + `execution.js` drops in `node_modules`, Bun fetched by npm/node during install, the `__decodeScrambled` string sweep, post-install credential harvest by node/bun (.npmrc / .aws / .azure / .gcloud / .kube / .ssh / `/var/run/secrets/kubernetes.io`), `preinstall` hook spawn lineage, post-install `npm publish` bursts (worm spread), GitHub egress from infected build agents, and full risk-ranked correlation. Chains directly with the existing Checkmarx KICS detection — run both for full TeamPCP coverage.
- `PhantomRPC - Windows RPC Runtime Impersonation LPE (Unpatched)` — Disclosed at Black Hat Asia 2026 (Apr 24) by Haidar Kabibo (Kaspersky). **Architectural** flaw in `rpcrt4.dll`: when a privileged client RPCs an offline / disabled service, the runtime never validates the responding server. A low-priv attacker holding `SeImpersonatePrivilege` (default for `Network Service` / `Local Service`) stands up a rogue server mimicking the disabled endpoint, calls `RpcImpersonateClient`, and steals the caller's token → SYSTEM. **MSRC closed the case with no patch.** Five abuse paths: `gpupdate.exe`→TermService, `msedge.exe`→TermService, WDI polling TermService every 5–15 min, `ipconfig.exe`/DHCP, `w32tm.exe`→`\PIPE\W32TIME`. **12 KQL queries** covering rogue named-pipe creation on `\PIPE\W32TIME` / TermService / DHCP, service-state tampering on the abused services (sc/PowerShell + registry `Start` value), abused-binary parent anomalies, WDI poll tick correlation, Network/Local Service hosting service-mimicking pipes, `rpcrt4.dll` loaded from user-writable paths, ServiceInstalled by low-priv accounts, integrity-level inversion (Local/Network Service parent → SYSTEM child), the public PhantomRPC audit-framework artefacts, and full risk-ranked correlation. Detection-as-mitigation since no fix exists; complement with Sysmon Event 17/18 and the `Microsoft-Windows-RPC` ETW provider for deeper coverage.
- `UNC6692 Microsoft Teams External Chat Abuse - SNOW Ecosystem` — GTIG / Mandiant disclosed (Apr 22 2026) a campaign abusing **legitimate** Microsoft Teams external chat — no vuln, no exploit. Actor poses as IT helpdesk, links use `microsoft-edge:` URI to force-render a "Mailbox Repair and Sync Utility v2.1.5" phish page that rejects the first two passwords and exfils creds to AWS S3 on the third. Follow-on drops the **SNOW ecosystem**: SNOWBELT (Edge extension in `SysEvents` folder, unique VAPID key, DGA S3 C2), SNOWGLAZE (Python WSS tunneler to Heroku `sad4w7h913-b4a57f9c36eb[.]herokuapp[.]com`), SNOWBASIN (local Python HTTP server on :8000). AutoHotKey binary renamed `RegSrvc.exe` + `Protected.ahk`. Post-exploit: Task Manager LSASS dump, FTK Imager for NTDS.dit/SAM/SYSTEM/SECURITY, Pass-the-Hash, LimeWire exfil. **13 KQL queries** covering external-tenant Teams chat, `microsoft-edge:` URI from Teams/Outlook, the service-page-*-outlook S3 pattern, SNOWBELT DGA regex, VAPID key string sweep, `SysEvents` extension drops, `RegSrvc.exe`/.ahk staging, Heroku WSS egress, headless Edge with `--load-extension`, Python :8000 listeners, post-exploit credential/tool abuse, NTDS hive access, and full risk-ranked kill-chain correlation.
- `109 Fake GitHub Repositories - SmartLoader & StealC Campaign` — Hexastrike exposed a 7-week operation using **109 fake GitHub repos across 103 accounts** (cloned from legit open-source projects, SEO-padded descriptions) to drop SmartLoader + StealC. Chain: victim extracts ZIP → single-line `.bat` launches LuaJIT → obfuscated Lua stager hides console, anti-debugs, resolves live C2 IP via a **Polygon smart-contract dead drop** at `polygon.drpc.org` → multipart POST to a bare IP on `/api/` or `/task/` with host fingerprint + screenshot → StealC reflectively loaded in-memory. Persistence via two scheduled tasks: `AudioManager_ODM3` and `OfficeClickToRunTask_7d7757`. **12 KQL queries** covering the exact task names, polygon.drpc.org from non-browsers, LuaJIT from user-writable paths, bat-to-interpreter chain with `.txt`/`.log` scripts, bare-IP multipart POSTs, raw.githubusercontent→exec within 10 min, obfuscated schtasks regex, zip-to-bat launch correlation, console-hiding API abuse, StealC-style credential store reads, and full risk-ranked kill-chain correlation.
- `Checkmarx KICS Supply Chain Compromise - TeamPCP Trojanized DevSecOps Scanner` — TeamPCP poisoned Checkmarx KICS Docker Hub images (`v2.1.20`, `v2.1.20-debian`, `debian`, `alpine`, `latest`, fraudulent `v2.1.21`), VS Code extensions (`cx-dev-assist`, `ast-results`), and a backdated GitHub commit `68ed490b`. Trojan Go ELF → Bun-launched `mcpAddon.js` → GitHub / AWS / Azure / GCP / SSH / `.npmrc` / env theft → C2 `audit.checkmarx[.]cx` (94.154.172.43). Stolen tokens inject `format-check.yml` Actions workflow abusing `${{ toJSON(secrets) }}` to exfil all repo secrets. **13 KQL queries** covering IOCs, Bun misuse, credential harvest, workflow injection, and full kill-chain correlation.
- `Anthropic Mythos Preview - Third-Party Contractor API Key Exposure` — Unauthorised group demonstrated access to Anthropic's internal "Mythos" preview model via shared API keys held by a third-party contractor, then located the non-public endpoint by URL-inference from Anthropic's naming conventions. Anthropic's core infrastructure was not breached — this is a textbook **upstream third-party credential-exposure pattern** applied to LLM vendors, relevant to any enterprise using Anthropic / OpenAI / Azure OpenAI keys. **12 KQL queries** covering Anthropic traffic baseline, `sk-ant-` / `ANTHROPIC_API_KEY` material on disk and command-line, preview-model hostname references, LLM-as-exfil volume, impossible travel, and full risk-ranked correlation.
- `SGLang Jinja SSTI RCE - Malicious GGUF Chat Template (CVE-2026-5760)` — CVSS 9.8 unauthenticated RCE in the SGLang LLM serving framework. `getjinjaenv()` renders `tokenizer.chat_template` from loaded GGUF models via unsandboxed `jinja2.Environment()`; a single `POST /v1/rerank` with a malicious GGUF executes code as root in the default Docker image. Affects SGLang ≤ 0.5.9, no vendor patch at disclosure (CERT/CC VU#915947, Apr 20 2026); PoC at `github.com/Stuub/SGLang-0.5.9-RCE`. **12 KQL queries** covering SGLang inventory, model-path origin tracking, SSTI payload fingerprints (`__globals__` / `__builtins__` / `cycler.__init__`), IMDS and K8s service-account access, related pickle CVE cluster, and full kill-chain correlation. Linux-oriented.
- `FUD Crypt - Signed Malware Loader with Azure Trusted Signing Abuse` — Malware-as-a-Service crypter (`fudcrypt.net`, $800–$2,000/mo) producing polymorphic, triple-encrypted Windows implants. Abuses **Azure Trusted Signing** to chain payloads to the Microsoft Identity Verification Root CA — binaries appear Microsoft-signed and bypass SmartScreen. DLL-sideloading via Zoom/Slack/VS Code/OneDrive carriers → AMSI + ETW kill → PEB masquerading → WSS C2 to `mstelemetrycloud.com`; persistence via `WindowsUpdateSvc` Run key + `MicrosoftEdgeUpdateCore` scheduled task. **12 KQL queries** covering C2 / panel domains, carrier sideloading, signer abuse in user-writable paths, persistence, WebSocket beacon interval analysis, AMSI/ETW tampering, and full delivery-to-C2 correlation.
- `Vercel Supply Chain Breach - Context.ai OAuth Compromise & Env Var Theft` — Vercel disclosed unauthorised internal access (Apr 19–20 2026) following upstream compromise of Context.ai (third-party AI SaaS used by a Vercel employee) → malicious Google Workspace OAuth grant → account takeover → enumeration of env vars not marked "sensitive" → exfiltration. ShinyHunters listed data on BreachForums for $2M. Primary IOC: Google OAuth client ID `110671459871-30f1spbu0hptbs60cb4vsmv79i7bbvqj.apps.googleusercontent.com`. **12 KQL queries** covering the OAuth client ID, Context.ai grants, Vercel API / CLI / `auth.json` access, `.env` reads, impossible travel, credential-scanner tooling, token-abuse bursts, and Context.ai ↔ Vercel cross-exposure correlation.
- `RedSun - Defender Cloud Files Rollback TieringEngineService LPE (Unpatched)` — Second Microsoft Defender 0-day from Chaotic Eclipse (Apr 15 2026) — **UNPATCHED**, no CVE. Abuses Defender's cloud-file rollback: EICAR drop via Cloud Files API → Defender quarantine → oplock race → NTFS junction redirects the rollback write to `C:\Windows\System32\TieringEngineService.exe` → Storage Tiers service executes attacker code as SYSTEM. BlueHammer's Apr 15 patch does NOT mitigate. PoC: `github.com/Nightmare-Eclipse/RedSun`. **12 KQL queries** covering TieringEngineService modification, ImagePath tampering, EICAR in cloud-synced paths, `cldapi.dll` loads, junction creation targeting System32, rollback-plus-junction correlation within 5 min, and full Cloud-Files-to-overwrite kill-chain.
- `Interlock Ransomware - Cisco FMC Zero-Day Exploitation (CVE-2026-20131)` — Interlock exploited CVE-2026-20131 (CVSS 10.0 Java deserialisation) in Cisco Secure Firewall Management Center as a zero-day since Jan 26 2026 — 36 days pre-disclosure, unauth RCE as root. Also leverages CVE-2026-20079 (auth bypass). Full kill chain: FMC exploit + ClickFix → PowerShell RAT → Lumma/Berserk/`klg.dll`/`cht.exe` + Kerberoasting → Cobalt Strike + SystemBC + Interlock RAT C2 `150.171.27.10` → WDAC/EDR kill → AzCopy exfil → `.interlock` encryption. CISA KEV since Mar 19. Victims include DaVita, Kettering Health. **13 KQL queries** covering hashes, C2 beacon, payload masquerading, ransom-note artifacts, Kerberoasting, unauthorised RMM, AzCopy exfil, GPO note propagation, and full kill-chain correlation.
- `Active Directory RPC RCE - Domain Controller Memory Corruption (CVE-2026-33826)` — Critical (CVSS 8.0–9.8) RCE in AD DS via crafted RPC calls; improper input validation (CWE-20) corrupts memory and yields SYSTEM on domain controllers. Microsoft rates exploitation "More Likely." Affects all Windows Server from 2012 R2 through 2025 (Standard + Core). Patches: KB5082063 (Server 2025), KB5082142 (Server 2022). **12 KQL queries** covering DC inventory, anomalous RPC endpoint mapper (TCP 135) traffic, dynamic RPC port bursts, unexpected lsass/ntdsa children, NTLM downgrade, LSASS credential dumping, `ntds.dit` exfil via `ntdsutil`/`vssadmin`, DCSync from non-DCs, audit log clearing, and full RPC-to-credential-theft correlation.
- `BlueHammer - Defender Antimalware Platform Local Privilege Escalation (CVE-2026-33825)` — Defender 0-day patched Apr 15 2026 Patch Tuesday. CVSS 7.8 LPE chaining Defender update workflow + VSS + Cloud Files API + oplocks into a TOCTOU race → SAM/SYSTEM/SECURITY hive extraction from shadow copy → NTLM decrypt → SYSTEM. PoC leaked Apr 3 (`github.com/Nightmare-Eclipse/BlueHammer`). Microsoft signature `Exploit:Win32/DfndrPEBluHmr.BB` only catches the original PoC binary. Affected AM Platform ≤ 4.18.26020.6, patched 4.18.26050.3011. **12 KQL queries** covering vulnerable version, EICAR drops, VSS enumeration, hive reads from shadow copies, `cldapi.dll` abuse, `.lock` placeholders, 4723/4724 password-change-and-restore pairs, integrity-level pivots, and full kill-chain correlation.
- `ShowDoc RCE - Unrestricted File Upload Webshell & Botnet Deployment (CVE-2025-0520)` — CVSS 9.4 unauthenticated unrestricted file upload in ShowDoc's `/index.php?s=/home/page/uploadImg`. Attackers bypass extension validation via angle-bracket filenames (`test.<>php`), drop PHP webshells to `Public/Uploads/`, and deploy Srv-Hello / Mirai / BillGates DDoS trojans plus Monero miners. 2,000+ unpatched internet-facing instances remain exposed (VulnCheck, Apr 2026). Affects ShowDoc < 2.8.7. **12 KQL queries** covering C2 IPs (`1.117.4.172`, `194.145.227.21`, `1w.kacdn.cn`), 29 known MD5 hashes, ShowDoc inventory, webshell drops, angle-bracket bypass, web→shell spawns, `ldr.sh` / `sys.x86_64` loaders, XMRig wallets, and full webshell-to-C2 correlation.
- `Malicious Chrome Extensions - Data Exfil Session Theft Shared C2` — Socket Research identified 108 malicious Chrome extensions (~20K installs) under 5 front identities (Yana Project, GameGen, SideGames, Rodeo Games, InterAlt) sharing C2 `144.126.135.238` (Contabo) and `cloudapi[.]stream` (Strapi CMS, port 1337). 54 harvested Google identities via `chrome.identity.getAuthToken`; a "Telegram Multi-account" extension stole `user_auth` from localStorage every 15s for full Telegram Web session hijack. **13 KQL queries** covering C2 domain/IP beacons, Strapi port 1337 connections, OAuth2 harvest, Telegram session theft, 15-sec beacon pattern, `/user_info` exfil, bulk install detection, a live external threat-feed join, and full install-to-OAuth-to-C2 correlation.
- `Fake Proxifier ClipBanker - GitHub Supply Chain` — Trojanised Proxifier installer on GitHub (`lukecodix/Proxifier` releases) distributing ClipBanker RAT since early 2025; 2,000+ victims (India/Vietnam focus). Chain: malicious wrapper → Defender exclusion abuse (TMP/PowerShell/conhost) → `Proxifier*.tmp` stub donor → `api_updater.exe`/`proxifierupdater.exe` injection → `conhost.exe`/`fontdrvhost.exe` hollowing → fileless C++ ClipBanker replacing crypto wallet addresses across 26+ blockchains. Persistence via `HKLM\SOFTWARE\System::Config` + scheduled task. **12 KQL queries** covering known hashes, exclusion abuse, registry persistence, schtask→PowerShell chain, hollowing, Pastebin/Gist staging, stub detection, and full kill-chain correlation.
- `Malicious AI Browser Extensions` — Built from LayerX research (Apr 11): AI browser extensions are 60% more likely to carry a CVE, 3× more likely to access cookies, 2.5× more likely to run remote scripts, and 6× more likely to request elevated permissions. Incorporates the Dec 2024 Cyberhaven supply-chain cluster (36 extensions, 2.6M users) and the Jan 2026 Huntress "CrashFix" extension-to-RAT campaign. **10 KQL queries** covering known-bad extension IDs, silent manifest installs by non-browser parents, forced-install policy registry abuse, dangerous-permission pivots, remote `.js` from non-CDN hosts, non-browser `Local State` reads, CrashFix `explorer → mshta/powershell` correlation, and full install-to-LOLBin kill-chain.
- `Fake AI Installer PlugX Campaign` — Trojanised AI developer tooling installers distributed via typosquatted domains and malicious search ads; victims get a working front-end while PlugX RAT lands via DLL sideloading of a signed G DATA updater (`NOVUpdate.exe` → malicious `avk.dll` → XOR-encrypted `NOVUpdate.exe.dat`), beaconing to `8.217.190.58:443` within ~22s. Parallel: trojanised VS Code extensions calling `*.official-version[.]com`, macOS MacSync stealer calling `a2abotnet[.]com`. Known SHA256s `d559…f143` (avk.dll) and `8ac8…12bc` (payload). **10 KQL queries** covering hashes, `avk.dll` sideloading outside G DATA paths, `NOVUpdate.exe` from user-writable paths, XOR artifacts, C2 beacons, typosquatted archives, TCP/IP registry tampering, Defender exclusion abuse, and full drop-to-C2 correlation.
- `Adobe Acrobat Reader Prototype Pollution RCE (CVE-2026-34621)` — CVSS 8.6 zero-day in Adobe Acrobat / Reader, patched Apr 11 via APSB26-43 (priority 1). Prototype Pollution → arbitrary code on PDF open. Disclosed by Haifei Li / EXPMON; active exploitation since Dec 2025. Abuses `util.readFileIntoStream()` for local file exfil and `RSS.addFeed()` for C2. Known SHA256 `65dca34b…d25dde7`. **10 KQL queries** covering the hash, vulnerable-version inventory (Acrobat DC ≤ 26.001.21367, Acrobat 2024 ≤ 24.001.30356), Acrobat spawning LOLBins/shells, anomalous outbound from `AcroRd32`, sensitive local file reads (SSH/AWS/Azure/browser stores), PDF drops from mail/browser, 5-min PDF-open→beacon correlation, and full deliver→open→bad-signal correlation.
- `EDR Killers - Ransomware BYOVD Defense Evasion` — ESET confirms EDR killers (EDRKillShifter, AvNeutralizer/AuKill, Terminator, RealBlindingEDR, GhostDriver/Poortry) are now standard in modern ransomware intrusions. Attackers drop legitimately-signed but vulnerable kernel drivers (`rtcore64.sys`, `gdrv.sys`, `truesight.sys`, `dbutil_2_3.sys`, `mhyprot2.sys`, `procexp152.sys`) and abuse their IOCTLs to kill protected security processes from kernel space. **10 KQL queries** covering vulnerable driver loads, user-writable driver drops, kernel service creation, known EDR-killer binaries, Defender/EDR tampering, mass security-process termination, unsigned kernel loads, registry tampering, and a hash-free behavioural catch-all.
- `Cookie Theft & Session Hijacking` — With Google rolling out Device Bound Session Credentials (DBSC) in Chrome 146, this hunts the attacker side of session hijacking. Targets infostealers (Lumma, RedLine, Raccoon, Vidar, AMOS) reading browser credential stores, `Local State` encryption keys, DPAPI master keys, and cookie databases. **12 KQL queries** covering non-browser credential access, encryption key extraction, DPAPI abuse, staging/archiving, SQLite direct queries, multi-browser-vendor correlation, Entra ID token replay, impossible travel, C2 exfiltration, and full kill-chain correlation.
- `ClickFix macOS Script Editor` — ClickFix social-engineering campaign bypassing Terminal by abusing macOS Script Editor (`osascript`) to deliver Atomic Stealer (AMOS). Victims lured via fake CAPTCHAs and browser error dialogs; steals Keychain credentials, browser passwords, cookies, crypto wallets, and session tokens. **12 KQL queries** covering browser→`osascript` execution, encoded payloads, `curl | sh` delivery, Keychain harvesting, browser credential theft, crypto wallet access, LaunchAgent persistence, Gatekeeper bypass, DMG execution chains, C2 exfiltration, and full kill-chain correlation.
- `Adobe Reader 0-Day PDF Exploit` — Zero-day (CVE-2026-27220) discovered by EXPMON on Mar 26. Malicious PDF abuses JavaScript APIs (`util.readFileIntoStream`, `RSS.addFeed`) to steal local files and fingerprint systems. No user interaction beyond opening the PDF. C2: `169.40.2.68:45191`. **12 KQL queries** covering C2 communication, vulnerable version detection, suspicious Acrobat child processes, file-read anomalies, and multi-signal correlation.
- `ChipSoft HiX EHR` — Response pack following the Apr 7 ransomware attack on ChipSoft, the dominant Dutch healthcare EHR provider (~70% market share). Z-CERT advisory: disconnect VPN immediately. Applicable to any Dutch hospital running HiX in a potentially-exposed configuration. **9 KQL queries** covering software inventory, HiX process detection, VPN connection hunts, post-compromise indicators, and lateral-movement patterns from HiX hosts.
- `EvilTokens & AMOS Stealer` — Dual campaign: OAuth Device Code phishing hijacking enterprise M365 accounts (180+ phishing URLs / week) + AMOS macOS stealer targeting AI developers via ClickFix lures. **12 KQL queries** covering OAuth Device Code anomalies, EvilTokens C2 domains, behavioural patterns, LaunchAgent persistence, Keychain / browser credential harvesting, and cross-platform multi-signal correlation.

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
