# Build: Sysmon Logging (Blue-Team Side)

Goal: get real, detailed Windows event logging on the DC and Win10 client, so you can go back and see what your existing attacks (Kerberoasting, LLMNR poisoning) actually look like from a defender's view.

## 1. Download Sysmon
On each Windows box you want logging on (DC + Win10 client), download Sysinternals Sysmon from Microsoft's site (sysinternals sysmon page) — grab the zip, extract it.

## 2. Get a good starting config
Don't run Sysmon with defaults — use a known-good community config, e.g. **SwiftOnSecurity's sysmon-config** (a well-known, widely-used baseline XML). Download the `sysmonconfig-export.xml` file from that project.

## 3. Install with the config
In an elevated PowerShell/cmd, in the folder with both `Sysmon64.exe` and the config XML:
```
Sysmon64.exe -accepteula -i sysmonconfig-export.xml
```

## 4. Confirm it's running
```
Get-Service Sysmon64
```
Should show status: Running

## 5. Where to look at logs
Event Viewer →
```
Applications and Services Logs → Microsoft → Windows → Sysmon → Operational
```

## 6. Re-run an existing attack and watch
- Re-run Kerberoasting (Rubeus) from Sima's session on the Win10 client
- Check Sysmon logs on that client for Event ID 1 (process creation) — you should see Rubeus.exe launching, with full command line logged
- On the DC, check standard Windows Security logs for Event ID 4769 (Kerberos service ticket requests) — this is the actual detection signal real SOCs use for Kerberoasting

## Why this matters
This turns the lab from "attack only" into "attack + what it looks like when caught" — which is a much stronger story for interviews, and teaches you what real detections rely on.