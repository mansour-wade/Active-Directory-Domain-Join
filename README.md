# Active Directory Domain Join
 
Domain-joining the Linux and Windows clients built earlier in this lab to the `lab.local` Active Directory domain running on DC01. Covers Rocky Linux via realmd/SSSD, LDAP queries from a joined client, and a live packet capture of the Kerberos/LDAP/DNS traffic during the join. Fedora and Windows 11 join, GPO verification, and final AD-side confirmation are still in progress.
 
**Status: In Progress.** This README documents what has been completed so far and what is still left to do.
 
---
 
## Table of Contents
 
- [What I Built So Far](#what-i-built-so-far)
- [Tech Stack and Environment](#tech-stack-and-environment)
- [Project Files](#project-files)
- [The Build](#the-build)
  * [1. DNS Prerequisite Check on Rocky Linux](#1-dns-prerequisite-check-on-rocky-linux)
  * [2. Installing realmd, SSSD, and adcli](#2-installing-realmd-sssd-and-adcli)
  * [3. Domain Discovery and Join](#3-domain-discovery-and-join)
  * [4. AD Login Verification](#4-ad-login-verification)
  * [5. Packet Capture During Domain Join](#5-packet-capture-during-domain-join)
- [Troubleshooting and Real Issues](#troubleshooting-and-real-issues)
  * [Issue 1: DNS Configured on the Wrong Interface](#issue-1-dns-configured-on-the-wrong-interface)
  * [Issue 2: AD Login Failed With Bare Username](#issue-2-ad-login-failed-with-bare-username)
- [What's Left](#whats-left)
- [What I Learned So Far](#what-i-learned-so-far)
---
 
## What I Built So Far
 
Rocky Linux (`10.0.1.2`) is now fully joined to `lab.local` using realmd and SSSD. Before attempting the join, I confirmed DNS was pointing correctly at DC01 (`10.0.3.10`), including SRV record resolution for `_ldap._tcp` and `_kerberos._tcp`, since `realm join` depends on both to locate the domain controller and authenticate the join itself.
 
After joining, I tested an actual AD login with a domain user and confirmed the UID, GID, and group membership all resolved from AD rather than local `/etc/passwd`. I also left the domain and rejoined it while running a live `tcpdump` capture on the Ubuntu gateway, so the Kerberos (port 88), LDAP (port 389), and DNS (port 53) traffic from a real join is captured in full and available in `configs/domain-join-full.pcap`.
 
Fedora, Windows 11, GPO verification, and the final AD-side confirmation of all joined clients are not done yet. Those are listed under [What's Left](#whats-left).
 
---
 
## Tech Stack and Environment
 
| Component | Specification |
|---|---|
| Host OS | Rocky Linux 9.7 (Blue Onyx) |
| Hypervisor | Oracle VirtualBox |
| Gateway OS | Ubuntu Server |
| Domain Controller | Windows Server 2022 Datacenter Evaluation |
| AD Domain | lab.local |
| Client Being Joined | Rocky Linux 9 (10.0.1.2) |
| Join Method | realmd + SSSD + adcli |
| Packet Capture | tcpdump (Ubuntu gateway, enp0s8) |
 
---
 
## Project Files
 
| File | Source Path | What It Shows |
|---|---|---|
| [domain-join-full.pcap](configs/domain-join-full.pcap) | captured on Ubuntu enp0s8 | Full packet capture of Rocky's domain join, showing DNS SRV lookups, Kerberos AS-REQ/AS-REP exchanges, and LDAP bind/search traffic between Rocky and DC01. Open in Wireshark for protocol-level detail. |
 
---
 
