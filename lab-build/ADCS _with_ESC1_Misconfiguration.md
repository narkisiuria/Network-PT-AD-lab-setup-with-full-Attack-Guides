# Build: ADCS with ESC1 Misconfiguration

Goal: install Certificate Services on the domain, then misconfigure one template so a regular user can request a certificate that lets them authenticate as Domain Admin.

## 1. Install the role
On the DC (or a new joined member server — DC is fine for a lab):
- Server Manager → Add Roles and Features
- Select **Active Directory Certificate Services**
- On the role services screen, check **Certification Authority**
- Finish install, then click the yellow flag → "Configure Active Directory Certificate Services"
- Choose: Standalone setup is NOT what you want — pick **Enterprise CA**, **Root CA**, new private key, default crypto options
- Finish wizard

## 2. Duplicate a vulnerable template
- Open `certtmpl.msc` (Certificate Templates snap-in) on the CA server
- Find **User** template, right-click → Duplicate Template
- Name it something like `VulnTemplate`

## 3. Make it exploitable (ESC1 conditions)
In the new template's properties:
- **Subject Name** tab → select "Supply in the request" (this lets the requester choose ANY name, including a Domain Admin's)
- **Extensions** tab → Application Policies → make sure "Client Authentication" is included (usually is by default on User template)
- **Security** tab → give your low-priv test user (or "Authenticated Users") **Enroll** permission

## 4. Publish the template
- Open `certsrv.msc` on the CA
- Right-click Certificate Templates → New → Certificate Template to Issue
- Select `VulnTemplate`

## 5. Confirm it's live
From Kali, check with Certipy:
```
pip3 install certipy-ad
certipy find -u 'Sima Arusi@ad-lab.local' -p 'User123!' -dc-ip 192.168.56.10
```
Look for `VulnTemplate` flagged as `ESC1` vulnerable in the output.

## Next (attack, not build)
Once confirmed vulnerable, the attack itself (requesting a cert as Domain Admin, then using it to get a TGT) is a separate guide — this file only covers getting the misconfig in place.