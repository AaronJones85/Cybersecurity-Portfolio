# Lab 03 — Nmap Network Scanning & Reconnaissance

**Date:** June 7, 2026
**Platform:** Kali Linux (VMware Fusion on macOS Apple Silicon)
**Tool:** Nmap 7.99
**Target:** Local VM (172.16.231.128) & scanme.nmap.org (45.33.32.156)
**Difficulty:** Beginner

---

## Objective

Use Nmap to perform network reconnaissance including host discovery, port scanning, service version detection, OS fingerprinting, and banner grabbing. These are foundational skills used daily by SOC analysts and penetration testers to understand what is running on a network and identify potential vulnerabilities.

> **Note:** All scanning was performed on authorized targets only. scanme.nmap.org is a server maintained by the Nmap project specifically for practice scanning.

---

## Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Nmap | 7.99 | Network scanning and reconnaissance |
| Terminal (Kali) | — | Command execution |
| eth0 | — | Primary network interface |

---

## Scans Performed

| Scan Type | Command | Purpose |
|-----------|---------|---------|
| Host Discovery | `ip addr show eth0` | Identify local IP address |
| Basic Port Scan | `nmap 172.16.231.128` | Scan local VM for open ports |
| Service Version Scan | `nmap -sV scanme.nmap.org` | Identify services and versions |
| OS Detection | `sudo nmap -O scanme.nmap.org` | Fingerprint operating system |
| Banner Grabbing | `nmap -sV --script banner scanme.nmap.org` | Extract service banners |

---

## Steps Taken

### Step 1 — Identified Local IP Address

Ran the following command to identify the Kali VM's IP address on the network:

```bash
ip addr show eth0
```

**Result:**
- IP Address: `172.16.231.128/24`
- Broadcast: `172.16.231.255`
- MAC Address: `00:0c:29:7b:0b:8b`
- Interface state: UP (active)

**Screenshot 1 — IP Address Discovery:**

![IP Address](Screenshot-One.png)

---

### Step 2 — Basic Port Scan (Local VM)

Performed a default Nmap scan against the local Kali VM:

```bash
nmap 172.16.231.128
```

**Result:**
- 1000 ports scanned
- All 1000 ports in filtered/ignored state
- No open ports found
- Scan type: SYN Stealth Scan
- Scan time: 201.93 seconds

**Analysis:** All ports filtered indicates the machine is hardened with no services running and a firewall blocking responses. This is the expected result for a freshly installed Kali VM. From a SOC perspective, seeing all ports filtered on a host means it is either well-secured or actively blocking reconnaissance.

**Screenshot 2 — Basic Nmap Scan (Local VM):**

![Basic Nmap Scan](Screenshot-Two.png)

---

### Step 3 — Service Version Scan (scanme.nmap.org)

Performed a service version scan against Nmap's authorized practice server:

```bash
nmap -sV scanme.nmap.org
```

**Target:** scanme.nmap.org (45.33.32.156)
**Latency:** 0.078s
**Total ports scanned:** 1000

**Results:**

| Port | State | Service | Version |
|------|-------|---------|---------|
| 22/tcp | open | SSH | OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.13 |
| 25/tcp | filtered | SMTP | — |
| 53/tcp | open | DNS | NLnet Labs NSD |
| 80/tcp | open | HTTP | Apache httpd 2.4.7 (Ubuntu) |
| 111/tcp | filtered | rpcbind | — |
| 135/tcp | filtered | msrpc | — |
| 139/tcp | filtered | netbios-ssn | — |
| 445/tcp | filtered | microsoft-ds | — |
| 9929/tcp | open | nping-echo | Nping echo |
| 31337/tcp | open | tcpwrapped | — |

**OS Info:** Linux (CPE: cpe:/o:linux:linux_kernel)

**Analysis:** This is a Linux web server running SSH for remote access and Apache for web hosting. The filtered Windows-specific ports (135, 139, 445) suggest firewall rules blocking those services. Port 31337 (historically called "Elite") running tcpwrapped is interesting — tcpwrapped means the service accepted the connection then closed it, indicating a protected service.

**Screenshot 3 — Service Version Scan:**

![Service Version Scan](Screenshot-Three.png)

---

### Step 4 — OS Detection Scan

Performed OS fingerprinting to identify the operating system:

```bash
sudo nmap -O scanme.nmap.org
```

**Results:**

| Finding | Detail |
|---------|--------|
| Device type | Firewall |
| OS Guess | D-Link DFL-200 firewall (85% confidence) |
| OS CPE | cpe:/h:dlink:dfl-200 |
| Network Distance | 18 hops |
| Exact OS match | None (test conditions non-ideal) |

**Analysis:** Nmap could not definitively identify the OS because a firewall is sitting in front of the server, interfering with OS fingerprinting probes. The 18 hop network distance means traffic traveled through 18 routers to reach the target. This is a real-world example of how firewalls and network infrastructure complicate reconnaissance — an important concept for both attackers and defenders.

**Screenshot 4 — OS Detection:**

![OS Detection](Screenshot-Four.png)

---

### Step 5 — Banner Grabbing

Used Nmap's banner script to extract service banners which reveal detailed version information:

```bash
nmap -sV --script banner scanme.nmap.org
```

**Banners Retrieved:**

| Port | Banner |
|------|--------|
| 22/tcp | `SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.13` |
| 9929/tcp | Raw binary echo response |

**Analysis:** Banner grabbing retrieved the exact SSH version string — `OpenSSH 6.6.1p1`. This is significant because a SOC analyst or penetration tester would immediately cross-reference this version against known CVEs (Common Vulnerabilities and Exposures). Older SSH versions may have known exploits that an attacker could leverage. This demonstrates why keeping software updated is critical for security.

**Screenshot 5 — Banner Grabbing:**

![Banner Grabbing](Screenshot-Five.png)

---

## Summary of Findings

| Target | IP | Open Ports | OS | Key Services |
|--------|----|-----------|-----|-------------|
| Kali VM | 172.16.231.128 | None (all filtered) | Linux (Kali) | None — hardened machine |
| scanme.nmap.org | 45.33.32.156 | 22, 53, 80, 9929, 31337 | Linux (Ubuntu) | SSH, DNS, HTTP |

---

## Key Concepts Learned

**Port States:**
- **Open** — service is actively accepting connections
- **Filtered** — firewall is blocking probe packets
- **Closed** — no service running but port is reachable

**Nmap Scan Types Used:**
- **SYN Stealth Scan** — default scan, sends SYN packets without completing handshake
- **-sV** — probes open ports to determine service and version
- **-O** — OS fingerprinting using TCP/IP stack analysis
- **--script banner** — NSE script to grab service banners

**SOC Relevance:**
- Nmap is used in SOC environments to inventory network assets
- Service version detection helps identify outdated/vulnerable software
- OS fingerprinting helps build network asset maps
- Banner grabbing reveals exact software versions for CVE lookups
- Understanding filtered vs closed ports helps identify firewall rules

---

## What I Learned

- How to identify a machine's IP address and network configuration
- How to perform multiple types of Nmap scans with different objectives
- Why a fully filtered machine is a sign of good security posture
- How to interpret open, closed, and filtered port states
- How firewalls interfere with OS fingerprinting
- How banner grabbing reveals exact software versions
- How to use Nmap's NSE (Nmap Scripting Engine) scripts
- The real-world SOC analyst workflow for network reconnaissance

---

## Next Steps

- [ ] Lab 04 — TryHackMe SOC Level 1 Room
- [ ] Practice Nmap scans against TryHackMe lab targets via VPN
- [ ] Learn to save Nmap output to files using `-oN` flag
- [ ] Explore more Nmap NSE scripts for vulnerability scanning

---

## References

- [Nmap Official Documentation](https://nmap.org/docs.html)
- [scanme.nmap.org](http://scanme.nmap.org)
- [Nmap NSE Script Reference](https://nmap.org/nsedoc/)

---

*Part of an ongoing cybersecurity home lab portfolio documenting hands-on SOC analyst skill development.*
