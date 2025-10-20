 Scan Summary 

I ran a TCP SYN scan against the target IP 10.170.142.111. The Nmap output:

# Nmap 7.98 scan initiated Mon Oct 20 21:48:40 2025 as: "C:\Program Files (x86)\Nmap\nmap.exe" -sS -oN scan_results.txt 10.170.142.111
Nmap scan report for 10.170.142.111
Host is up (0.00099s latency).
Not shown: 995 closed tcp ports (reset)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3580/tcp open  nati-svrloc
5432/tcp open  postgresql

# Nmap done at Mon Oct 20 21:48:41 2025 -- 1 IP address (1 host up) scanned in 1.12 seconds

---
🧾 Commands Used
Performed TCP SYN scan:


nmap -sS 10.170.142.111 -oN scan_results.txt

Saved output as scan_results.txt (attached/uploaded).



---
 Open Ports — Analysis & Risks

Port	Service (as reported)	Typical Use	Immediate Risk / Notes

135	msrpc	Microsoft RPC endpoint mapper	Often used for Windows RPC; can be targeted for remote code execution or information leakage if exposed.

139	netbios-ssn	NetBIOS Session Service (file/printer sharing legacy)	Legacy SMB/NetBIOS — can leak hostnames/shares; susceptible to enumeration and SMB-based attacks.

445	microsoft-ds	SMB over TCP (modern file/printer sharing)	High-risk if exposed to untrusted networks — common vector for ransomware and remote code execution (e.g., EternalBlue-era exploits).

3580	nati-svrloc	(nati-svrloc) — vendor/service-specific	Uncommon; investigate which application is listening on this port on the host. Could be a network service for a proprietary app — check running processes.

5432	postgresql	PostgreSQL database server	Database exposed — if authentication is weak or default configs used, attacker may gain DB access and exfiltrate data. Should not be open to untrusted networks.



---
Recommendations 

1. Restrict access with a firewall

Block ports 135, 139, 445, 5432, and 3580 from the internet / untrusted subnets. Allow only trusted management IPs if remote access is required.



2. Harden services

For SMB (ports 139/445): disable SMBv1, apply latest security patches, and avoid exposing to WAN.

For PostgreSQL (5432): require strong passwords, use SSL, and bind postgresql.conf to localhost or private management network if remote access isn't needed.



3. Investigate unknown service (3580)

On the host, run: netstat -ano | findstr 3580 (Windows) or ss -ltnp | grep 3580 (Linux) to identify the process and application that owns the port. Patch or reconfigure if unnecessary.



4. Patch & update

Ensure the OS and all network-facing applications are up to date with security patches.



5. Use least privilege & authentication

Disable services not required. Enforce strong auth (SSH keys, database users with minimal privileges).



6. Monitor & log

Enable logging and monitor for suspicious connections to these ports. Consider IDS/IPS rules for anomalous SMB or RPC traffic.



7. Network segmentation

Place critical services (databases, file shares) on isolated subnets and require VPN for admin access.
