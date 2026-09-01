# win10PRO-victim Setup

Role: domain-joined client machine, used as the "victim" for attacks run from Kali against real domain sessions.

## 1. Use Windows 10 **Pro**, not Home
Windows 10 Home cannot join a domain at all — this was hit and confirmed during this build. Get a Pro ISO instead.

## 2. Create the VM
- New VM, attach the Win10 Pro ISO
- Attach it to the lab internal network (192.168.56.0/24, same as the DC)
- Same boot-order/optical-drive checks as the DC — confirm the ISO is actually attached and boots before install

## 3. Install Windows, set networking
- After install, confirm the box gets an IP on the lab subnet (via pfSense DHCP, or set static)
- Set DNS to point at the DC: `192.168.56.10` (domain join needs to resolve the domain via the DC's DNS, not any other DNS server)

## 4. Join the domain
- System Properties → Change → Domain
- Enter: `ad-lab.local`
- Authenticate with `AD-LAB\Administrator` (or another Domain Admin account) when prompted
- Reboot when asked

## 5. Fix Kerberos clock skew if login fails
Same issue as the DC setup — if this VM's clock is off from the DC by more than a few minutes, domain login will fail with a Kerberos error even with correct creds. Sync/set the clock correctly.

## 6. Confirm domain join worked
- Log in with a domain account (e.g. `AD-LAB\Administrator`, or later the low-priv test user)
- Run:
```
whoami /groups
```
Should show AD-LAB domain groups.

## Result in this lab
- Hostname: DESKTOP-TU9VN6B (or similar, auto-generated)
- Domain-joined to `ad-lab.local`
- Used later as the machine where Sima Arusi's low-priv session runs (Kerberoasting, LLMNR poisoning, BloodHound collection all triggered from here)