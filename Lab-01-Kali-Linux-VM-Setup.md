Lab 01 — Kali Linux VM Setup on macOS (Apple Silicon)
Date: June 6, 2026
Platform: VMware Fusion on macOS (Apple Silicon / ARM)
Goal: Build a personal cybersecurity home lab to practice SOC analyst skills, prepare for TryHackMe labs, and develop hands-on experience for a career in cybersecurity.Objective
Set up a fully functional Kali Linux virtual machine on a MacBook with Apple Silicon (M-series chip) using VMware Fusion. This lab serves as the foundation for all future cybersecurity practice and documentation.Steps Taken
1. Downloaded Kali Linux ISO
	•	Selected the ARM64 version compatible with Apple Silicon
2. Created VM in VMware Fusion
	•	Chose Debian 13.x 64-bit ARM as the guest OS
	•	Configured CPU, RAM, and disk before installation
3. Installed via Graphical Installer
Selected Graphical Install from the GRUB boot menu for a user-friendly setup experience.
Installation choices:
	•	Language & locale: English / US
	•	Hostname: kali
	•	Domain: (left blank — standalone lab machine)
	•	User: anonymous lab user (no real name used — security best practice)
	•	Partitioning: Guided — use entire disk, all files in one partition
	•	Desktop: Xfce (lightweight, fast for VM use)
	•	Tools: top10 + default collectionKey Decisions & Reasoning
	•	Why Kali Linux? Industry standard for cybersecurity labs; comes pre-loaded with tools like Nmap, Wireshark, Metasploit, Burp Suite, and more.
	•	Why VMware Fusion over VirtualBox? Better ARM/Apple Silicon support and performance on M-series Macs.
	•	Why Xfce? Lightweight desktop that runs efficiently in a VM without consuming excess RAM.
	•	Why 4GB RAM / 4 cores? Enough headroom to run tools like Wireshark and Metasploit simultaneously without lag, while leaving resources for macOS.
	•	Why NAT networking? Safe default — VM can reach the internet but is isolated from the local network.What I Learned
	•	How to configure a VM specifically for cybersecurity work
	•	Importance of allocating appropriate resources (RAM/CPU/disk) for lab environments
	•	Kali Linux partition structure and installer options
	•	Why a dedicated lab VM is safer than installing tools on a host machine
	•	VMs can be completely deleted to reclaim disk space — making them risk-free to experiment withNext Steps
	•	Connect Kali to TryHackMe via OpenVPN
	•	Complete TryHackMe SOC Level 1 path
	•	Run first Wireshark packet capture
	•	Practice Nmap network scanning
	•	Document each lab as a portfolio write-up
