# pfSense VLAN Configuration Guide 🌐
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![pfSense](https://img.shields.io/badge/pfSense-CE-orange)
![License](https://img.shields.io/badge/license-MIT-green)

Complete guide for configuring VLANs in pfSense, supporting both static and DHCP configurations.

## 📋 Table of Contents
- [Prerequisites](#prerequisites)
- [VLAN Configuration](#vlan-configuration)
  - [VLAN Creation](#1-vlan-creation)
  - [Interface Assignment](#2-interface-assignment)
  - [Firewall Configuration](#3-firewall-configuration)
  - [DHCP Server Setup](#4-dhcp-server-setup)
  - [Testing](#5-testing-and-verification)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Contributing](#contributing)

## ⚡ Quick Start

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

Useful link: https://www.youtube.com/watch?v=NihE2u3zBlw

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

## 📚 Best Practices

1. **Documentation**
   - Maintain detailed network diagrams
   - Document all VLAN IDs and purposes
   - Keep configuration backups

2. **Security**
   - Implement proper network segmentation
   - Use restrictive firewall rules
   - Regularly audit VLAN access

3. **Management**
   - Use consistent naming conventions
   - Implement change control procedures
   - Regular configuration backups
   - Test before production deployment
