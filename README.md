# TechNova Ltd. Network Design and Security Configuration

This project demonstrates the design, configuration, and testing of a three-department network for **TechNova Ltd.** using Cisco Packet Tracer. It implements DHCP addressing, an internal HTTP and FTP server, and an extended Access Control List (ACL) to block ICMP ping traffic from Sales to IT while preserving access to essential network services.

## Table of Contents

- [Project Overview](#project-overview)
- [Network Topology](#network-topology)
- [Tools and Technologies](#tools-and-technologies)
- [Configuration Steps](#configuration-steps)
- [Results and Findings](#results-and-findings)
- [Author](#author)

## Project Overview

The purpose of this project is to build and secure a small enterprise network for TechNova Ltd., comprising three departments: **IT, Sales, and HR**. Each department operates on a separate subnet connected through a Cisco 2911 router.

The project objectives were to:

- Design a network with three separate departmental subnets.
- Configure router interfaces and default gateways.
- Implement DHCP to assign IP addresses automatically to client PCs.
- Configure an internal server with HTTP and FTP services.
- Establish baseline connectivity between departments before applying security restrictions.
- Implement an extended ACL to prevent Sales devices from pinging IT devices without blocking HTTP, FTP, or other permitted traffic.
- Validate the configuration through connectivity tests and router verification commands.

## Network Topology

The network uses a **star-style departmental layout**, with a Cisco 2911 router connecting three Cisco 2960 switches. Each switch serves its respective department, and the IT department also hosts the internal server.

### Network Devices

| Device | Model | Quantity | Purpose |
|---|---|---:|---|
| Router | Cisco 2911 | 1 | Inter-subnet routing, DHCP, and ACL enforcement |
| Switches | Cisco 2960 | 3 | Connect devices within each department |
| PCs | Generic PC | 6 | Two client PCs per department |
| Server | Generic Server | 1 | Internal HTTP and FTP services |

All network connections use **copper straight-through cables**.

### IP Addressing Plan

| Department | Network | Default Gateway | Addressing Method |
|---|---|---|---|
| IT | `192.168.10.0/24` | `192.168.10.1` | DHCP for PCs; static for server |
| Sales | `192.168.20.0/24` | `192.168.20.1` | DHCP |
| HR | `192.168.30.0/24` | `192.168.30.1` | DHCP |

### Router Interface Assignments

| Router Interface | Connected Department | IP Address |
|---|---|---|
| `GigabitEthernet0/0` | IT | `192.168.10.1/24` |
| `GigabitEthernet0/1` | Sales | `192.168.20.1/24` |
| `GigabitEthernet0/2` | HR | `192.168.30.1/24` |

### Internal Server Configuration

The IT server uses a static IP address to ensure its services remain accessible at a consistent address.

- **IP address:** `192.168.10.2`
- **Subnet mask:** `255.255.255.0`
- **Default gateway:** `192.168.10.1`
- **HTTP service:** Enabled
- **FTP service:** Enabled
- **FTP username:** `technova`

The server provides an internal company web page and FTP access for testing.

### Security Requirement

The primary security requirement is to **block ICMP traffic from Sales to IT** while allowing Sales to access the IT server through HTTP and FTP. HR and IT must retain their permitted connectivity.

## Tools and Technologies

- **Cisco Packet Tracer:** Network topology design, device configuration, and connectivity testing.
- **Cisco IOS CLI:** Router interface configuration, DHCP setup, and ACL implementation.
- **Cisco 2911 Router:** Inter-subnet routing and traffic filtering.
- **Cisco 2960 Switches:** Departmental network connectivity.
- **DHCP:** Automatic IP address and default gateway assignment.
- **Extended ACLs:** Protocol-specific traffic filtering.
- **HTTP:** Internal web server access.
- **FTP:** Internal file transfer service testing.
- **PC Desktop Tools:** IP Configuration, Command Prompt, and Web Browser.
- **Networking Commands:** `ipconfig`, `ping`, `ftp`, `show ip interface brief`, and `show access-lists`.

## Configuration Steps

### 1. Build the Network Topology

1. Add one Cisco 2911 router, three Cisco 2960 switches, six PCs, and one server to Cisco Packet Tracer.
2. Connect `GigabitEthernet0/0` to the IT switch.
3. Connect `GigabitEthernet0/1` to the Sales switch.
4. Connect `GigabitEthernet0/2` to the HR switch.
5. Connect two PCs to each departmental switch.
6. Connect the server to the IT switch.
7. Use copper straight-through cables for all connections.

### 2. Configure the Router Interfaces

Enter the following commands in the router CLI:

```text
enable
configure terminal

interface gigabitEthernet 0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 exit

interface gigabitEthernet 0/1
 ip address 192.168.20.1 255.255.255.0
 no shutdown
 exit

interface gigabitEthernet 0/2
 ip address 192.168.30.1 255.255.255.0
 no shutdown
 exit
```

Verify that all interfaces are operational:

```text
show ip interface brief
```

All three interfaces should report a status and protocol of `up`.

### 3. Configure DHCP

Exclude the router gateway addresses and the IT server address from the DHCP pools:

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.2
ip dhcp excluded-address 192.168.20.1
ip dhcp excluded-address 192.168.30.1
```

Create the DHCP pool for IT:

```text
ip dhcp pool IT
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
```

Create the DHCP pool for Sales:

```text
ip dhcp pool SALES
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
```

Create the DHCP pool for HR:

```text
ip dhcp pool HR
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
```

### 4. Configure the IT Server

1. Open the server's **Desktop > IP Configuration** settings.
2. Assign the static IP address `192.168.10.2`.
3. Set the subnet mask to `255.255.255.0`.
4. Set the default gateway to `192.168.10.1`.
5. Open **Services > HTTP** and enable HTTP.
6. Edit the default web page to display the company name and the text `Internal Company Server`.
7. Open **Services > FTP** and enable FTP.
8. Create the FTP user `technova` for service testing.

### 5. Configure the Client PCs

For each of the six PCs:

1. Open **Desktop > IP Configuration**.
2. Select **DHCP**.
3. Open Command Prompt and run:

```text
ipconfig
```

4. Confirm that the PC has received an IP address, subnet mask, and default gateway from the correct departmental DHCP pool.

### 6. Test Baseline Connectivity

Before applying the ACL, test connectivity between departments using the actual IP addresses assigned to the PCs.

Examples include:

```text
ping <IT-PC-address>
ping <HR-PC-address>
```

Test HTTP access from a Sales PC by opening the following URL in its web browser:

```text
http://192.168.10.2
```

Test FTP access from a Sales PC:

```text
ftp 192.168.10.2
```

Confirm that the pings succeed and that the HTTP page loads and FTP login works before proceeding.

### 7. Configure the Extended ACL

Create an extended ACL named `Sales-ACL` to deny ICMP traffic from the Sales subnet to the IT subnet while permitting other IP traffic:

```text
enable
configure terminal

ip access-list extended Sales-ACL
 deny icmp 192.168.20.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
 exit

interface gigabitEthernet 0/1
 ip access-group Sales-ACL in
 exit

end
```

The ACL is applied **inbound on `GigabitEthernet0/1`**, the Sales-facing router interface.

The rules work as follows:

- **Deny ICMP:** Blocks ping traffic originating from `192.168.20.0/24` and destined for `192.168.10.0/24`.
- **Permit IP:** Allows other IP traffic, including HTTP and FTP, as well as permitted communication with other networks.
- **Inbound application:** Filters Sales traffic as it enters the router.

The `permit ip any any` rule is essential because an ACL has an implicit deny at the end. Without the permit rule, other traffic could also be blocked.

### 8. Validate the ACL

After applying the ACL, repeat the connectivity tests.

Verify the ACL configuration and interface assignment using:

```text
show access-lists
show ip interface gigabitEthernet 0/1
```

Confirm that the deny rule's match counter increases after blocked ping attempts and that `Sales-ACL` is listed as the inbound access list on the Sales interface.

## Results and Findings

The project achieved the required network configuration and security behaviour.

### Connectivity Test Results

| Test | Expected Result | Actual Result |
|---|---|---|
| IT to Sales ping | Success | Success |
| IT to HR ping | Success | Success |
| HR to IT ping | Success | Success |
| HR to Sales ping | Success | Success |
| Sales to HR ping | Success | Success |
| Sales to IT ping | Blocked | Blocked |
| Sales to IT server HTTP | Allowed | Allowed |
| Sales to IT server FTP | Allowed | Allowed |
| HR to IT server HTTP | Allowed | Allowed |
| HR to IT server FTP | Allowed | Allowed |

### Key Findings

- **Successful subnet configuration:** IT, Sales, and HR operate on separate `/24` subnets connected through the Cisco 2911 router.
- **Automatic addressing:** DHCP successfully assigns IP addresses and default gateways to all six PCs.
- **Working internal services:** The IT server provides HTTP and FTP services accessible from Sales and HR.
- **Effective access control:** The ACL blocks ping traffic from Sales to IT without disrupting the required server services.
- **Preserved departmental connectivity:** HR retains connectivity to IT and Sales, and IT can reach Sales and HR.
- **Verified ACL enforcement:** Router inspection commands confirm the ACL rules and their application to the Sales-facing interface.

### Troubleshooting Considerations

- If a PC receives an address beginning with `169.254.x.x`, check DHCP pool configuration and router interface status.
- If a PC cannot reach another subnet, verify its default gateway using `ipconfig`.
- If a link remains red, check the cable type, connected ports, and interface status.
- If Sales can still ping IT, confirm that `Sales-ACL` is applied inbound on `GigabitEthernet0/1`.
- If HTTP access fails, verify that the server's HTTP service is enabled and that the ACL denies ICMP rather than all IP traffic.

### Conclusion

The TechNova Ltd. network meets the project requirements for departmental segmentation, DHCP configuration, internal server services, and protocol-specific traffic filtering.

The key lesson is that **security controls should enforce the required restriction without unnecessarily disrupting legitimate services**. By denying ICMP traffic instead of all traffic between Sales and IT, the project demonstrates how an extended ACL can implement a targeted security policy while preserving HTTP and FTP access.

## Author

**Taiwo Dauda**

- **Location:** Scotland, UK
- **Student ID:** `GRC-C26-09-EU-028`
- **Focus Areas:** IT Governance, Risk & Compliance (GRC), Cybersecurity, Networking, and IT Audit
- **GitHub:** [Add your GitHub profile URL]
- **LinkedIn:** [Add your LinkedIn profile URL]
- **Email:** [Add your professional email address]

*Building practical networking and cybersecurity skills, one project at a time.*
