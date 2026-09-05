# Secure Network Architecture – OPNsense / DMZ / Suricata IDS

Hands-on cybersecurity lab: design, deployment and testing of a segmented enterprise network with firewall, Active Directory, web server in DMZ, intrusion detection and controlled Red Team attacks.

---

## Overview

This project simulates a realistic small/medium enterprise network in a fully virtualized environment (KVM/QEMU).

**Objectives:**
- Design and implement network segmentation (WAN / LAN / DMZ)
- Deploy and harden a central firewall (OPNsense)
- Set up an Active Directory domain controller
- Expose a web service securely through Port Forwarding
- Deploy Suricata IDS for real-time detection
- Perform controlled offensive tests (Nmap, Nikto, Hydra) and analyze detection results

---

## Architecture

| Zone | Network            | Gateway          | Main hosts                        |
|------|--------------------|------------------|-----------------------------------|
| WAN  | 192.168.122.0/24   | DHCP             | OPNsense                          |
| LAN  | 192.168.10.0/24    | 192.168.10.254   | Windows Server (AD), Kali Linux   |
| DMZ  | 192.168.20.0/24    | 192.168.20.1     | Ubuntu Web Server                 |

**Traffic policy:**
- LAN / DMZ → Internet : allowed (NAT)
- Internet → DMZ (80/443) : allowed (Destination NAT only)
- LAN ↔ DMZ : denied by default

---

## Technology Stack

| Component              | Technology                          |
|------------------------|-------------------------------------|
| Virtualization         | KVM / QEMU + virt-manager + libvirt |
| Firewall / Router      | OPNsense                            |
| Identity               | Windows Server (AD DS + DNS)        |
| Web server             | Ubuntu Server + Apache (SSL)        |
| Offensive tools        | Kali Linux (Nmap, Nikto, Hydra)     |
| Intrusion Detection    | Suricata IDS                        |

---

## What was implemented

- Multi-zone network isolation with libvirt
- OPNsense firewall rules and Destination NAT (HTTP/HTTPS)
- Active Directory domain (`lab.local`) with test users
- Apache web server with SSL in the DMZ
- Suricata IDS with custom detection rules
- Red Team scenarios:
  - Network discovery & service enumeration (Nmap)
  - Web vulnerability scanning (Nikto)
  - RDP brute-force (Hydra)
- Analysis of Suricata alerts and control effectiveness

---

## Key Results

| Control                        | Result  | Notes                                      |
|--------------------------------|---------|--------------------------------------------|
| LAN / DMZ segmentation         | Success | Inter-zone traffic properly blocked        |
| Port Forwarding 80/443         | Success | Controlled web exposure                    |
| Nmap detection (Suricata)      | Success | ET SCAN and custom rules triggered         |
| Nikto detection (Suricata)     | Success | TCP/DNS anomalies observed                 |
| RDP brute-force resistance     | Weak    | Simple passwords, no account lockout       |
| Web security headers           | Weak    | Missing CSP, HSTS, X-Content-Type-Options  |

---

## Identified weaknesses & hardening recommendations

**Priority actions:**
- Enforce strong password policy + account lockout
- Enable MFA / Network Level Authentication for RDP
- Restrict RDP access by source IP on the firewall
- Add modern security headers on the web server
- Use a valid SSL certificate (correct SAN)
- Centralize logs (Suricata + Windows Event) into a SIEM

---

## Evidence

Screenshots of configurations, scans and Suricata alerts are available in the `screenshots/` folder.
