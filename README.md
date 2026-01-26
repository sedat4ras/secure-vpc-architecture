# AWS Multi-Tier Secure Network Architecture

## Project Overview
The primary objective of this project is to implement a robust and secure network environment on AWS that follows the principle of least privilege. In a standard single-tier architecture, web servers are often exposed directly to the internet, creating a significant attack surface. This project solves that problem by placing the application layer in a completely isolated Private Subnet. Traffic is managed through a managed Application Load Balancer, while outbound connectivity for system updates is handled by a custom NAT Instance.

![Architecture Diagram](diagrams/Architecture.png)

## Architecture Design
The architecture is built within a custom Virtual Private Cloud (VPC) using the 10.0.0.0/16 CIDR block. The design is split into two distinct layers: Public Layer and Private Layer. The Public Layer contains the Application Load Balancer and the NAT Instance, which are the only components with a public footprint. The Private Layer contains the Apache Web Server, which has no public IP address and cannot be reached directly from the internet.

## Infrastructure Status and Resource Management
The resources were deployed across multiple Availability Zones to ensure high availability. The management of these instances required careful coordination of IP addressing and network routing tables to ensure the private instance could communicate with the NAT instance for internet-bound traffic.

![Infrastructure Instances](screenshots/instances.png)

## Security Group Chaining and Traffic Control
The core of this project security lies in Security Group Chaining. Instead of allowing traffic based on broad IP ranges, the security groups are linked to each other. This ensures that even if an unauthorized user knows the private IP of the server, they cannot communicate with it unless they are coming through the approved load balancer or the management gateway.

## Application Load Balancer Security
The ALB acts as the front door. It is configured to accept standard HTTP traffic on port 80 from any source. Its primary role is to receive requests and forward them to the healthy targets in the private subnet.

![Load Balancer Configuration](diagrams/load-balancer.png)
![ALB Security Group](diagrams/alb-sg.png)

## Web Server Security Isolation
The Web Server is the most protected asset. Its security group is configured with strict rules. It only accepts traffic if the source is the ALB Security Group ID and it only accepts management traffic if the source is the NAT Instance Security Group ID.

![Web Server Security Group](diagrams/production-web-server-sg.png)

## Management Gateway (NAT Instance) Security
The NAT Instance serves a dual purpose: it acts as a NAT gateway for the private server outbound requests and as a Bastion Host for administrative access. Its security group is restricted to only allow SSH access from the administrator's specific public IP address.

![NAT Instance Security Group](diagrams/nat-instance-sg.png)

## Connectivity and Deployment Verification

### Load Balancer Target Health
A critical component of a multi-tier system is the health check mechanism. The ALB constantly performs heartbeats to the private web server. If the server fails to respond on port 80, the ALB will stop sending traffic to it. The following screenshot confirms that the private instance is correctly registered and passing these health checks.

![ALB Target Health](diagrams/alb-healthy.png)

### Administrative Access via SSH Agent Forwarding
Since the web server is in a private subnet, direct SSH is impossible. I utilized SSH Agent Forwarding, allowing me to connect to the NAT Instance and then jump to the Web Server using my local private key without ever uploading that key to the cloud. This maintains the integrity of the security credentials.

![SSH Agent Forwarding Access](diagrams/secure-ssh-jump-access.png)

### Final Infrastructure Validation
The ultimate proof of the architecture success is the live web response. By navigating to the DNS name provided by the ALB, we can see the professional landing page served by the private EC2 instance. This confirms that the entire chain from the internet gateway to the load balancer and finally to the private subnet is functioning as intended.

![Live Web Page Validation](diagrams/live-web-page.png)
