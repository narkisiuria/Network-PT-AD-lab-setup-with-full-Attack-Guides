# Full MD Guides On How To Set Up A PT-AD Lab And Preform Offensive Cyber Attacks On It  

A full guide chain on how to build an isolated penetration-testing and Active Directory lab from scratch, and how to carry out real attacks against it. **PT-AD Lab** documents an entire hands-on build, from network setup to a full Kerberoasting and BloodHound attack chain, aimed at real-world offensive security training.

---

## 🚀 Key Features

### 🖥️ Full Isolated Lab Environment
*   **Attacker Machine:** Kali Linux, fully configured for MITM, spoofing, and AD attacks.
*   **Vulnerable Targets:** Metasploitable2 for classic network exploitation, a domain-joined Windows 10 victim VM for AD attacks.
*   **Real Gateway:** pfSense configured as the lab's actual gateway and router, with DHCP handled end to end.

### 🎯 Real Attack Guides
*   **Network Attacks:** ARP spoofing, MITM, DNS spoofing, and SSL stripping, all run and documented live in the lab.
*   **Active Directory Attacks:** Full Kerberoasting chain (SPN setup, ticket extraction with Rubeus, offline cracking with hashcat) and BloodHound-based attack path mapping.
*   **Post-Exploitation Tricks:** Techniques like getting a payload to run on Windows startup.

### 🏗️ Lab-Build Guides
*   **Active Directory From Scratch:** Domain Controller setup, domain-joining a client, and creating both low-privilege and Kerberoastable service accounts.
*   **Step-by-Step, Not Automated:** The AD lab is built manually before ever touching automated tools like GOAD, so every step is actually understood.

---

## 📂 Project Structure

```text
PT-AD-Lab/
├── attacks/                # Attack guides (DNS spoofing, MITM, Kerberoasting, BloodHound)
├── lab-build/               # Guides for building the AD lab and preparing attack accounts
├── setups/                  # Setup guides for Kali Linux and Metasploitable2
├── tricks_and_methods/      # Additional techniques and post-exploitation tricks
└── README.md
```

---

## 🛠️ Requirements and System Setup

*   **Virtualization:** VirtualBox (or equivalent) capable of running multiple VMs at once.
*   **VMs Required:** Kali Linux, Metasploitable2, Windows 10 Pro, Windows Server 2022, pfSense.
*   **Networking:** An isolated internal network, with pfSense acting as gateway.

---

## 📰 Latest Updates

### August 16, 2026
*   Added an Active Directory BloodHound attack guide: `attacks/attack_BloodHound.md`
*   Added a Windows startup-payload technique guide: `tricks_and_methods/how_to_make_a_payload_run_on_startup_WIN.md`

### August 10, 2026
*   Added a new Active Directory attack guide: Kerberoasting
*   Added Active Directory lab-build guides for creating a low-privilege user, creating a service account, and making a service account Kerberoastable
*   Redesigned the project layout by separating attacks, lab-build guides, setups, and tricks and methods

---

## ⚠️ Warning

This lab is for offensive security practice in an isolated, self-owned environment only. None of these techniques should be run against systems you do not own or are not explicitly authorized to test.

---

## 🗺️ Roadmap

*   [x] Full network lab (Kali, Metasploitable2, Win10 victim, pfSense gateway)
*   [x] ARP spoofing, MITM, DNS spoofing, SSL stripping
*   [x] Active Directory lab built manually (DC, domain-joined client, low-priv user, Kerberoastable service account)
*   [x] Full Kerberoasting chain (Rubeus extraction, hashcat cracking)
*   [x] BloodHound data collection and graph exploration
*   [ ] Resume BloodHound attack-path finding (svc-sql to Domain Admins)
*   [ ] Explore GOAD's automated AD lab deployment
