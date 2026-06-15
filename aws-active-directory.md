# Cloud Infrastructure: Deploying Active Directory in AWS EC2

## Project Objective
Demonstrate cloud instance provisioning, security group management, network parameter anchoring, and Active Directory Domain Services implementation.

## Phase 1: AWS Provisioning & Security
* Launched a `t3.micro` Windows Server 2025 instance inside the AWS Free Tier.
* Allocated and associated an **Elastic IP address** to preserve a permanent public routing path.
* Locked down incoming RDP access traffic (Port 3389) strictly to my local network IP parameters to secure the server against global brute-force discovery sweeps.

## Phase 2: Active Directory Promotions
1. Renamed the computer host to `DC-01` to establish system architecture standards.
2. Installed the **Active Directory Domain Services (AD DS)** features via Server Manager.
3. Created a new forest root called `homelab.local`.
4. Anchored network identity configurations by mapping the Preferred DNS Server back to loopback (`127.0.0.1`).

## Phase 3: Identity Management Tests
* Created test organizational structures (OUs) to segregate departmental structures.
* Implemented credential access parameters using **Active Directory Users and Computers**.
