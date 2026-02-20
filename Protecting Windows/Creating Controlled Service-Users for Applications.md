
# Secure Windows Service + IIS deployment — Step-by-step

> **Scope / intention:** Host a single public site accessible on port **8001** while the backend app runs as a locally bound service under a locked-down service account. Prevent apps from spawning `cmd.exe` / `powershell.exe`. Minimize blast radius if a service is compromised.

---

## Prelim: assumptions & safe defaults

- Windows Server 2019/2022/2025 (commands use modern PowerShell).
    
- You have Administrator privileges on the machine.
    
- NSSM is available (download from nssm.cc) or you can use `sc.exe` / native service creation if you prefer. ([nssm.cc](https://nssm.cc/usage?utm_source=chatgpt.com "NSSM - the Non-Sucking Service Manager"))
    

---

## 0 — High-level architecture (do this BEFORE configuring)

1. Backend application binary (the actual server) listens **only on localhost** on port `9001`.
    
2. IIS will act as the public reverse proxy and accept external traffic on port **8001** (HTTP) and forward to `http://127.0.0.1:9001`.
    
3. One dedicated **service account** `svc_app8001` runs the backend service.
    
4. IIS app pool runs as either `AppPoolIdentity` or a dedicated custom identity — choose consistent account to avoid unnecessary network access. ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities?utm_source=chatgpt.com "Application Pool Identities"))
    

Rationale: Keeps backend unreachable from network; only IIS handles inbound traffic.

---

## 1 — Create the dedicated local service account

**Commands (PowerShell as Admin):**

```powershell
# create local user
$pass = Read-Host -AsSecureString "Enter password for svc_app8001"
New-LocalUser -Name "svc_app8001" -Password $pass -Description "Service account for App8001" -NoPasswordLastSet

# ensure not admin
Remove-LocalGroupMember -Group "Administrators" -Member "svc_app8001" -ErrorAction SilentlyContinue
```

**Also:** Disable interactive logon later via rights assignment (next step).  
**Check:** `Get-LocalUser svc_app8001`

**Why:** Human accounts and service accounts must be separate; do not run services as Administrator. ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities?utm_source=chatgpt.com "Application Pool Identities"))

---

## 2 — Grant minimal required user rights

**Goal:** give `svc_app8001` only `Log on as a service` (and _not_ interactive / RDP).

**Manual GUI:** `secpol.msc → Local Policies → User Rights Assignment → Log on as a service` → Add `svc_app8001`.

**PowerShell (GPO environments require GPO change instead):**

```powershell
# requires secedit /ntrights utility or edit via ntrights.exe (PsExec kit). Prefer GPO in domains.
```

**Verify:** Event logs when service starts; or use `whoami /priv` under that user (after impersonation test).

**Why:** Prevents the account from being used interactively.

---

## 3 — Create application folder and harden NTFS

**Commands:**

```powershell
$folder = "C:\Services\App8001"
New-Item -ItemType Directory -Path $folder -Force

# remove inheritance and set explicit permissions:
icacls $folder /inheritance:r
icacls $folder /grant "svc_app8001:(OI)(CI)F"
icacls $folder /grant "SYSTEM:(OI)(CI)F"
```

> **Note:** In PowerShell, quote the permission string; otherwise PowerShell will parse `(OI)` as an expression. ([Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/icacls?utm_source=chatgpt.com "icacls"))

**Place your app binary** at: `C:\Services\App8001\app.exe` and any config files in that same folder. Only `svc_app8001` + `SYSTEM` should have access.

**Check:** `icacls C:\Services\App8001` should show `svc_app8001:(OI)(CI)(F)`.

**Why:** Prevents other users/services from reading/writing app files.

---

## 4 — Install the app as a Windows Service with NSSM

**Why NSSM:** simple, reliable wrapper for running any exe as a service; supports setting the service account. ([nssm.cc](https://nssm.cc/usage?utm_source=chatgpt.com "NSSM - the Non-Sucking Service Manager"))

**Commands (assumes nssm.exe is in PATH):**

```powershell
# Install the service using nssm
nssm install App8001 "C:\Services\App8001\app.exe"
# set working directory (optional)
nssm set App8001 AppDirectory "C:\Services\App8001"

# Set the service to run as svc_app8001
nssm set App8001 ObjectName ".\svc_app8001" "THE_PASSWORD_FOR_SVC"  # pass actual password
# configure restart behaviour
nssm set App8001 Start SERVICE_AUTO_START
nssm set App8001 AppNoConsole 1
```

**Start service:**

```powershell
net start App8001
```

**Verify:** `sc query App8001` or check `Get-Service App8001`. Check that the app is listening on `127.0.0.1:9001` (or whichever port you configured):

```powershell
netstat -ano | Select-String ":9001"
```

**Pitfalls:** NSSM `ObjectName` requires the password argument; use secure automation carefully. ([nssm.cc](https://nssm.cc/commands?utm_source=chatgpt.com "Managing services from the command line"))

---

## 5 — Windows Firewall: allow only IIS public port and block direct public backend port

**Commands:**

```powershell
# Allow inbound on 8001 (IIS)
New-NetFirewallRule -DisplayName "IIS_App8001_Inbound" -Direction Inbound -Protocol TCP -LocalPort 8001 -Action Allow -Profile Any -Description "Allow HTTP for App8001"

# Block external access to backend 9001 (explicit block)
New-NetFirewallRule -DisplayName "Block_App8001_Backend_External" -Direction Inbound -Protocol TCP -LocalPort 9001 -Action Block -Profile Any -RemoteAddress Any
```

**Tip:** Where possible, bind backend to `127.0.0.1` only; then network-level block is extra protection. ([Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2025-ps&utm_source=chatgpt.com "New-NetFirewallRule (NetSecurity)"))

**Verify:** `Get-NetFirewallRule -DisplayName "IIS_App8001_Inbound"` and `Test-NetConnection -ComputerName localhost -Port 9001`.

---

## 6 — Install IIS components + URL Rewrite + ARR (reverse proxy pieces)

**Why:** URL Rewrite + ARR provide IIS-level reverse proxy. These are not installed by default. ([iis.net](https://www.iis.net/downloads/microsoft/url-rewrite?utm_source=chatgpt.com "URL Rewrite : The Official Microsoft IIS Site"))

**Steps (offline/manual recommended for servers):**

1. Download & install **URL Rewrite 2.x** (MSI). Official page: URL Rewrite on iis.net. ([iis.net](https://www.iis.net/downloads/microsoft/url-rewrite?utm_source=chatgpt.com "URL Rewrite : The Official Microsoft IIS Site"))
    
2. Download & install **Application Request Routing (ARR)** (ARR 3.x). ([iis.net](https://www.iis.net/downloads/microsoft/application-request-routing?utm_source=chatgpt.com "Application Request Routing : The Official Microsoft IIS Site"))
    
3. In IIS Manager → select server → **Application Request Routing Cache** → **Server Proxy Settings** → **Enable proxy**.
    

**PowerShell (enable proxy setting):**

```powershell
Import-Module WebAdministration
Set-WebConfigurationProperty -pspath 'MACHINE/WEBROOT/APPHOST' -filter 'system.webServer/proxy' -name 'enabled' -value 'True'
```

**Verify:** In IIS Manager you should now see **URL Rewrite** at the server and site level, and **ARR** icons. Test by creating a rewrite rule that proxies to `http://127.0.0.1:9001`.

**Important port note:** Do **not** bind IIS site to the same port as backend. Use IIS port 8001 and backend 9001 (no collision). ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/extensions/url-rewrite-module/reverse-proxy-with-url-rewrite-v2-and-application-request-routing?utm_source=chatgpt.com "Reverse Proxy with URL Rewrite v2 and Application ..."))

---

## 7 — Create IIS App Pool and Site, set identity

**Create App Pool:**

- Name: `App8001Pool`
    
- .NET CLR: **No Managed Code** (unless app needs it)
    
- Identity: `ApplicationPoolIdentity` **or** custom account `.\svc_app8001` if the site must access the same resources as the service.
    

**Steps via PowerShell:**

```powershell
Import-Module WebAdministration
New-WebAppPool -Name "App8001Pool"
Set-ItemProperty IIS:\AppPools\App8001Pool -Name processModel.identityType -Value 3  # 3 = SpecificUser
# set custom account:
Set-ItemProperty IIS:\AppPools\App8001Pool -Name processModel.userName -Value ".\svc_app8001"
Set-ItemProperty IIS:\AppPools\App8001Pool -Name processModel.password -Value "THE_PASSWORD"
```

**Create site (HTTP port 8001):**

```powershell
New-Item -Path "IIS:\Sites\App8001" -PhysicalPath "C:\inetpub\app8001" -Bindings @{protocol="http";bindingInformation="*:8001:"}
Set-ItemProperty IIS:\Sites\App8001 -Name applicationPool -Value "App8001Pool"
```

**NTFS permissions for site folder:**

```powershell
icacls "C:\inetpub\app8001" /inheritance:r
icacls "C:\inetpub\app8001" /grant "svc_app8001:(OI)(CI)RX"
icacls "C:\inetpub\app8001" /grant "SYSTEM:(OI)(CI)F"
```

**Verify:** Browse `http://localhost:8001` locally on server. Check application pool worker process runs as the configured identity (Task Manager → Details → w3wp.exe → token user).

**Why:** Separate app pools isolate sites and reduce lateral movement. ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities?utm_source=chatgpt.com "Application Pool Identities"))

---

## 8 — Create URL Rewrite rule to proxy to backend

**IIS Manager GUI:** Site → URL Rewrite → Add Rule(s) → Reverse Proxy (if available) or Blank Rule:

**Blank rule sample:**

- Pattern: `(.*)`
    
- Action type: **Rewrite**
    
- Action URL: `http://127.0.0.1:9001/{R:1}`
    

If using ARR, enable proxying so ARR will forward.

**Test:** `curl http://localhost:8001/` → route should reach backend app.

**Warning:** Do not configure the backend to listen on a public interface. Keep it `127.0.0.1`.

---

## 9 — Harden IIS + service behavior

- **Disable unnecessary modules** in IIS (WebDAV, TRACE, other modules you do not need).
    
- **Enable request filtering** to block suspicious verbs or large payloads.
    
- **Set timeouts** and **rapid-fail protection** for app pool (Advanced Settings → Rapid-Fail Protection).
    
- **Limit file upload size** and enable `requestLimits` in web.config if needed.
    
- **Log everything** (IIS logs, application logs).
    

**Why:** Minimizes attack surface and gives visibility for containment.

---

## 10 — Prevent execution of cmd/powershell (App Control)

You requested apps must NEVER be able to run `cmd.exe` or PowerShell even if user credentials exist. There are two enterprise-grade options:

### Option A — WDAC / App Control for Business (recommended for strict enforcement)

- Create a WDAC base policy in audit mode (do not enforce until you validate). Use `New-CIPolicy` on a reference machine, test in Audit, iterate, then enforce. WDAC blocks execution at kernel level and is the most robust option. ([Microsoft Learn](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/design/create-appcontrol-policy-using-reference-computer?utm_source=chatgpt.com "Create an App Control policy using a reference computer"))
    

**High level commands:**

```powershell
# On a reference machine:
New-CIPolicy -Level Publisher -FilePath C:\WDAC\wdac-base.xml -Fallback Hash
ConvertFrom-CIPolicy -XmlFilePath C:\WDAC\wdac-base.xml -BinaryFilePath C:\WDAC\wdac-base.bin

# Deploy in audit first:
Copy-Item C:\WDAC\wdac-base.bin C:\Windows\System32\CodeIntegrity\SIPolicy.p7b
# Reboot, monitor CodeIntegrity logs
```

**To block shells:** explicitly add deny rules for the shell paths:

- `C:\Windows\System32\cmd.exe`
    
- `C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe`
    
- `C:\Program Files\PowerShell\7\pwsh.exe`
    

**Why WDAC:** kernel-enforced prevents process spawns from usermode processes; not bypassable by typical malware.

### Option B — AppLocker (if WDAC not available)

- Enable AppLocker via `secpol.msc` or Group Policy.
    
- Create default allow rules for Windows components, then create **deny** rules for the shell paths for **Everyone**. Enforce after testing. AppLocker still gives strong control but is less absolute than WDAC.
    

**Commands to start AppLocker service:**

```powershell
sc config AppIDSvc start= auto
sc start AppIDSvc
```

**Caveat:** AppLocker/WDAC require testing; they can break legitimate admin tasks. Always deploy audit-first. See Microsoft guidance. ([Microsoft Learn](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/design/create-appcontrol-policy-using-reference-computer?utm_source=chatgpt.com "Create an App Control policy using a reference computer"))

---

## 11 — Additional host hardening (quick checklist)

- Enable **Windows Defender / EDR** and apply Attack Surface Reduction (ASR) rules.
    
- Enforce strong passwords / rotate service account credentials (use a secrets manager).
    
- Disable NTLM where possible; prefer Kerberos in domain environments.
    
- Regularly patch OS/IIS/app; automate via WSUS or equivalent.
    
- Use TLS for public traffic (bind port 8001 → prefer HTTPS 443, terminate TLS at IIS; get a cert via CA or ACME).
    
- Centralize logs to SIEM for alerting (login failures, unexpected service starts, CodeIntegrity events).
    

---

## 12 — Quick verification & tests (must perform)

1. From remote client: `curl http://<server-ip>:8001` → should reach site.
    
2. From remote client: `nc -vz <server-ip> 9001` → should **fail** (backend not reachable externally).
    
3. On server: `netstat -ano | findstr 9001` → shows listener only on `127.0.0.1:9001`.
    
4. App Pool worker runs under intended identity — check Task Manager or `Get-Process -IncludeUserName` (requires admin).
    
5. Attempt to run `powershell.exe` under the service account (simulate) — should be blocked once App Control enforced.
    
6. Check CodeIntegrity logs if WDAC audit mode used.
    

---

## 13 — Rollout plan (how to deploy safely)

1. **Test environment**: build the entire stack on a staging server; enable WDAC/AppLocker in **audit** mode.
    
2. Run for 7–14 days; collect deny/audit logs and whitelist legitimate apps (if any).
    
3. Harden GPOs and rollout to production servers in waves.
    
4. Have an emergency rollback plan (how to remove SIPolicy.p7b or disable AppLocker).
    
5. Maintain a documented change record (policy versions, whitelist hashes).
    

---

## 14 — Short troubleshooting tips

- App Pool fails to start → check Event Viewer: `System` and `Application` logs (WAS / W3SVC-WP).
    
- `502` from IIS reverse proxy → backend not listening or ARR proxy disabled. Check ARR Server Proxy Settings. ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/extensions/configuring-application-request-routing-arr/creating-a-forward-proxy-using-application-request-routing?utm_source=chatgpt.com "Creating a Forward Proxy Using Application Request Routing"))
    
- `403/500.19` → NTFS permission or config file issue; check `icacls` and `web.config`.
    

---

# References (authoritative)

These are the key sources to validate the steps and for deeper reading:

- IIS Application Pool Identities and security — Microsoft docs. ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities?utm_source=chatgpt.com "Application Pool Identities"))
    
- URL Rewrite Module — official IIS site / Microsoft docs. ([iis.net](https://www.iis.net/downloads/microsoft/url-rewrite?utm_source=chatgpt.com "URL Rewrite : The Official Microsoft IIS Site"))
    
- Reverse proxy with URL Rewrite + ARR — Microsoft walkthrough. ([Microsoft Learn](https://learn.microsoft.com/en-us/iis/extensions/url-rewrite-module/reverse-proxy-with-url-rewrite-v2-and-application-request-routing?utm_source=chatgpt.com "Reverse Proxy with URL Rewrite v2 and Application ..."))
    
- New-NetFirewallRule / Windows Firewall rule guidance — Microsoft docs. ([Microsoft Learn](https://learn.microsoft.com/en-us/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2025-ps&utm_source=chatgpt.com "New-NetFirewallRule (NetSecurity)"))
    
- App Control / WDAC / AppLocker guidance (create policy using reference computer) — Microsoft docs. ([Microsoft Learn](https://learn.microsoft.com/en-us/windows/security/application-security/application-control/app-control-for-business/design/create-appcontrol-policy-using-reference-computer?utm_source=chatgpt.com "Create an App Control policy using a reference computer"))
    
- NSSM usage & commands — NSSM project site. ([nssm.cc](https://nssm.cc/usage?utm_source=chatgpt.com "NSSM - the Non-Sucking Service Manager"))
    
- icacls reference — Microsoft docs (NTFS permissions). ([Microsoft Learn](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/icacls?utm_source=chatgpt.com "icacls"))
    

---

# Final blunt advice

- **Do not** flip WDAC/AppLocker to enforcement without audit logs and a tested rollback — you will block legitimate tasks.
    
- Keep the backend **localhost only**. Do not expose backend port publicly.
    
- Rotate service account credentials and store them in a secrets manager (HashiCorp Vault / Azure Key Vault / CyberArk) rather than plaintext.
    
- Use TLS for public traffic and keep port 8001 limited to only necessary external clients (restrict RemoteAddress in firewall when possible).
    

---

If you want, I can:

- Produce a **ready-to-run PowerShell script** that implements these steps (including NSSM installation, icacls, firewall rules, IIS configuration, URL Rewrite/ARR post-install checks) — tested for idempotency.
    
- Or produce a **two-page policy document** suitable for sign-off by your security team.
    

Which of those do you want next?