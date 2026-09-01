# Build: Second Server for Lateral Movement Practice

Goal: add a second domain-joined machine where a regular user has been mistakenly given local admin rights — a very common real-world misconfig that enables lateral movement.

## 1. Build the VM
- New VM, install Windows Server (any edition — 2019/2022) or another Windows 10 Pro instance
- Give it a static IP on the same lab subnet (e.g. 192.168.56.20)

## 2. Join it to the domain
- System Properties → Change → Domain: `ad-lab.local`
- Authenticate with a Domain Admin account when prompted
- Reboot

## 3. Create the misconfiguration
On this new server, add your existing low-priv user to the **local Administrators group** (this is the mistake a real sysadmin might make — e.g. "just to fix a printer issue" and never removed):
```
net localgroup administrators AD-LAB\SimaArusi /add
```
(adjust username format if needed — try `"AD-LAB\Sima Arusi"` with quotes if it has a space)

## 4. Enable WinRM (for PSRemoting-based lateral movement practice)
On the new server, run as admin:
```
Enable-PSRemoting -Force
```

## 5. Confirm the misconfig from Kali
Using Sima's cracked/known password, test access with a tool like evil-winrm:
```
pip3 install evil-winrm
evil-winrm -i 192.168.56.20 -u 'Sima Arusi' -p 'User123!'
```
If it drops you into a shell, the misconfig works — Sima (a normal domain user) has full admin on this box even though she's nobody special in AD.

## What this unlocks (attacks, separate guides)
- Dumping local SAM/LSASS on this box for more creds
- Practicing lateral movement techniques (WinRM, WMI, PsExec-style)
- If a Domain Admin ever logs into this box, harvesting their session/token becomes possible too