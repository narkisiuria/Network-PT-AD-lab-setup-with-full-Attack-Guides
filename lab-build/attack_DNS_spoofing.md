DNS spoofing (or DNS cache poisoning) is a cyberattack that alters the Domain Name System (DNS) records, silently redirecting users from legitimate websites to malicious, attacker-controlled clones. The goal is to steal login credentials, distribute malware, or intercept sensitive data.

*HOW IT WORKS*
The Domain Name System acts as the "phone book" of the internet, translating readable website addresses (e.g., example.com) into numerical IP addresses.The Request: When you type a website name, your browser queries a DNS server.The Manipulation: An attacker intercepts this query and injects false information, causing the server to return the wrong IP address.The Redirection: Without knowing, your browser connects to a fraudulent site that looks and feels like the real thing, while the browser address bar continues to display the correct, intended URL.

to start the DNS spoofing attack first boot up 3 machines:

1: kali attacker machine.
2: win10 victim machine.
3: pfsense router.

in order to start the attack you have to get the packets of the victim's machine which means you have to be the first complete the MITM attack and stand between the router and the victim machine and see thire packets.
so if you are still not getting the packets of the win10 machine/victim machine, learn how to preform the MITM attack by reading and completing what is demanded in the attack_MITM.md file.

After you have done that and you are officialy the man in the middle, in order to start the attack you have to install this tool by typing:

```bash
sudo apt install ettercap-text-only -y
```

*WHAT THIS TOOL ACTUALY DOES*
Ettercap does the DNS lie for you:
it watches for the victim's DNS questions ("what's the IP for x website?"), and answers with a fake IP before the real DNS server gets the chance.

