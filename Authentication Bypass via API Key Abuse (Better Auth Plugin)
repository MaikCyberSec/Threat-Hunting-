Detection Guidance: Authentication Bypass via API Key Abuse (Better Auth Plugin)

A recently disclosed vulnerability in the Better Auth API Keys plugin allows attackers to generate privileged API keys without proper authentication by supplying a userId in the request body.

If exploited, this can result in:

🔐 Authentication bypass

👤 Account impersonation

📈 Privilege escalation

🔑 Long-lived API key abuse

This post provides Microsoft Defender Advanced Hunting (KQL) queries to help detect potential exploitation attempts.

Platform:

Microsoft Defender for Endpoint

Microsoft 365 Defender

🔎 Detection 1: Unauthenticated Requests Containing userId in Body

This hunt identifies suspicious API key creation attempts where:

The request targets API key endpoints

The body contains userId

There is no authenticated account context

DeviceNetworkEvents
| where Timestamp > ago(7d)
| where RemoteUrl contains "/api-key" or RemoteUrl contains "/apikey"
| where HttpRequestMethod == "POST"
| where AdditionalFields contains "userId"
| where isnull(InitiatingProcessAccountName)
| project Timestamp, DeviceName, RemoteIP, RemoteUrl, AdditionalFields
| order by Timestamp desc

🔍 What to Investigate

Requests from unfamiliar IP addresses

High-frequency POST attempts

API key creation outside business hours

No corresponding successful authentication event

⚠️ Note: This requires HTTP body logging or proxy log ingestion into Defender.

🔎 Detection 2: Suspicious Privilege Escalation Activity

If an attacker successfully creates a privileged API key, it may be used for service-style logons or elevated operations.

This hunt detects abnormal service logons or elevated access behavior:

IdentityLogonEvents
| where Timestamp > ago(7d)
| where LogonType == "Service"
| summarize LogonCount = count() by AccountName, DeviceName
| where LogonCount > 10
| order by LogonCount desc

🔍 What to Investigate
Accounts that normally do not perform service logons
Sudden spike in service authentication events
Newly observed service accounts
API/application accounts behaving interactively

🎯 Advanced Correlation (Recommended)

Correlate API key creation with privilege escalation within a short timeframe:

let SuspiciousKeyCreation =
DeviceNetworkEvents
| where Timestamp > ago(7d)
| where RemoteUrl contains "/api-key"
| where HttpRequestMethod == "POST"
| where AdditionalFields contains "userId"
| project CreationTime = Timestamp, DeviceName, RemoteIP;

IdentityLogonEvents
| where Timestamp > ago(7d)
| join kind=inner SuspiciousKeyCreation on DeviceName
| where Timestamp between (CreationTime .. CreationTime + 1h)
| project Timestamp, AccountName, DeviceName, RemoteIP


This detects:
API key abuse followed by service logon
Rapid post-exploitation activity
Lateral movement attempts

🛡 Defensive Recommendations
✅ Upgrade Better Auth to the patched version
🔄 Rotate all API keys created via the vulnerable plugin
📊 Enable HTTP request body logging
📥 Ingest application and proxy logs into Defender
🚨 Convert hunts into custom detection rules

🧠 MITRE ATT&CK Mapping
T1078 – Valid Accounts
T1134 – Access Token Manipulation
T1098 – Account Manipulation
T1550 – Use of Authentication Tokens

If you’re running Better Auth in production and relying on API keys for automation or service-to-service communication, I strongly recommend validating your telemetry coverage at the application layer.

Detection is only possible if the logs exist.
