# pfSense VLAN Configuration Guide 🌐
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![pfSense](https://img.shields.io/badge/pfSense-CE-orange)
![License](https://img.shields.io/badge/license-MIT-green)

Complete guide for configuring VLANs in pfSense, supporting both static and DHCP configurations.

## 📋 Table of Contents
- [Post Installation Security Setup](#-post-installation-security-setup)
  - [SSH Configuration](#ssh-configuration)
  - [HTTPS Setup](#https-setup)
  - [WAN Security](#wan-security)
  - [DNS Configuration](#dns-configuration)
- [Prerequisites](#prerequisites)
- [VLAN Configuration](#vlan-configuration)
  - [VLAN Creation](#1-vlan-creation)
  - [Interface Assignment](#2-interface-assignment)
  - [Firewall Configuration](#3-firewall-configuration)
  - [DHCP Server Setup](#4-dhcp-server-setup)
  - [Testing](#5-testing-and-verification)
- [Troubleshooting](#troubleshooting)

## 🔒 Post Installation Security Setup

### SSH Configuration

![image](https://github.com/user-attachments/assets/2ea52dee-d241-40b2-90cd-5b699adb4c2e)

```plaintext
Path: System > Advanced > Admin Access
```

**Required Steps:**
1. Enable Secure Shell
2. Change SSH Port from default 22
3. Configure SSH Key Authentication (recommended)
4. Restrict SSH access to specific IPs

**Example Configuration:**
```plaintext
SSH Port: 2222 (or another non-standard port)
SSH Key Authentication: Enable
Password Authentication: Disable (if using keys)
```
![image](https://github.com/user-attachments/assets/88012fee-d058-4b27-a937-9b8e8de196b6)


### HTTPS Setup

```plaintext
Path: System > Advanced > Admin Access
```

**Configuration Steps:**
1. Enable HTTPS for WebConfigurator
2. Generate/Import SSL Certificate
3. Configure HTTP Redirect to HTTPS
4. Set HTTPS Port (default 443)

**Security Recommendations:**
```plaintext
TLS Version: 1.2 and 1.3 only
Strong Ciphers: Enable
HSTS: Enable
HTTP Redirect: Enable
```
![image](https://github.com/user-attachments/assets/25e6228c-2031-4a4d-ac22-8f94233e4cbd)

### WAN Security

```plaintext
Path: Interfaces > WAN
```

**Block Private Networks:**
1. Navigate to WAN interface
2. Enable "Block private networks"
3. Enable "Block bogon networks"
4. 
![image](https://github.com/user-attachments/assets/bb6b88a9-2940-40bc-a895-dd165f9597ec)


**Why Important:**
- Prevents RFC1918 traffic from entering your network
- Blocks known-bad IP ranges
- Reduces attack surface

### DNS Configuration

```plaintext
Path: System > General Setup
```

**DNS Server Setup:**
1. Configure primary DNS servers
2. Uncheck "Allow DNS server list to be overridden by DHCP/PPP on WAN"
3. Set DNS Server Override options

**Example Configuration:**
```plaintext
DNS Servers:
1.1.1.1 (Cloudflare)
8.8.8.8 (Google)
Uncheck: Allow Override by ISP
Enable: DNS Resolution
```

![image](https://github.com/user-attachments/assets/900f28a7-13da-4d0e-b900-f5399d553b4d)



**Benefits:**
- Prevents ISP DNS hijacking
- Maintains consistent DNS resolution
- Improves privacy and security

## ⚡ Quick Star
VLAN Configuration
```bash
# Quick command sequence
1. Web UI > Interfaces > VLANs > Add
2. Setup VLAN ID and Interface
3. Assign IP/DHCP
4. Configure Firewall Rules
```

## 🔧 Prerequisites

Before starting, ensure you have:

- pfSense installed and updated
- 802.1Q VLAN compatible switch
- Administrator access to pfSense
- Current configuration backup

## 📖 VLAN Configuration

### 1. VLAN Creation

```plaintext
Path: Interfaces > Assignments > VLANs > Add
```
![image](https://github.com/user-attachments/assets/97b917ad-35b5-4c46-8430-82326ec605dc)


**Required Parameters:**
- Parent Interface: `igb0/em0`
- VLAN Tag: `1-4094`
- Description: `VLAN_Name`

### 2. Interface Assignment

#### 2.1 Static IP Setup
```plaintext
IPv4 Configuration:
- Type: Static
- IPv4 Address: 192.168.10.1
- Subnet mask: 24
```
![image](https://github.com/user-attachments/assets/b9f3c670-eb5a-4af4-94e8-f9670022fc1e)

![image](https://github.com/user-attachments/assets/2626112b-f902-467c-b26f-d861b8e6052b)

Useful link: https://www.youtube.com/watch?v=NihE2u3zBlw   (configuration of VLANS in Pfsense)
https://youtu.be/BbRIEKuFeT8?si=onIIuPrNY6yh-K-w (VLAN Base)
https://youtu.be/4LKbvYdCC1I?si=bUjohgq9ziKPbiR- (VLAN Trunking)
https://www.youtube.com/watch?v=jkva3hbJXZE (VLAN routing)
https://www.youtube.com/watch?v=kBPMT3CXTfA (SW_Layer_3)
https://www.youtube.com/watch?v=8onkkwLJF2Y (VTP)

#### 2.2 DHCP Setup
```plaintext
IPv4 Configuration:
- Type: DHCP
- [Automatic configuration]
```


### 3. Firewall Configuration

Basic rules example:

```plaintext
Action  Source          Destination   Port    Description
ALLOW   VLAN_net       ANY           *       Allow outbound
ALLOW   ANY            VLAN_net      TCP/80  Allow HTTP
BLOCK   *              *             *       Block all else
```

### 4. DHCP Server Setup

```plaintext
Path: Services > DHCP Server > [VLAN interface]

Range: 192.168.10.100 - 192.168.10.200
Subnet: 255.255.255.0
Gateway: [VLAN Interface IP]
```

![image](https://github.com/user-attachments/assets/4570eed9-1acb-4e13-b555-1b107b2c1e70)

![image](https://github.com/user-attachments/assets/acb514ae-034b-443c-bb68-64fdd239618c)


### 5. Testing and Verification

1. Verify VLAN interface status
2. Test DHCP assignment (if configured)
3. Check internal VLAN connectivity
4. Verify internet access (if configured)
5. Review system logs for errors

## 🔍 Troubleshooting

### Common Issues and Solutions

| Issue | Solution |
|----------|-----------|
| No DHCP Lease | Check DHCP service status |
| No Connectivity | Verify VLAN tag configuration |
| Switch Errors | Check port configurations |
| Routing Issues | Verify interface gateway settings |

### Useful Debugging Commands

```bash
# Check interface status
ifconfig vlan10

# Verify DHCP leases
dhcpd -T

# Test connectivity
ping -c 3 192.168.10.1

# View VLAN traffic
tcpdump -i vlan10
```
   - Use consistent naming conventions
   - Implement change control procedures
   - Regular configuration backups
   - Test before production deployment
