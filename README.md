# Cyber Gymnasium for Security Courses

**NET 4901A Capstone Final Report — Winter 2024**
Algonquin College / Carleton University — Networking Technology Program
Supervisor: Ola Mohammed
Team: Anas Ahmed, Evan McNeil, Jacob Moores, Yousuf Muse
Corporate partners: General Dynamics Mission Systems Canada (GDMS-C), Field Effect

## Overview

A pair of hands-on cybersecurity labs built on a Cyber Range platform, designed to slot directly into the Networking Technology curriculum (NET 4011 — Advanced Topics in Networking Security). The goal was to give students a safe, guided environment to launch, detect, and mitigate two real attack techniques, with clear step-by-step instructions written from a student's-eye view rather than a vague assignment sheet.

The team split into two sub-groups — one per lab — so each lab could be tested "blind" by teammates who hadn't built it, catching unclear instructions before they reached real students.

## Lab 1 — SSH Brute Force Detection, Mitigation & Privilege Escalation

**Environment:** Kali Linux (attacker) to Ubuntu VM (target)

**Attack.** Hydra, a parallelized login-cracking tool bundled with Kali, is used to brute-force SSH credentials on the Ubuntu target.

**Detection.** Snort (open-source IDS) is configured with a custom rule set, alerts routed to a plaintext log, to flag the repeated failed authentication attempts hitting port 22.

**Mitigation.** Students write a rule for UFW (Ubuntu's built-in firewall) to block the attacking host, with a deliberate gotcha: UFW evaluates rules top-down with an implicit deny at the bottom, so a rule placed after that deny will never fire. Getting rule ordering right is part of the lesson.

**Privilege escalation.** To show what happens after a successful intrusion, the lab walks through a legacy (patched) Nmap privilege-escalation path: an old Nmap build with the setuid bit set could be run in interactive mode to drop into a root shell. From there, students create a new privileged user, then use it to safely delete the account they escalated through, demonstrating account takeover and cleanup in one pass.

## Lab 2 — ARP Spoofing / Man-in-the-Middle

**Environment:** Kali Linux (attacker, using arpspoof + dsniff), Windows 10 (client), Ubuntu (server, running FileZilla for SFTP)

**Attack.** The attacker poisons the client's ARP cache, associating its own MAC address with the server's IP. Traffic between client and server is silently rerouted through the attacker. With dsniff and IP forwarding enabled on the attacker box, the MITM position is used to intercept credentials, the SFTP connection is disrupted, the client falls back to plaintext FTP, and its credentials leak straight to the attacker.

**Detection.** A Python/Scapy-based script (adapted from the open-source spoof-ee tool) run on the Windows client watches for a mismatch between the server's known MAC and the MAC currently answering for its IP, the signature of ARP spoofing.

**Mitigation.** Dynamic ARP entries can be overwritten at will by an attacker; static entries can't. The lab has students remove the dynamic entry for the server and manually bind its IP to its real MAC address via netsh interface ipv4 add neighbors, permanently hardening the client against further spoofing from that session.

## Key Tools

Kali Linux, Snort (IDS), UFW, Hydra, Nmap, arpspoof / dsniff, Python (Scapy), Wireshark, FileZilla (SFTP)

## Lessons Learned

- Splitting into two sub-teams gave each lab an independent "fresh eyes" tester, surfacing unclear instructions early.
- Working with industry partners (Field Effect, GDMS-C) grounded the labs in realistic attacker behavior rather than textbook simplifications.
- Consistent documentation formatting across sub-teams matters, this was fixed late and cost extra rework; doing it up front would have saved time.

## Next Steps (from the original report)

- Move the attacker outside the local network for more realistic topology.
- Layer in secondary payloads (e.g. a trojan dropped via the ARP MITM position, or spyware installed after the privilege-escalation path).
- Broader student testing to gather completion-rate and runtime metrics.

---
*Corporate-partner-provided lab materials, screenshots, and proprietary Cyber Range assets referenced in the original report are not redistributed here.*
