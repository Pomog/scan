# SCAN
## 1. Context and Terminology
### 1.1 The OSI Layers in Brief
- Application Layer (HTTP, TLS, SSH, etc.)
Where the “user-facing” parts of networking happen (web traffic, email, etc.).

- Transport Layer (TCP, UDP)
Responsible for the idea of “ports” (e.g., port 80 for HTTP, port 443 for HTTPS, port 22 for SSH).
TCP ensures reliability (handshakes, guaranteed delivery).

- Network Layer (IP)
Responsible for IP addressing and routing. This is where IP addresses (e.g., 192.168.0.10) live.

- Link Layer (Ethernet / Wi-Fi and ARP)
Local network communication, including MAC addresses and ARP (Address Resolution Protocol).

### 1.2 What Is Scanning?
- Host Discovery (Are you alive?)
At the link layer, ARP is key on a local network (subnet). ARP stands for Address Resolution Protocol, which maps an IP address to a MAC address.
At higher layers, “ping scans” or “ICMP echo requests” can also discover hosts.

- Port Discovery (Which ports are open?)
Once you know a host is alive, you typically run a TCP scan with a tool like Nmap to see which ports (services) are available and listening.

### 1.3 Your Setup (Simplified)
- VM #1: 01_scan_laptop (you control this)
- VM #2: 01_scan_RRF-CONTROL (the target)

## 2. Getting Started on 01_scan_laptop
### 2.1 Log In via SSH
```bash
ssh -p 10122 root@127.0.0.1
```
![image](https://github.com/user-attachments/assets/a37ef25a-30be-4045-9cee-48bd5090fe9d)
```bash
ip a
```
![image](https://github.com/user-attachments/assets/ae7b99cc-239f-47de-b92a-069f0fdba648)

## 3. Step 1 — Find the Server’s IP via ARP
### 3.1 Determine Subnet
```bash
ip addr show enp0s8
```
![image](https://github.com/user-attachments/assets/271fabec-d66b-49e7-a969-0e56eb886cd6)
### 3.2 ARP Scan with arp-scan
```bash
arp-scan --interface=enp0s8 192.168.0.0/16
```
```
# sudo arp-scan --interface=enp0s8 192.168.0.0/16
-bash: sudo: command not found
# arp-scan --interface=enp0s8 192.168.0.0/16
Interface: enp0s8, datalink type: EN10MB (Ethernet)
Starting arp-scan 1.9.5 with 65536 hosts (https://github.com/royhills/arp-scan)
192.168.23.42   08:00:27:d0:e5:21       Cadmus Computer Systems
```
## 4. Step 2 — Identify Open Ports on the Target
### 4.1 Basic TCP Port Scan with Nmap
- -sS
A “SYN scan,” the most common and somewhat stealthier TCP scan.
- -p-
Scans all ports (1 through 65535). This is slower, but ensures you don’t miss any potential open port.
- -T4
Increases speed (timing template “4” is fairly aggressive). Keep in mind on a 10 Mbps network, it can still take a while.

```bash
nmap -sS -p- -T4 192.168.23.42
nmap -sS 192.168.23.42 -T4
```
![image](https://github.com/user-attachments/assets/8141ba28-8f07-44ec-9f39-aaf6111aee40)
![image](https://github.com/user-attachments/assets/72cb2057-d147-4860-9cf4-5206f096b047)

- found an open port: 4223/tcp on 192.168.23.42
![image](https://github.com/user-attachments/assets/cd099ed5-2e2a-4581-86da-e475366dbb51)

### 4.2 Service & Version Detection
```bash
nmap -sS -sV -p- -T4 192.168.56.XXX
```
```
Nmap scan report for 192.168.23.42
Host is up (0.00020s latency).
Not shown: 65534 closed ports
PORT     STATE SERVICE VERSION
4223/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
MAC Address: 08:00:27:D0:E5:21 (Oracle VirtualBox virtual NIC)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1388.52 seconds
```

## 5. Step 3 — “Find a Way In”
![image](https://github.com/user-attachments/assets/eaa17001-496e-42c0-8ea2-7f0e67ebb270)






