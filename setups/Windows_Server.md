# Windows Server 2022 (Domain Controller) Setup

Role: the Active Directory Domain Controller for the lab domain `ad-lab.local`.

## 1. Create the VM
- New VM, Windows Server 2022, attach a valid install ISO (a corrupted/bad ISO download caused real problems in this build — verify checksum or re-download if install fails oddly)
- Attach it to the lab internal network (same subnet as pfSense LAN, 192.168.56.0/24)
- **Check the boot order** and make sure the optical drive is actually attached before booting the installer — both were real issues hit during this build

## 2. Install Windows Server, set static IP
- After install, set a static IP on this box, e.g. `192.168.56.10`
- Set its own IP as the **preferred DNS server** (a DC should always point DNS at itself, not the router/gateway)

## 3. Promote to Domain Controller
- Server Manager → Add Roles and Features → **Active Directory Domain Services**
- After install, click the yellow flag → "Promote this server to a domain controller"
- Choose: **Add a new forest**
- Root domain name: `ad-lab.local`
- Set Directory Services Restore Mode (DSRM) password
- Accept defaults for NetBIOS name, DB/log paths, finish and reboot

## 4. Fix clock skew (real issue hit in this lab)
Kerberos fails if client and DC clocks differ by more than ~5 minutes. This lab hit a ~9 hour mismatch that broke domain login.
- Check DC time: `w32tm /query /status` or just Settings → Date & Time
- Sync manually if needed, or set correct timezone before joining any clients

## 5. Confirm it's working
- Log in as `AD-LAB\Administrator`
- Run:
```
whoami /groups
```
Should show Domain Admins, Enterprise Admins, Schema Admins.

## Result in this lab
- Domain: `ad-lab.local`
- DC IP: `192.168.56.10`