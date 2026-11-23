# Azure Networking: Jumpbox and Private VM

A secure Azure network design in which administrators connect to a public **jumpbox** and, from there, reach a **private VM** that has no public IP address.

---

## 1. Requirements

**Azure**
- Active Azure subscription with permission to create resources
- Resource Group: `saran-rg` (Central India)
- Virtual Network: `saranya-vnet` (`10.0.0.0/16`)
  - Public subnet: `10.0.1.0/24`
  - Private subnet: `10.0.2.0/24`
- Two route tables (public and private)
  - Public route table: `0.0.0.0/0` with next hop **Internet**, associated with the public subnet
  - Private route table: no internet route, associated with the private subnet
- Two Linux VMs (AlmaLinux 9, Standard_D2s_v4)
  - Jumpbox: public IP, NSG allowing SSH (port 22)
  - Private VM: no public IP

**Client**
- A machine with an SSH client
- Internet connection

---

## 2. Use Case

- Securely manage servers that must not be exposed to the internet
- Protect backend systems such as database servers, application servers, and internal tools
- Provide a single, controlled entry point for administrators
- Support multi-tier architectures with separate public and private layers
- Meet security and compliance needs by limiting and logging access

---

## 3. How It Works in Real Time (Company Sites)

**Traffic flow**

```
Admin laptop  -->  Internet  -->  Jumpbox (public IP, NSG allows SSH)
                                      |
                              Private network (SSH)
                                      v
                                 Private VM (no public IP)
```

**Real-time process**
1. The administrator connects to the jumpbox's public IP using SSH.
2. The NSG checks the inbound rules and allows only the permitted traffic.
3. From the jumpbox, the administrator connects to the private VM using its private IP.
4. Traffic between the jumpbox and the private VM stays inside the Azure virtual network and never crosses the public internet.
5. The private VM cannot be reached directly from outside because it has no public IP.

**How companies apply this**
- Production databases and application servers are placed in private subnets.
- Access to the jumpbox is limited to office or VPN IP addresses.
- Logins use SSH keys and multi-factor authentication.
- Admin sessions are logged and monitored for audit purposes.
- Many companies replace the self-managed jumpbox with **Azure Bastion**, which gives secure access without exposing any public IP on the VMs.
- A **NAT Gateway** gives private VMs outbound internet access for updates while keeping inbound access closed.

---

## 4. Advantages

- **Better security:** Backend servers are hidden from the internet.
- **Smaller attack surface:** Only one host accepts inbound connections.
- **Centralized access control:** One place to enforce rules, logging, and monitoring.
- **Network segmentation:** Public and private tiers are separated.
- **Routing control:** Route tables define exactly how each subnet sends traffic.
- **Cost-effective:** Only one public IP is needed for many private servers.
- **Scalable:** More subnets and VMs can be added within the same virtual network.
- **Industry standard:** Matches how enterprise cloud environments are secured.

---

**Author:** Saranya T | [GitHub: saranya149](https://github.com/saranya149)
