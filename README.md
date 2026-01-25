# AWS Multi-Tier Secure Infrastructure 

This project demonstrates a professional-grade AWS networking architecture designed to isolate web servers from the public internet while maintaining high availability and secure outbound access.

## 🏗️ Architecture Diagram

![Architecture Diagram](diagrams/architecture.png)

Project Overview
The goal of this project was to host a web server that is completely invisible to the public internet. 
By placing the server in a Private Subnet, we eliminate direct attack vectors. 

### Key Features:
- **Zero Public Exposure:** The web server has no Public IP; it's tucked away in a Private Subnet.
- **Entry Point:** Traffic is handled by an **Application Load Balancer (ALB)** across two Availability Zones.
- **Outbound Connectivity:** A custom **NAT Instance** allows the private server to download updates without being exposed.
