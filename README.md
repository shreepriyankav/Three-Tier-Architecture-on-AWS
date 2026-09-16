# Three-Tier-Architecture-on-AWS
A secure, scalable, and highly available three-tier web application architecture built on AWS, with centralized logging, monitoring, and alerting.

<img width="537" height="802" alt="Screenshot 2026-09-16 160501" src="https://github.com/user-attachments/assets/8ea98f88-cfd3-4da3-9c94-3b26bb7a22f1" />

 # Overview

This project demonstrates the deployment of a three-tier architecture on AWS, consisting of the Web Tier, Application Tier, and Database Tier.

The infrastructure is built inside a custom Amazon VPC using public and private subnets, security groups, load balancing, managed database services, monitoring, logging, alerting, and backup mechanisms.

The project follows AWS architecture principles such as:

🔐 Network isolation
🛡️ Least-privilege security
⚡ High availability
📊 Monitoring and observability
💾 Backup and disaster recovery
🌐 Secure application access

# 🏗️ Architecture
                         👤 USER
                           │
                           │ HTTPS : 443
                           ▼
                 ┌─────────────────────┐
                 │ Application Load    │
                 │ Balancer (ALB)      │
                 └──────────┬──────────┘
                            │
                            │ HTTP : 80
                            ▼
                 ┌─────────────────────┐
                 │     WEB TIER        │
                 │     EC2 + Apache    │
                 └──────────┬──────────┘
                            │
                            │ Flask : 5000
                            ▼
                 ┌─────────────────────┐
                 │     APP TIER        │
                 │     EC2 + Flask     │
                 └──────────┬──────────┘
                            │
                            │ MySQL : 3306
                            ▼
                 ┌─────────────────────┐
                 │     DATABASE TIER   │
                 │     Amazon RDS      │
                 │       MySQL         │
                 └─────────────────────┘

                         AWS VPC
                       10.0.0.0/16

# 🌐 VPC & Network Design

VPC CIDR: 10.0.0.0/16

The VPC provides an isolated networking environment for all application components.

# Public Subnet
10.0.1.0/24

Contains the:

Application Load Balancer
NAT Gateway

The public subnet has access to the Internet Gateway.

# Private Subnet
10.0.2.0/24

Contains the:

Web Tier EC2
Application Tier EC2
Amazon RDS MySQL

Private resources are not directly accessible from the public internet.

# Traffic Flow

The application request follows this flow:

User / Browser
      │
      │ HTTPS (443)
      ▼
Application Load Balancer
      │
      │ HTTP (80)
      ▼
Web Server
Apache EC2
      │
      │ Flask (5000)
      ▼
Application Server
Flask EC2
      │
      │ MySQL (3306)
      ▼
Amazon RDS
MySQL Database

<img width="845" height="612" alt="Screenshot 2026-09-16 161732" src="https://github.com/user-attachments/assets/62dc9ecc-6f33-4623-b5d4-769629b1ad2b" />

# 🔐 Security Architecture

Security groups are configured using a least-privilege approach.

# ALB Security Group

Allows:

HTTP  → Port 80  → Internet
HTTPS → Port 443 → Internet

# Web Tier Security Group

Allows:

HTTP → Port 80 → ALB Security Group

The Web Tier does not need to accept direct internet traffic.

# Application Tier Security Group

Allows:

Flask → Port 5000 → Web Tier Security Group

# Database Security Group

Allows:

MySQL → Port 3306 → Application Tier Security Group

This creates a controlled communication path:

Internet
   ↓
ALB
   ↓
Web Tier
   ↓
App Tier
   ↓
Database

# 📊 Observability & Monitoring

The project uses Amazon CloudWatch for centralized monitoring and logging.

EC2 Instances
      │
      ▼
CloudWatch Agent
      │
      ├── Metrics
      ├── Logs
      └── System Information
              │
              ▼
        Amazon CloudWatch
              │
              ├── Dashboards
              │
              └── Alarms
                    │
                    ▼
                  SNS
                    │
                    ▼
              📧 Email Alert

# 🚨 CloudWatch Alarms

CloudWatch alarms are configured to monitor important infrastructure conditions.

Example:

Disk Usage > 80%
       ↓
CloudWatch Alarm
       ↓
SNS Notification
       ↓
📧 Email Alert

This allows infrastructure issues to be identified before they affect application availability.

# 📁 Project Structure
aws-3-tier-architecture/
│
├── README.md
│
├── architecture/
│   └── aws-3-tier-architecture.png
│
├── screenshots/
│   ├── vpc.png
│   ├── subnets.png
│   ├── route-tables.png
│   ├── ec2.png
│   ├── alb.png
│   ├── rds.png
│   ├── cloudwatch.png
│   └── sns.png
│
├── infra/
│   ├── terraform/
│   └── cloudformation/
│
└── documentation/
    └── project-documentation.pdf
    
# 🎯 Real-World Use Case

A traditional application running on a single server can face problems such as:

Single point of failure
Direct database exposure
Difficult scaling
Limited monitoring
Poor network isolation
Difficult backup management

This architecture separates the application into independent tiers.

        WEB TIER
           ↓
      APP TIER
           ↓
      DATABASE TIER

Each layer has its own security controls and responsibilities. This design can be extended for larger production environments by adding additional EC2 instances, Auto Scaling, Multi-AZ RDS, stronger CI/CD automation, and infrastructure-as-code.

# 📈 Future Improvements

The architecture can be further improved by implementing:

Infrastructure provisioning using Terraform
EC2 Auto Scaling
Multi-AZ RDS deployment
AWS Secrets Manager for database credentials
AWS Certificate Manager for HTTPS
CI/CD pipeline using Jenkins
Containerization using Docker
Kubernetes deployment
Automated security scanning
Centralized application logging
AWS WAF for additional web-layer protection

# ✅ Project Result

The project demonstrates a structured AWS three-tier application architecture with:

✅ Network Isolation
✅ Public / Private Subnet Separation
✅ Load Balancing
✅ Application Tier Separation
✅ Managed Database
✅ Least-Privilege Security
✅ Monitoring & Logging
✅ Automated Alerts
✅ Backup Strategy
✅ High Availability Design

It provides practical experience with AWS networking, compute, databases, security, load balancing, monitoring, and cloud architecture design.

# 👩‍💻 Author

Shree Priyanka V L

B.Tech — Computer Science and Business Systems

Interested in:

☁️ AWS
🔧 DevOps
🐳 Docker
☸️ Kubernetes
🔄 CI/CD
🛡️ DevSecOps

# ⭐ Project

If you found this project useful, consider giving the repository a ⭐.


