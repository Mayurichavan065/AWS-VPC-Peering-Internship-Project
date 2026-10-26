# AWS VPC Peering Internship Project

### Secure Inter-VPC Communication Using AWS EC2, VPC Peering, and Bastion Host

**Internship Domain:** Amazon Web Services (AWS)  
**Project Type:** Cloud Networking and Infrastructure  
**AWS Region:** Asia Pacific (Mumbai) — `ap-south-1`  
**Project Status:** Successfully Completed  
**Developed By:** Mayuri Chavan

---

## 📌 1. Project Overview

This project demonstrates the implementation of secure communication between two isolated Virtual Private Clouds (VPCs) using **Amazon Web Services (AWS)**.

The infrastructure consists of two VPCs, three subnets, three EC2 instances, an Internet Gateway, custom route tables, security groups, and a VPC Peering Connection.

A Bastion Host is deployed in a public subnet to provide secure SSH access to private EC2 instances.

The project demonstrates how private EC2 instances in different VPCs can communicate securely using private IPv4 addresses without requiring direct public internet access.

---

## 🎯 2. Project Objectives

- Design and configure two isolated AWS Virtual Private Clouds.
- Create public and private subnets with appropriate CIDR blocks.
- Deploy EC2 instances within the configured subnets.
- Implement a Bastion Host for secure SSH access.
- Configure security groups to control network traffic.
- Establish a VPC Peering Connection between two VPCs.
- Configure route tables for inter-VPC communication.
- Verify connectivity using SSH and ICMP ping commands.
- Demonstrate secure communication between private EC2 instances.

---

## ☁️ 3. AWS Services Used

| AWS Service | Purpose |
|---|---|
| Amazon VPC | Creates isolated virtual networks |
| Amazon EC2 | Hosts virtual machines |
| VPC Peering | Enables private communication between VPCs |
| Internet Gateway | Provides internet connectivity for the public subnet |
| Route Tables | Controls network traffic routing |
| Security Groups | Controls inbound and outbound traffic |
| Amazon EBS | Provides storage for EC2 instances |
| EC2 Key Pairs | Enables SSH authentication |

---

## 🏗️ 4. System Architecture

The project consists of two VPCs.

### VPC A — `ewb-vpc-a`

**CIDR:** `10.10.0.0/16`

Contains:

- Public Subnet: `ewb-a-public`
- Private Subnet: `ewb-a-private`
- Bastion Host EC2
- App-A Private EC2
- Internet Gateway

### VPC B — `ewb-vpc-b`

**CIDR:** `10.20.0.0/16`

Contains:

- Private Subnet: `ewb-b-private`
- App-B Private EC2

### Architecture Diagram

```text
                        INTERNET
                            |
                            |
                    Internet Gateway
                            |
                            |
             +-----------------------------+
             |          VPC A              |
             |       10.10.0.0/16          |
             |                             |
             |  Public Subnet              |
             |  10.10.1.0/24               |
             |                             |
             |  Bastion EC2                |
             |  10.10.1.10                 |
             |          |                  |
             |          | SSH              |
             |          v                  |
             |  Private Subnet             |
             |  10.10.2.0/24               |
             |                             |
             |  App-A EC2                  |
             |  10.10.2.10                 |
             +-------------+---------------+
                           |
                           |
                    VPC Peering
                           |
                           |
             +-------------+---------------+
             |          VPC B              |
             |       10.20.0.0/16          |
             |                             |
             |  Private Subnet             |
             |  10.20.1.0/24               |
             |                             |
             |  App-B EC2                  |
             |  10.20.1.10                 |
             +-----------------------------+
```

The Bastion Host also provides SSH access to App-B through the configured VPC peering routes.

---

## 🌐 5. Network Configuration

### VPC Configuration

| VPC | CIDR Block |
|---|---|
| `ewb-vpc-a` | `10.10.0.0/16` |
| `ewb-vpc-b` | `10.20.0.0/16` |

### Subnet Configuration

| Subnet | VPC | CIDR |
|---|---|---|
| `ewb-a-public` | VPC A | `10.10.1.0/24` |
| `ewb-a-private` | VPC A | `10.10.2.0/24` |
| `ewb-b-private` | VPC B | `10.20.1.0/24` |

### EC2 Instance Configuration

| Instance | Private IP | Subnet | Public IP |
|---|---|---|---|
| `ewb-bastion` | `10.10.1.10` | Public A | Enabled |
| `ewb-app-a` | `10.10.2.10` | Private A | Disabled |
| `ewb-app-b` | `10.20.1.10` | Private B | Disabled |

**Instance Configuration:**

- Operating System: Amazon Linux 2023
- Instance Type: `t3.micro`
- Storage: 8 GiB gp3
- SSH Authentication: EC2 Key Pair

---

## 🔐 6. Security Group Configuration

Three security groups were created to control network access.

### Bastion Security Group

**Name:** `ewb-sg-bastion`

Allows SSH access from the authorized administrator's public IP address.

### App-A Security Group

**Name:** `ewb-sg-app-a`

Inbound Rules:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | `10.10.1.10/32` |
| ICMP Echo Request | ICMP | `10.20.1.10/32` |

### App-B Security Group

**Name:** `ewb-sg-app-b`

Inbound Rules:

| Type | Port | Source |
|---|---|---|
| SSH | 22 | `10.10.1.10/32` |
| ICMP Echo Request | ICMP | `10.10.2.10/32` |

These security group rules restrict access to the required sources.

---

## 🔗 7. VPC Peering Configuration

A VPC Peering Connection was established between VPC A and VPC B.

**Peering Connection Name:** `ewb-vpc-peering`

- Requester VPC: `ewb-vpc-a`
- Accepter VPC: `ewb-vpc-b`
- Region: Mumbai
- Peering Status: Active during connectivity testing

### Route Table Configuration

| Route Table | Destination | Target |
|---|---|---|
| `ewb-rt-a-public` | `0.0.0.0/0` | Internet Gateway |
| `ewb-rt-a-public` | `10.20.0.0/16` | VPC Peering |
| `ewb-rt-a-private` | `10.20.0.0/16` | VPC Peering |
| `ewb-rt-b-private` | `10.10.0.0/16` | VPC Peering |

Each route table also retains its local VPC route.

These routes enable private communication between the VPCs.

---

## 💻 8. Implementation Steps

The project was implemented through the following stages:

1. Created two VPCs with non-overlapping CIDR blocks.
2. Created one public subnet and two private subnets.
3. Created and attached an Internet Gateway to VPC A.
4. Configured three route tables and subnet associations.
5. Created security groups for Bastion, App-A, and App-B.
6. Created an EC2 key pair for SSH authentication.
7. Launched three Amazon Linux EC2 instances.
8. Configured SSH connectivity through the Bastion Host.
9. Created and accepted the VPC Peering Connection.
10. Updated route tables for inter-VPC communication.
11. Tested connectivity between private EC2 instances.
12. Captured implementation and testing screenshots.
13. Stopped all three EC2 instances after testing.

---

## 🧪 9. Connectivity Testing

### Test 1 — SSH to Bastion Host

Successfully connected from a Windows laptop to the Bastion Host using SSH.

### Test 2 — SSH to Private EC2 Instances

Successfully accessed the private EC2 instances through the Bastion Host.

### Test 3 — App-A to App-B

Executed the following command from App-A:

```bash
ping -c 4 10.20.1.10
```

**Result:**

```text
4 packets transmitted
4 packets received
0% packet loss
```

**Status:** PASS

### Test 4 — App-B to App-A

Executed the following command from App-B:

```bash
ping -c 4 10.10.2.10
```

**Result:**

```text
4 packets transmitted
4 packets received
0% packet loss
```

**Status:** PASS

### Testing Summary

| Test | Result |
|---|---|
| Bastion SSH | PASS |
| App-A SSH | PASS |
| App-B SSH | PASS |
| App-A → App-B Ping | PASS |
| App-B → App-A Ping | PASS |

The successful bidirectional ping tests confirm that the private EC2 instances communicated across the VPC Peering Connection.

---

## 📸 10. Project Screenshots

Implementation evidence is organized in the `Screenshots` directory.

| Folder | Contents |
|---|---|
| `01-VPC` | VPC creation and configuration |
| `02-Subnets` | Public and private subnet configuration |
| `03-Route-Tables` | Route tables and subnet associations |
| `04-Security-Groups` | Security group configurations |
| `05-EC2` | EC2 instance deployment |
| `06-Peering` | VPC peering and routing |
| `07-Testing` | SSH and ping connectivity tests |
| `08-Cleanup` | Stopped EC2 instances |

The complete implementation screenshots and explanations are included in the final project report.

---

## ⚠️ 11. Challenges and Solutions

### Challenge 1 — Missing SSH Key File

**Problem:** PowerShell could not locate the downloaded `.pem` file.

**Solution:** Located the key in a different directory and moved it to the correct SSH folder.

### Challenge 2 — SSH Private Key Permissions

**Problem:** SSH rejected the private key because its file permissions were too open.

**Solution:** Updated Windows file permissions using `icacls`.

### Challenge 3 — Duplicate Security Group Rule

**Problem:** AWS rejected an attempt to add an SSH rule that already existed.

**Solution:** Verified the existing inbound rule and avoided creating a duplicate.

### Challenge 4 — Private Instance SSH Timeout

**Problem:** The initial SSH connection to App-A timed out.

**Solution:** Checked the EC2 instance state, security group rules, and initialization status before retrying.

### Challenge 5 — Inter-VPC Communication

**Problem:** Communication between isolated VPCs required additional network configuration.

**Solution:** Established VPC peering and configured the required routes and ICMP security group rules.

---

## 📊 12. Project Results

The project successfully demonstrated:

- Creation of isolated AWS network environments.
- Secure SSH administration through a Bastion Host.
- Private EC2 deployment without public IP addresses.
- Communication between VPCs using VPC Peering.
- Bidirectional ICMP connectivity with 0% packet loss in the recorded tests.
- Network access control using security groups and route tables.

---

## 🏁 13. Conclusion

This project successfully implemented secure private communication between two AWS Virtual Private Clouds using VPC Peering.

The implementation demonstrated practical cloud networking concepts, including subnet configuration, routing, security groups, EC2 deployment, and Bastion Host access.

Successful bidirectional connectivity tests confirmed that the configured VPC peering architecture supported communication between private EC2 instances.

The project provided hands-on experience in AWS cloud infrastructure deployment, network troubleshooting, secure access management, and connectivity validation.

---

## 📚 14. References

- AWS Documentation — Amazon VPC
- AWS Documentation — VPC Peering
- AWS Documentation — Amazon EC2
- AWS Documentation — Security Groups
- AWS Documentation — Route Tables

---

## 👩‍💻 Author

**Mayuri Chavan**  
B.Tech — Computer Engineering  
Dr. Babasaheb Ambedkar Technological University, Lonere

**Project:** AWS VPC Peering Internship Project

---

⭐ *This project was developed as part of an AWS Internship to demonstrate practical knowledge of cloud networking, secure EC2 access, and inter-VPC communication.*
