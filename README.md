# Three-Tier Architecture on AWS

A secure, scalable, and highly available three-tier web application architecture built on AWS, with centralized logging, monitoring, and alerting.

<img width="537" height="802" alt="Screenshot 2026-09-16 160501" src="https://github.com/user-attachments/assets/8ea98f88-cfd3-4da3-9c94-3b26bb7a22f1" />

---

## 📋 Overview

This project demonstrates the deployment of a three-tier architecture on AWS, consisting of the Web Tier, Application Tier, and Database Tier.

The infrastructure is built inside a custom Amazon VPC using:
- Public and private subnets
- Security groups
- Load balancing
- Managed database services
- Monitoring and logging
- Alerting and backup mechanisms

### AWS Architecture Principles

- 🔐 **Network Isolation** — Layered security with separate tiers
- 🛡️ **Least-Privilege Security** — Minimal access between components
- ⚡ **High Availability** — Load balancing and managed services
- 📊 **Monitoring & Observability** — CloudWatch metrics and dashboards
- 💾 **Backup & Disaster Recovery** — Automated backups and failover
- 🌐 **Secure Application Access** — HTTPS encryption and SSL/TLS

---

## 🏗️ Architecture Diagram

```
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
                  │    EC2 + Apache     │
                  └──────────┬──────────┘
                             │
                             │ Flask : 5000
                             ▼
                  ┌─────────────────────┐
                  │     APP TIER        │
                  │    EC2 + Flask      │
                  └──────────┬──────────┘
                             │
                             │ MySQL : 3306
                             ▼
                  ┌─────────────────────┐
                  │  DATABASE TIER      │
                  │    Amazon RDS       │
                  │       MySQL         │
                  └─────────────────────┘

                        AWS VPC
                      10.0.0.0/16
```

---

## 🌐 VPC & Network Design

### VPC Configuration

| Component | CIDR Block |
|-----------|-----------|
| VPC | 10.0.0.0/16 |
| Public Subnet | 10.0.1.0/24 |
| Private Subnet | 10.0.2.0/24 |

The VPC provides an isolated networking environment for all application components.

### 📍 Public Subnet (10.0.1.0/24)

Contains:
- ✅ Application Load Balancer (ALB)
- ✅ NAT Gateway

**Access:** Direct access to Internet Gateway

### 🔒 Private Subnet (10.0.2.0/24)

Contains:
- ✅ Web Tier EC2 Instance
- ✅ Application Tier EC2 Instance
- ✅ Amazon RDS MySQL Database

**Access:** Only via NAT Gateway (egress), no direct internet access

---

## 📊 Traffic Flow

```
User / Browser
     │
     │ HTTPS (443)
     ▼
Application Load Balancer
     │
     │ HTTP (80)
     ▼
Web Server (Apache EC2)
     │
     │ Flask (5000)
     ▼
Application Server (Flask EC2)
     │
     │ MySQL (3306)
     ▼
Amazon RDS (MySQL Database)
```

<img width="845" height="612" alt="Screenshot 2026-09-16 161732" src="https://github.com/user-attachments/assets/62dc9ecc-6f33-4623-b5d4-769629b1ad2b" />

---

## 🔐 Security Architecture

Security groups are configured using a **least-privilege approach**, ensuring minimal required access between tiers.

### ALB Security Group

| Protocol | Port | Source | Purpose |
|----------|------|--------|---------|
| HTTP | 80 | Internet (0.0.0.0/0) | Web traffic |
| HTTPS | 443 | Internet (0.0.0.0/0) | Encrypted web traffic |

### Web Tier Security Group

| Protocol | Port | Source | Purpose |
|----------|------|--------|---------|
| HTTP | 80 | ALB Security Group | Receives traffic from ALB only |

**Note:** Does NOT accept direct internet traffic.

### Application Tier Security Group

| Protocol | Port | Source | Purpose |
|----------|------|--------|---------|
| Flask | 5000 | Web Tier SG | Receives traffic from Web Tier only |

### Database Security Group

| Protocol | Port | Source | Purpose |
|----------|------|--------|---------|
| MySQL | 3306 | App Tier SG | Receives traffic from App Tier only |

### Controlled Communication Path

```
Internet
   ↓
ALB (Port 443/80)
   ↓
Web Tier (Port 80)
   ↓
App Tier (Port 5000)
   ↓
Database (Port 3306)
```

---

## 📊 Observability & Monitoring

The project uses **Amazon CloudWatch** for centralized monitoring and logging.

### Monitoring Architecture

```
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
             ├── Logs Insights
             └── Alarms
                   │
                   ▼
                 SNS Topic
                   │
                   ▼
             📧 Email Alert
```

### 🚨 CloudWatch Alarms

CloudWatch alarms are configured to monitor critical infrastructure conditions.

**Example Alert Workflow:**

```
Disk Usage > 80%
     ↓
CloudWatch Alarm Triggered
     ↓
SNS Notification Sent
     ↓
📧 Email Alert to Admins
```

This enables proactive issue identification before problems affect application availability.

### Monitored Metrics

- 💾 **Disk Usage** — Alert at >80%
- 🔥 **CPU Utilization** — Alert at >90%
- 🧠 **Memory Usage** — Alert at >85%
- 🌍 **Network Traffic** — Anomaly detection
- ⏱️ **Application Latency** — Response time tracking
- ❌ **Error Rates** — HTTP error monitoring

---

## 📁 Project Structure

<img width="721" height="572" alt="Screenshot 2026-09-16 162407" src="https://github.com/user-attachments/assets/cbc3b5c2-21be-4088-a83f-4eae28ff94cd" />

---

## 🎯 Real-World Use Case

### Problems with Single-Server Architecture

Traditional applications running on a single server face:

- ⚠️ **Single Point of Failure** — Server down = entire application down
- ⚠️ **Direct Database Exposure** — Security vulnerability
- ⚠️ **Difficult Scaling** — Limited capacity
- ⚠️ **Limited Monitoring** — Poor visibility
- ⚠️ **Poor Network Isolation** — Mixed concerns
- ⚠️ **Difficult Backup Management** — Complex recovery

### Three-Tier Architecture Solution

This architecture separates the application into **independent, scalable tiers**:

```
┌──────────────┐
│   WEB TIER   │  ← Handles HTTP requests
└──────┬───────┘
       │
┌──────▼───────┐
│   APP TIER   │  ← Business logic
└──────┬───────┘
       │
┌──────▼────────┐
│ DATABASE TIER │  ← Data persistence
└───────────────┘
```

Each layer has:
- ✅ Independent scaling
- ✅ Dedicated security controls
- ✅ Clear responsibilities
- ✅ Isolated failure domains

---

## 📈 Future Improvements

The architecture can be extended for production environments with:

- 🏗️ **Infrastructure as Code** — Terraform provisioning
- 📈 **Auto Scaling** — EC2 Auto Scaling Groups
- 🔄 **Multi-AZ Deployment** — Multi-AZ RDS for high availability
- 🔑 **Secrets Management** — AWS Secrets Manager for credentials
- 🔐 **HTTPS Certificates** — AWS Certificate Manager (ACM)
- 🔄 **CI/CD Pipeline** — Jenkins, GitHub Actions, or GitLab CI
- 🐳 **Containerization** — Docker for consistent environments
- ☸️ **Orchestration** — Kubernetes (EKS) for container management
- 🛡️ **Security Scanning** — Automated vulnerability assessments
- 📝 **Application Logging** — Centralized ELK stack or CloudWatch Logs Insights
- 🔒 **WAF Protection** — AWS Web Application Firewall (WAF)
- 📊 **Advanced Monitoring** — Prometheus, Grafana, Datadog
- 🎯 **Cost Optimization** — Reserved Instances, Spot Instances

---

## ✅ Project Achievements

This project successfully demonstrates:

| Feature | Status |
|---------|--------|
| Network Isolation | ✅ |
| Public / Private Subnet Separation | ✅ |
| Load Balancing | ✅ |
| Application Tier Separation | ✅ |
| Managed Database Service | ✅ |
| Least-Privilege Security | ✅ |
| Monitoring & Logging | ✅ |
| Automated Alerts | ✅ |
| Backup Strategy | ✅ |
| High Availability Design | ✅ |

**Practical Experience Gained:**

This project provides hands-on experience with:
- AWS networking (VPC, subnets, security groups)
- Compute services (EC2, load balancing)
- Database services (RDS, managed MySQL)
- Security best practices (least-privilege access)
- Monitoring and observability (CloudWatch)
- Cloud architecture design patterns

---

## 👩‍💻 Author

**Shree Priyanka V L**

📚 **Education:** B.Tech — Computer Science and Business Systems

### Skills & Interests

- ☁️ **AWS** — Cloud architecture and services
- 🔧 **DevOps** — Automation and infrastructure
- 🐳 **Docker** — Containerization
- ☸️ **Kubernetes** — Orchestration
- 🔄 **CI/CD** — Continuous integration and deployment
- 🛡️ **DevSecOps** — Security in DevOps

---

## ⭐ Support This Project

If you found this project useful, please consider:
- ⭐ Starring the repository
- 🍴 Forking for your own use
- 📝 Contributing improvements
- 💬 Sharing feedback

---

**Last Updated:** September 16, 2026

