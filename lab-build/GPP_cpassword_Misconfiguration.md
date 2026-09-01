# Build: GPP cpassword Misconfiguration

Goal: create a Group Policy Preference that stores a password, so it lands in a policy XML file with a reversibly-encrypted password (cpassword) — a real (old but still-found) misconfig class.

## 1. Open Group Policy Management
On the DC: `gpmc.msc`

## 2. Create a new GPO
- Right-click your domain (or an OU with the Win10 client in it) → "Create a GPO in this domain, and Link it here"
- Name it something like `MappedDrive-Policy`

## 3. Add a Preference that uses stored creds
Edit the GPO → go to:
```
User Configuration → Preferences → Windows Settings → Drive Maps
```
- Right-click → New → Mapped Drive
- Set a fake path like `\\DC01\share`
- Under "General" tab, set **"Connect as"** with a username and password (any test creds, e.g. a fake `gppuser` / `Summer2026!`)
- Save

(Alternative that also works: Control Panel Settings → Local Users and Groups → New Local User, with a password set — same cpassword mechanism.)

## 4. Force the policy to write to SYSVOL
- Run `gpupdate /force` on the DC, or just wait — GPO changes replicate to SYSVOL automatically
- The password gets stored, AES-encrypted, in an XML file under:
```
\\ad-lab.local\SYSVOL\ad-lab.local\Policies\{GUID}\User\Preferences\Drives\Drives.xml
```

## 5. Confirm it's there
From any domain-joined machine (or even unauthenticated SYSVOL read, which is often allowed):
```
findstr /S cpassword \\ad-lab.local\SYSVOL\ad-lab.local\Policies\*.xml
```
You should see a `cpassword="..."` attribute in the XML.

## Next (attack, not build)
Decrypting it is a separate step — Microsoft published the private AES key years ago after this was reported, so any cpassword string can be decrypted with public tools (e.g. `gpp-decrypt` on Kali). That's the attack guide, not this one.