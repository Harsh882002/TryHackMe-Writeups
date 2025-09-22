# TryHackMe Labs & Write-ups

A curated collection of TryHackMe labs and detailed write-ups documenting my hands-on learning in penetration testing and cybersecurity. This repo demonstrates practical skills across Web Application Security, Network Exploitation, Forensics, Binary Analysis, and Post-Exploitation.

## Repository Structure


## How to Use
- Browse folders to find labs by category or date.
- Each lab file contains: objective, environment/tools, step-by-step methodology (commands/payloads/screenshots), results (flags), and mitigation notes.
- Reusable scripts or PoCs are stored in `Tools/`. Screenshots go to `assets/images/`.

## Write-up Template
Use this template for all lab files to keep consistency.

```markdown
# Lab Title (TryHackMe)

## Objective
Short description of the challenge goal.

## Environment / Tools
List of tools and versions (e.g., Nmap, Burp Suite, Wireshark, Ghidra, Python).

## Methodology / Steps
1. Reconnaissance
   - commands & outputs (e.g., `nmap -sC -sV 10.10.10.100`)
2. Enumeration
3. Exploitation
4. Privilege Escalation (if applicable)

Include commands, payloads, and screenshots inline.

## Result / Flag
Final flag or outcome.

## Mitigation / Notes
How to fix the vulnerability and lessons learned.
****
