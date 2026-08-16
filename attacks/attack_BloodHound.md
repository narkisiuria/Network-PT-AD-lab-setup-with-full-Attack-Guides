# BloodHound - AD Recon & Attack Path Mapping

## Goal
Map Active Directory relationships (users, groups, computers, permissions) to find attack paths toward Domain Admin.

## Tools Used
- **BloodHound.py** — Python collector, runs on Kali, pulls AD data over LDAP using domain creds
- **BloodHound CE** — graph visualization app (Docker: app-db, graph-db, bloodhound containers)
- **Docker / docker-compose** — runs BloodHound CE locally

## Environment
- Domain: `ad-lab.local`
- DC: `192.168.56.10`
- Collector account: low-priv domain user `Sima Arusi`

## Steps

### 1. Install collector
```
pip3 install bloodhound
```

### 2. Collect AD data (from Kali, remote, no SharpHound/Windows needed)
```
bloodhound-python -d ad-lab.local -u "Sima Arusi" -p User123! -c All -w 20 -ns 192.168.56.10
```
- `-c All` = collect every data type (users, groups, computers, GPOs, OUs, trusts, sessions, ACLs)
- `-ns` = use DC as DNS server for name resolution (avoids needing a Kerberos realm entry manually)

Result: 6 users, 52 groups, 2 computers, 19 containers, 1 OU → 7 JSON files.

### 3. Stand up BloodHound CE
```
sudo apt install docker.io
sudo systemctl start docker
docker compose up -d
```
Get initial admin password:
```
docker compose logs bloodhound | grep -i password
```

### 4. Ingest data
Zip the JSON files, upload via BloodHound CE web UI (`http://localhost:8080` → Quick Upload):
```
zip bloodhound_data.zip *.json
```

### 5. Analyze
- Search a compromised/owned account (e.g. `svc-sql`, already Kerberoasted — see kerberoasting notes)
- Use **Pathfinding** tab: set `svc-sql` as start, `DOMAIN ADMINS` as end
- BloodHound traces the shortest chain of abusable relationships (group memberships, ACL rights like GenericAll/WriteOwner, admin sessions) connecting them

## Key Lesson
BloodHound isn't for browsing the whole domain — it's for asking one specific question: "given this account I control, what's my path to Domain Admin?"

## Status
Environment built, data collected + ingested successfully. Pathfinding from `svc-sql` to Domain Admins queued as next step (Kali resource-limited, paused for now).