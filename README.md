# A full guide on how to perform real world cybersecurity attacks in a VM lab you'll build and document on your own

### Latest Updates (August 10, 2026)
- Added a new Active Directory attack guide: Kerberoasting
- Added Active Directory lab-build guides for creating a low privilege user, creating a service account, and making a service account Kerberoastable
- Redesigned the project layout by separating attacks, lab-build guides, setups, and tricks and methods

This README outlines a hands-on, isolated virtual machine lab designed for practicing network penetration testing and Active Directory attacks. The environment uses Kali Linux as the attacker machine, Metasploitable2 as a vulnerable target, Windows 10 as a victim VM, and pfSense as a gateway/router. The project provides step-by-step setup guides for configuring the attacker and target systems, along with notes on ARP spoofing, MITM attacks, and Kerberoasting. It also includes lab-build guides for creating the Active Directory users and service accounts needed for the Kerberoasting attack. Its primary goal is to deliver practical, hands-on offensive security training to prepare for a professional pentesting career.

The documentation is organized into four sections:

- `attacks/` - attack guides, including DNS spoofing, MITM, and Kerberoasting
- `lab-build/` - guides for building the Active Directory lab and preparing accounts for attacks
- `setups/` - setup guides for Kali Linux and Metasploitable2
- `tricks_and_methods/` - extra techniques and methods
